# Procedure: how this skill grades a plan package

## Read order

1. Read `## Issue` first: the reported symptom, and nothing else yet. Note the symptom in one sentence — this is what "bounded" and "grounded" get measured against later.
2. Read `## Repo facts`: note the bug-report template's asks and the contribution/AI policy verbatim (especially any line about disclosure for issue comments specifically vs. contributions generally).
3. Read `## Thread highlights`: note whether any OWNER/MEMBER/COLLABORATOR comment already points at a cause, a file, or a preferred approach. If no such comment exists, note "no maintainer direction" explicitly — that is itself the fact `thread-aware-comms` needs.
4. Read `## Repro evidence` in full: every step, every control run, every artifact (output, exit code, stack trace). Note which controls passed and which case failed — this is the fact set `diagnosis-grounded` and `decisive-test-plan` are checked against.
5. Read `## Candidate plan` last, in its own section order (Diagnosis, Scope, Approach/Files, Test plan, Risk). Reading the evidence first and the plan last means the plan is judged against what actually happened, not against what sounds plausible.
6. Read `## Candidate plan comment` after the plan, since it is graded only against the thread and the repo facts, not against the plan's own internal logic.

In live mode, read `scope.md` before any of the above (refuse to grade an out-of-scope issue), and read `voice-guide.md` after grading the comms check, just before reporting.

## Evidence gathering

For each check, pull the named fact and write it down before grading, so the grade step below never re-reads the whole package:

- **diagnosis-grounded**: from step 4's notes, list every control/artifact and whether the Diagnosis (step 5) explains it. One line per control: "explained" or "contradicted/unexplained."
- **bounded-scope**: from step 5, list everything the plan's Approach/Files touches. Mark each item as "addresses the step-1 symptom" or "beyond it." Separately note any item the plan itself calls out as deferred/not-in-scope — that is evidence FOR the check, not against it.
- **executable**: from step 5's Approach/Files, list each stated step. Mark any step that defers a real decision ("whichever is easier," "not sure," no named file) as an executability gap.
- **decisive-test-plan**: from step 5's Test plan, extract the stated expected outcome. Compare it against step 4's repro steps: does the test plan name the same command/scenario with a concrete expected result, or a named regression test tied to the issue?
- **thread-aware-comms**: from step 3's maintainer-direction note and step 2's policy note, check the plan comment (step 6) for (a) explicit engagement with any noted maintainer direction, and (b) an AI-disclosure statement if the policy note requires one for comments (or states no carve-out).

Live mode only: gather the issue-side evidence (repo facts, thread, repro comment) via `gh`/the GitHub API/the web per these same categories, and take the student's own posted repro comment as the repro evidence (or the house repro pack, on a house issue, as quoted in the drafts).

## Check execution

Run the five checks in the rubric's table order: `diagnosis-grounded`, `bounded-scope`, `executable`, `decisive-test-plan`, `thread-aware-comms`. Each check is independent — grade it only from the evidence gathered for it above, never from how other checks graded.

For each check, apply the rubric's pass condition literally to the gathered evidence. Grade `fail` when the gathered evidence contains a concrete contradiction, gap, or unmet condition named in the pass condition. Grade `unclear` only when the package genuinely does not contain enough information to apply the pass condition either way (for example, no repro evidence block exists at all) — not when the evidence is merely thin; thin-but-consistent evidence that meets the pass condition still passes (a terse plan can still be ready).

A check may be graded without re-reading the full package once its evidence line (from "Evidence gathering" above) is written down — grade directly from that note.

## Verdict assembly

Apply the rubric's verdict rule: accept only if all five required checks pass; any fail holds the package at reject; `unclear` counts as fail. There is no partial credit and no preferred-check override this round, since every check is required.

In the output JSON, each check's `"evidence"` field quotes the one fact from "Evidence gathering" that decided it (a specific control run, a specific named-vs-vague step, a specific thread comment or policy line) — never a restatement of the pass condition itself. For the deciding check on a reject verdict, make sure that quoted evidence is the first thing a student would need to see to know what to fix.
