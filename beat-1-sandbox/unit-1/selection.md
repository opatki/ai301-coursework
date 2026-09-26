# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72

**Verdict output**

[Your skill's live-mode output for this issue, pasted verbatim and ending with the
fenced JSON verdict block. A summary does not satisfy this field.]

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

```
## Scope check

Repo matches `codepath/pathreview-ai301-fa26-s1` — in scope. House rule applied:
classmates' claim comments (5 on #72) are ignored per Path Review rules; a shared
issue costs nobody anything.

## Ranked read-out

**1. #72 — `verify_password` raises `UnknownHashError` on malformed stored hashes** (accept)
Best fit: touches core auth/security logic — the closest of the three candidates to
"system tooling," smallest estimated effort (1-2h), and the bug is exceptionally
well-verified (5 independent students reproduced the same failure across
macOS/Windows/Python 3.11-3.12 with no contradicting evidence), so there's very
little risk of hidden surprises once you start.

**2. #54 — Resume section detection fails on text with leading whitespace** (accept)
Cleanest and completely uncontested (0 comments), with an exact repro snippet and
expected-vs-actual output — matches the "clear reproduction steps" preference well,
though it's more isolated regex/text logic than "system tooling."

**3. #69 — Output parser crashes on a top-level JSON array fallback** (accept)
Solid RAG-pipeline bug, but jacho15 already posted a full root-cause analysis and fix
plan in the thread — still open per the house rule, but it leaves less independent
debugging for you to do.

All three passed every required check: `community-alive` (last commit 9 days ago),
`unclaimed` (no assignees, and the only open PR in the repo references a different
issue), `scope-fits` (each names its file(s), effort estimate, and the specific
failing test), and `ai-policy-ok` (no CONTRIBUTING.md, so no stated restriction). All
three also failed the preferred `repo-in-use` check (no releases published yet) —
expected for a two-week-old course repo, and it doesn't affect any verdict.

{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72",
  "checks": [
    {"name": "community-alive", "grade": "pass", "evidence": "Most recent default-branch commit 2026-09-16, 9 days before today (2026-09-25) — within 90 days."},
    {"name": "repo-in-use", "grade": "fail", "evidence": "Releases list is empty; no release has ever been published."},
    {"name": "unclaimed", "grade": "pass", "evidence": "Assignees: none. Only open PR in the repo (#74) references issue #60, not #72. Per the Path Review house rule, the 5 students' claim comments do not count as a claim."},
    {"name": "scope-fits", "grade": "pass", "evidence": "Names exactly 2 files (core/security.py, tests/unit/test_security.py), a specific xfail'd test to un-mark, and an estimated effort of 1-2 hours; no umbrella, unresolved decision, core-internals statement, or usage question."},
    {"name": "ai-policy-ok", "grade": "pass", "evidence": "CONTRIBUTING.md returns 404 (no stated policy); silence passes."}
  ],
  "verdict": "accept"
}
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

1. Full run (20 issues), initial rubric (`maintainer-alive` + `responds-to-issues` as
   two separate required checks, `scope-fits` weighted `preferred`, no AI-policy
   check): `agreement: 15/20 scored items  (bar: 18/20: below the bar; category floor
   unmet: no match in policy)`
2. Targeted re-grade (`--only issue-06,issue-12,issue-14,issue-15,issue-20`) after
   revising the rubric: `agreement: 5/5 scored items` (partial run — 5 of the 5
   previous disagreements now agree; partial runs report no bar).
3. Full run (20 issues), revised rubric, verification pass before saving:
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`
4. Full run (20 issues), revised rubric, final run saved with `--save-run`:
   `agreement: 20/20 scored items  (bar: 18/20: PASS)` — this is the run recorded in
   `eval-run.txt`.

**Issue analysis**

