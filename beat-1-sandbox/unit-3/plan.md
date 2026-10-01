# Plan: issue #72 — `verify_password` raises `UnknownHashError` instead of returning `False`

## Diagnosis

`verify_password` in `core/security.py` calls `pwd_context.verify(plain_password,
hashed_password)` directly and returns its result, with no handling for a
stored hash passlib cannot verify. When the hash is not a recognizable
format, passlib raises `passlib.exc.UnknownHashError`, and that exception
propagates straight out of `verify_password` instead of being caught.

This matches both my Unit 2 reproduction and a fresh probe against the
current code: `verify_password("password", "not_a_valid_bcrypt_hash")` raises
`passlib.exc.UnknownHashError: hash could not be identified`, while the
control call against a real bcrypt hash from `hash_password` returns `True`
normally — the failure is isolated to the malformed-hash path, not a general
regression in verification. The covering test, `test_verify_with_wrong_hash_format`,
is marked `@pytest.mark.xfail(strict=True, reason="issue #72 (manifest H-05): ...")`
and encodes exactly this: a non-bcrypt string should make `verify_password`
return `False`, not raise.

I checked the full exception picture before scoping the fix, since catching
only the one named exception type seemed likely to leave a sibling case
uncaught:

- `""` (empty string), a sha-crypt-shaped string, and the literal `"None"` →
  all raise `passlib.exc.UnknownHashError`
- a bcrypt-*shaped* but truncated string (`"$2b$12$shorttoken"`) → raises a
  plain `ValueError: salt too small (bcrypt requires exactly 22 chars)`
- **`passlib.exc.UnknownHashError` is itself a subclass of the built-in
  `ValueError`** — confirmed directly against the installed passlib in this
  repo's venv (`UnknownHashError.__mro__` is `[UnknownHashError, ValueError,
  Exception, BaseException, object]`). So both failure shapes above are the
  same family at the exception-hierarchy level: passlib raising `ValueError`
  (or its `UnknownHashError` subclass) when it cannot verify the given hash,
  not two unrelated defects.
- To make sure a broad `except ValueError` wouldn't also swallow unrelated
  bugs, I checked a caller-error case too: passing a non-string hash (e.g.
  an `int`) raises `TypeError`, not `ValueError` — that stays uncaught, so a
  real type-contract violation from a caller still surfaces instead of
  silently becoming `False`.

That settles the fix at catching `ValueError` (which covers `UnknownHashError`
as a subclass) around the `pwd_context.verify(...)` call, rather than naming
`UnknownHashError` specifically and leaving the truncated-bcrypt case to
raise.

## Scope

**In scope:** `verify_password` returns `False`, instead of raising, for any
`ValueError` (including its `UnknownHashError` subclass) that passlib raises
while attempting to verify the stored hash.

**Not in scope:**
- `TypeError` from a non-string hash argument — that's a caller contract
  violation, not a malformed-hash case, and should keep propagating.
- Any change to `hash_password`, token handling, or anything else in
  `core/security.py`.
- Any change to how/where hashes are stored or validated elsewhere in the
  codebase.

## Files to touch

- `core/security.py` — wrap the `pwd_context.verify(...)` call in
  `verify_password` in a `try`/`except ValueError: return False`.
- `tests/unit/test_security.py` — remove the `@pytest.mark.xfail` marker from
  `test_verify_with_wrong_hash_format` (per `docs/CONTRIBUTING.md`'s rule that
  fixing a seeded bug includes dropping its xfail marker), and add a second
  regression case for the truncated-bcrypt `ValueError` shape, since the fix
  now covers it too.

## Approach

1. In `verify_password` (`core/security.py`), wrap the existing
   `pwd_context.verify(...)` call in `try`/`except ValueError: return False`.
   No new import is needed: `UnknownHashError` is a `ValueError` subclass, so
   catching the built-in covers both shapes found during diagnosis.
2. Leave the success path (`return bool(pwd_context.verify(...))`) unchanged.
3. In `tests/unit/test_security.py`, delete the `@pytest.mark.xfail(...)`
   decorator above `test_verify_with_wrong_hash_format` so it runs as a normal
   assertion.
4. Add one more case next to it: `verify_password("password", "$2b$12$shorttoken")`
   should return `False` (currently raises `ValueError`), to pin down the
   second exception shape found during diagnosis.

## Test plan

**Before (current `main`, confirms the bug):**

```
$ .venv/Scripts/python -m pytest tests/unit/test_security.py -v
...
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format XFAIL
...
24 passed, 1 xfailed, 1 warning in 8.49s
```

**After (expected once the fix lands):**

```
$ .venv/Scripts/python -m pytest tests/unit/test_security.py -v
...
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED
tests/unit/test_security.py::TestSecurity::test_verify_with_truncated_bcrypt_hash PASSED
...
26 passed in <time>
```

No more `xfailed` entries from this test, no `XPASS(strict)` failure (since
the marker is removed rather than left behind), and the two existing
passing tests exercising the success path
(`test_verify_password_correct`, `test_verify_password_incorrect`) still pass
unchanged, confirming the fix doesn't touch the non-error path. I'll also
re-run `make check` (`lint`, `format`, `typecheck`) locally before opening the
PR, per `docs/CONTRIBUTING.md`'s CI requirements.

## Risks/Unknowns

- **Breadth of the `ValueError` catch.** Catching `ValueError` broadly (rather
  than the more specific `UnknownHashError`) is deliberate here, since it's a
  subclass and the sibling truncated-bcrypt case needs the same handling —
  but it does mean any future passlib `ValueError` variant I haven't seen
  would also fail closed to `False` rather than raise. I checked that a
  caller-side type error (`TypeError` from a non-string hash) is not affected,
  which is the risk I was most concerned about; I haven't exhaustively
  enumerated every `ValueError` passlib's bcrypt backend can raise beyond the
  two shapes in this plan.
- **Shared issue.** Other students may be working this same issue in
  parallel (course house rule: that doesn't block my own claim, repro, plan,
  or PR). I'm not referencing anyone else's comments or findings here; the
  exception-hierarchy check above is my own, run against this repo's venv.

## Deviations

Nothing changed; the plan held. The implementation is exactly the
`try`/`except ValueError: return False` wrap around `pwd_context.verify(...)`
described in Approach, on the one file/one function named in Files to touch,
plus the two test changes (xfail marker removed, truncated-bcrypt regression
case added) described there. `make check` (ruff, black --check, mypy) and the
full `tests/unit/test_security.py` suite pass as predicted in the Test plan.
