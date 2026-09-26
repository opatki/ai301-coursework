# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

opatki

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5841718435

```
Hi! I'd like to take this on as one of my first open-source contributions. I'll reproduce the malformed-hash failure in verify_password on my own machine and report back with what I find before looking into a fix.
```

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5841806402

```
Environment: Python 3.14.0 (Windows), passlib 1.7.4, bcrypt 4.3.0, python-jose 3.5.0, pydantic-settings 2.15.0, from a fresh venv against this fork's main (.env copied from .env.example, defaults unchanged). I installed only the subset of pyproject.toml's dependencies that core.security/core.config import (not the full Docker/Postgres/Redis stack from SETUP.md), since test_verify_with_wrong_hash_format is marked @pytest.mark.unit ("fast, no external dependencies") and never touches the database.

Steps:

1. python -m venv .venv
2. .venv/Scripts/pip install "pydantic-settings>=2.1.0" "python-jose[cryptography]>=3.3.0" "passlib[bcrypt]>=1.7.4" "bcrypt>=4.0.1,<5.0.0" "pytest>=7.4.0" "fastapi>=0.109.0" "structlog>=24.1.0"
3. cp .env.example .env
4. .venv/Scripts/python -m pytest tests/unit/test_security.py -v
5. Direct call, to see the raw exception:

$ .venv/Scripts/python -c "
from core.security import verify_password, hash_password
good_hash = hash_password('password')
print(verify_password('password', good_hash))
verify_password('password', 'not_a_valid_bcrypt_hash')
"

Output from step 4 (relevant line):
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format XFAIL
...
24 passed, 1 xfailed, 1 warning in 8.49s

Output from step 5:
True
Traceback (most recent call last):
  ...
passlib.exc.UnknownHashError: hash could not be identified

Expected: verify_password("password", "not_a_valid_bcrypt_hash") returns False (per the issue and the covering test's assertion — "should handle gracefully, return False").

Actual: it raises passlib.exc.UnknownHashError: hash could not be identified, matching the issue's exact reported exception. The control call just above it, against a real bcrypt hash from hash_password, returns True normally — the failure is isolated to the malformed-hash path, not a general regression in verify_password.

One unrelated note: passlib 1.7.4 prints a benign (trapped) error reading bcrypt version warning against bcrypt 4.3.0 (a known passlib/bcrypt version-detection mismatch) — this is not related to the bug and doesn't affect the result; the pyproject.toml pin (bcrypt>=4.0.1,<5.0.0) already reflects this.

I only exercised the exact scenario the issue and test_verify_with_wrong_hash_format describe (a non-bcrypt string). A few classmates' comments on this issue mention that other malformed-hash shapes can raise ValueError instead — I haven't verified that myself, so I'm not claiming it here.
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run (20 packages), first draft of `rubric.md` + `references/evidence-guide.md`
   (both authored from scratch this week, since the shipped templates are empty):
   `categories: clear-accept 7/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3
   wrong-target 4/4` — `agreement: 19/20 scored items  (bar: 18/20: PASS)`.
2. Targeted re-grade (`--only pkg-12,pkg-18,pkg-06`) after tightening `steps-reproducible`'s
   wording (pkg-12 was the one disagreement; pkg-18 and pkg-06 as canaries, since they were
   the packages whose reject verdicts depended on that same check's stricter reading):
   `agreement: 3/3 scored items` (partial run, no bar).
3. Full confirming run, revised rubric, before saving:
   `categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3
   wrong-target 4/4` — `agreement: 20/20 scored items  (bar: 18/20: PASS)`.
4. Full run, final, saved with `--save-run`: `categories: clear-accept 8/8  disclosure 1/1
   no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4` — `agreement: 20/20 scored
   items  (bar: 18/20: PASS)` — this is the run recorded in `eval-run.txt`.

**Package analysis**

`pkg-12` (source: `prettier/prettier#19795`, category `clear-accept`). Gold label: `accept`,
noted as "both shapes reproduced on the current release with outputs shown; version delta
stated; next step concrete." My first-draft rubric graded this `reject`, failing
`steps-reproducible`: the candidate's repro report describes running `node repro.mjs`
containing "the issue's two `prettier.format` calls," but never pastes `repro.mjs`'s literal
contents as a code block — only the exact input string, the exact `rangeStart`/`rangeEnd`
numbers, and the exact `parser` option, all in prose. My rubric's evidence read that as an
unshown file a stranger couldn't reconstruct, so it failed the package. That's wrong: every
value needed to write `repro.mjs` yourself is stated precisely (not vaguely described), so a
stranger loses nothing by not seeing the file pasted literally. I revised
`steps-reproducible`'s pass condition to accept precise prose parameters as equivalent to a
pasted file, reserving the fail case for resources that are genuinely withheld (a private
repo, an unshared config) rather than merely described instead of quoted. Re-graded, `pkg-12`
now passes `steps-reproducible` and the package grades `accept`, matching gold.

**Check rationale**

From `rubric.md`:

> | steps-reproducible | The repro report's steps (commands, config, starting state), read
> against what a stranger with only the posted comment would need, and against any
> starting-state detail (a driver flag, a platform) the issue's own repro depends on | A
> stranger reading only the posted comment could reconstruct and run the same steps: every
> input, parameter, or config the steps depend on is either shown verbatim or stated
> precisely enough in prose (exact values, not vague description) that a stranger could
> rebuild it themselves, and no starting-state detail the issue's repro depends on is
> silently dropped or swapped for a default. Fails only when a referenced resource is
> withheld or inaccessible (a private repo, credentials, an unshown application-specific
> config) such that no amount of care lets a stranger reconstruct it | required |

It reads this way because of `pkg-12` above: my first draft required steps to be "shown
inline," which is really a proxy for the thing I actually care about — can a stranger
reconstruct this without me? Prose with exact values satisfies that just as well as a pasted
script. I narrowed the fail condition to genuine unreachability (`pkg-18`'s private
monorepo, where no amount of care lets a stranger rebuild the config) rather than a
literal-inline-code requirement, so the check now measures reconstructability, not
formatting.

**Trade-offs**

The loosened wording gives up some resistance to a report that describes a script
convincingly but gets a value subtly wrong (a typo'd range number, say) — a stranger
reconstructing from prose has to trust the transcription in a way pasted code doesn't
require. I accept that gap, and I checked that it doesn't already cost me anything: I
re-ran `pkg-18` (private monorepo, unfollowable-comms) and `pkg-06` (missing driver context,
unfollowable-comms) as canaries alongside `pkg-12`, and both still correctly reject —
`steps-reproducible` still catches a resource that's genuinely inaccessible, it just no
longer penalizes a resource that's fully specified but not pasted verbatim.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