`issue-12` (source: `bookwyrm-social/bookwyrm#1133`, category `policy`). Gold label:
`reject`, noted as "passes every liveness, scope, and claim check; the repo's
contributing docs ban AI-generated code and documentation outright." My initial
rubric had no check anywhere covering the AI-contribution-policy family, so it graded
this issue `accept` — it passed liveness, scope, and claim checks and there was
nothing in the rubric to catch the ban. That single missing family was also why the
whole `policy` category matched 0/1 on the baseline run. I added a required
`ai-policy-ok` check that reads the "contribution policy" line in repo-facts and
fails only on an outright ban on AI-generated code/documentation (conditions like
required disclosure or human review still pass, and silence passes). With that check
in place, `issue-12` now grades `reject` — it fails `ai-policy-ok` on the bundle's
quoted line, "We do not accept AI-generated code or documentation" — matching gold.

**Check rationale**

From `rubric.md`:

> | ai-policy-ok | The "contribution policy" line under repo-facts (or CONTRIBUTING.md
> / a dedicated AI-policy file in live mode) | The stated policy does not contain an
> outright ban on AI-generated code or documentation. Conditions (disclosure,
> required human review, required testing/understanding) are not bans and pass.
> Silence / no stated policy passes. An AGENTS.md file is a positive signal, not a
> ban | required |

I wrote it this way because the lecture's four families (maintainer alive, repo in
use, scope fits, unclaimed) don't cover a fifth real failure mode the evidence guide
calls out: a repo whose contribution policy rejects AI-assisted work outright, which
is exactly how this course's contributions are made. Making it `required` (not
`preferred`) was deliberate — `issue-12` passes every other family, so a `preferred`
weight would never have rejected it. The pass condition is narrowed to outright bans
specifically, because most policies I saw across the 20 bundles (`issue-08`,
`issue-10`, `issue-15`) state conditions (disclosure, testing, human review) rather
than bans, and those are terms to follow, not reasons to reject a first issue.

**Trade-offs**

The check only catches a *stated* ban — it reads whatever the repo-facts "contribution
policy" line (or CONTRIBUTING.md) says, and silence passes by design. A repo that
has never written down a policy but informally closes unreviewed AI-assisted PRs
would still pass `ai-policy-ok`, because there's no written evidence to grade against.
I accept that gap: the alternative (treating silence as a fail) would have rejected
`issue-04`, `issue-06`, `issue-11`, `issue-14`, `issue-16`, `issue-18`, `issue-19`, and
`issue-20` — every one of which is gold `accept` and has "no statement on AI or
contribution tooling" in its bundle. Nothing else in the rubric changed to compensate
for this gap; it's a deliberate limit of what a text-based check can verify.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. **Fit to interests and time available.** I want to sharpen full-stack debugging
   and get more comfortable navigating codebases I didn't write, and `#72` is a tight,
   self-contained exception-handling bug in an auth module (`core/security.py`) with
   a 1-2 hour estimate — small enough to finish without a big time commitment, but
   real system-level code (password verification), not a docs or config tweak.
2. **What the verdict got right, and what I weighed that the rubric couldn't.** The
   rubric correctly confirmed the mechanical facts: the repo is active (commit 9 days
   old), nobody is formally assigned or has an open linked PR, the fix is scoped to
   two named files with a named failing test, and there's no AI-contribution
   restriction to worry about. What the rubric can't weigh is the *quality* of the
   five students' claim comments on the thread — I read past the checks and noticed
   their independent reproductions agree with each other (same `UnknownHashError`/
   `ValueError` behavior across three OS/Python combinations), which told me the bug
   is real and well-understood before I even open the code, beyond what "unclaimed"
   or "scope-fits" can capture.
3. **Anticipated difficulty in claiming it.** Low technically — the fix is a narrow
   exception-handling change (fail closed instead of letting `UnknownHashError`
   escape) with an existing `xfail` test that tells me exactly when I'm done. The main
   practical wrinkle is social, not technical: five classmates have already announced
   intent on the same issue. Per the Path Review house rule that costs nobody
   anything, since course credit attaches to opening a PR, not to merging first — but
   it does mean I should claim and start promptly rather than assume the issue is
   idle.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
