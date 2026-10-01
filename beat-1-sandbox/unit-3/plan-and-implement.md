# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

opatki

**Plan comment**

[TODO: paste the permalink to the posted comment, then the exact comment text underneath, once `comment.md` is posted to https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72]

---

## Your branch

**Branch**

fix/72-verify-password-malformed-hash

**Evidence**

Before (current `main`, re-run of the Unit 2 reproduction command):

```
$ .venv/Scripts/python -m pytest tests/unit/test_security.py -v
...
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format XFAIL
...
24 passed, 1 xfailed, 1 warning in 8.49s
```

After (on `fix/72-verify-password-malformed-hash`, with the fix applied):

```
$ .venv/Scripts/python -m pytest tests/unit/test_security.py -v
...
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [ 84%]
tests/unit/test_security.py::TestSecurity::test_verify_with_truncated_bcrypt_hash PASSED [ 88%]
...
26 passed, 1 warning in 12.04s
```

No `xfailed` entries and no `XPASS(strict)` failure, since the marker was removed as part of the
fix. The new `test_verify_with_truncated_bcrypt_hash` case covers a second exception shape found
while diagnosing the issue: `UnknownHashError` is itself a `ValueError` subclass, and a
bcrypt-shaped-but-truncated hash raises a plain `ValueError` too, so both are now handled by the
same `except ValueError` clause. Local CI checks also pass on the changed files:

```
$ .venv/Scripts/python -m ruff check core/security.py tests/unit/test_security.py
All checks passed!

$ .venv/Scripts/python -m black --check core/security.py tests/unit/test_security.py
All done! 2 files would be left unchanged.

$ .venv/Scripts/python -m mypy core/security.py
Success: no issues found in 1 source file
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run (`--limit 3`, packages pkg-01/02/03), first draft of `rubric.md` +
   `references/evidence-guide.md` + `procedure.md` (all authored from scratch this week, since
   the shipped templates are empty): `agreement: 3/3 scored items` (partial run, no bar).
2. Full run, first draft rubric: `categories: clear-accept 5/7  scope-creep 4/4
   thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4` — `agreement: 18/20 scored items
   (bar: 18/20: PASS)`. Two disagreements, both in `clear-accept`: pkg-13 and pkg-14, both
   failing my `executable` check.
3. Targeted re-grade (`--only pkg-14,pkg-13,pkg-10,pkg-17,pkg-18`) after loosening
   `executable`'s pass condition (pkg-14 was the disagreement driving the change; pkg-13 as a
   same-category canary, pkg-10/17/18 as `unbuildable`-category canaries, since that's the other
   category the loosened wording could flip): `agreement: 5/5 scored items` (partial run, no
   bar).
4. Full confirming run, revised rubric, saved with `--save-run`: `categories: clear-accept 7/7
   scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4` — `agreement: 20/20
   scored items  (bar: 18/20: PASS)` — this is the run recorded in `eval-run.txt`.

**Package analysis**

`pkg-14` (source: `zellij-org/zellij#5174`, category `clear-accept`). Gold label: `accept`,
noted as "honestly scoped-down: reattach handshake fix with a regression-window repro; defers
the untestable Windows variant and says so; arguable on the deferral, ready as scoped." My
first-draft rubric graded this `reject`, failing `executable`: the plan names the general
location precisely (the client attach/reattach path in `zellij-server`'s client connection
handling, and `zellij-client`'s terminal query issuance) and a fully decided mechanism (consume
pending OSC query responses before pane input is wired), but says "exact functions to be pinned
in the PR after tracing the query issuance with debug logs, which I have working." My rubric's
pass condition read any build-time decision as a fail, the same way it correctly fails pkg-18's
"upstream or vendored, whichever is easier." That's wrong: pkg-14 has already decided *what* to
do and *where*, down to the module; only the exact function name is deferred, and the plan
states a working method for finding it. pkg-18 defers the approach itself (which of several
fixes to apply, in which codebase). I revised `executable`'s pass condition to separate
"approach undecided" (fail) from "location and mechanism decided, exact line pinned down during
implementation via a stated method" (pass). Re-graded, `pkg-14` now passes `executable` and the
package grades `accept`, matching gold.

**Check rationale**

From `rubric.md`:

> | executable | The plan's Approach and Files-to-touch, read for whether a stranger could start
> work without asking the author anything | The plan names a concrete location (file, module, or
> subsystem) and a decided mechanism for the fix — what will change and how. A stranger could
> open that location and start applying the mechanism. This still passes when the exact
> function/line is left to be pinned down during implementation, as long as the location and
> mechanism are both decided and the plan states how the exact spot gets found (e.g., "traced
> with debug logs, which I have working"). Fails when the approach itself is undecided — which
> of several strategies to take, which layer or fork to fix, a choice deferred to "whichever is
> easier" or "not sure yet" — or when no location/mechanism is named at all | required |

It reads this way because of `pkg-14` above: my first draft conflated "a decision is deferred to
build time" with "the approach is undecided," which are not the same thing. A plan can commit
fully to what will change and where, and still leave the literal function name for
implementation, the same way a human contributor would say "I'll pin the exact call site once I
trace it" without that being a gap in the plan. I narrowed the fail condition to the approach
itself being open (pkg-17's "not sure which layer," pkg-18's "whichever is easier") rather than
any unresolved identifier, so the check now measures whether a stranger knows *what to do and
where*, not whether every symbol name is already known.

**Trade-offs**

The loosened wording gives up some resistance to a plan that hides real uncertainty behind
confident-sounding "I'll pin this down" language without an actual stated method. I accept that
gap, and I checked that it doesn't already cost me anything: I re-ran `pkg-10` ("profile and
optimize" with no chosen approach), `pkg-17` ("investigate the input stack... not sure which
layer"), and `pkg-18` ("upstream or vendored, whichever is easier") as canaries alongside
`pkg-14`, and all three still correctly reject on `executable` — the check still catches an
approach that's genuinely undecided, it just no longer penalizes a plan that has decided the
approach and location but defers the exact line, backed by a concrete way of finding it.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
