# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**Where it lives:** In an eval bundle, the plan's `Diagnosis` subsection (under `## Candidate plan`) states the cause; the `## Repro evidence` block holds the steps, control runs, and artifacts (command output, stack traces, exit codes) that cause must explain. Live, the equivalent is the plan's own diagnosis statement in `plan.md` read against the reproduction evidence posted in the student's own repro comment on the issue thread (or the house repro pack, for a house issue).

**What good looks like:** Every control run and artifact in the repro evidence is consistent with the stated cause — a control that passed is one the cause predicts should pass (it's outside the triggering condition named by the cause), and the control or case that failed is the one the cause predicts should fail. A diagnosis that never mentions a control the repro evidence shows, or that a control actively rules out, is not grounded even if it sounds plausible. Calibration trap to watch for: a diagnosis that matches what the *thread* already believes (a maintainer's or reporter's guess) is not automatically grounded — check it against the repro evidence's own steps, not against the thread's prior consensus, since the two can diverge.

## Scope

**Where it lives:** The plan's `Scope` (or the scope language folded into the `Change`/`Diagnosis` paragraph) and `Approach`/`Files` sections, read against the issue's reported symptom in `## Issue`.

**What good looks like:** One bounded change that addresses the reported symptom and nothing structurally larger. A plan may legitimately narrow its own scope (gate a fix to one platform, defer a related rework) — that is honest scoping, not a failure, as long as the deferred item is named and excluded rather than included. A plan fails this the moment work beyond the issue's symptom (a refactor, a migration, a new option, a cross-cutting redesign, "also check other X while I'm here") is folded into the change itself, not merely mentioned as a future idea. Distinguish "I will also fix three other things" (scope creep) from "I am explicitly not fixing three other things, here's why" (bounded).

## Executability

**Where it lives:** The plan's `Approach`/`Change` and `Files` sections.

**What good looks like:** A specific location (file, module, or subsystem) and a decided mechanism (what changes, and how) rather than an activity (investigate, look into, figure out). A stranger reading only the plan could start work immediately: open the named location and start applying the named mechanism. Leaving the exact function or line to be pinned down during implementation still passes, as long as the location and mechanism are both already decided and the plan says how the exact spot will be found (a debugging method already working, not a vague "I'll figure it out") — that is a pinning detail, not an open decision. A plan fails this when the approach itself — which of several strategies to take, which layer or fork to change — is left open ("upstream or vendored, whichever is easier," "not sure which layer yet," "somewhere around X") or when no location or mechanism is named at all — "profile and optimize" or "poke around the code" are not approaches, even if a file is mentioned in passing.

## Test plan

**Where it lives:** The plan's `Test plan` section, read against the `## Repro evidence` block's steps and artifacts.

**What good looks like:** The test plan says exactly what will be run and what result confirms the fix — re-running the repro's own command/steps with a named expected outcome (an exit code, specific output, a passing assertion), or a new regression test tied to the issue's scenario. A decisive test plan could tell someone else, before the fix is built, exactly what evidence would confirm it worked. A vague test plan ("run the full test suite," "should feel fast," "nothing else should feel broken") names no outcome specific to the fix and fails this check even when the rest of the plan is strong — a solid plan with an outcome-free test plan is still not ready.

## Honesty

**Where it lives:** The plan's stated risks/unknowns (often a `Risk:` line) and, for live-mode re-checks, the `## Deviations` section of a re-posted `plan.md`.

**What good looks like:** Genuine unknowns are named as unknowns ("I have not yet measured X; if it shows up I will Y"), not dressed up as settled facts. This guide's checks don't score honesty as its own required row this round, but a plan that states risk honestly tends to also pass executability and scope cleanly, since vague confidence is usually where those checks catch a plan. In live mode, after a build deviates from the posted plan, a deviation note that says plainly what changed and why ("nothing changed; the plan held" counts) is what `plan-check` re-grades; a deviation visible only in the diff, never written down, is not honest work.

## Comms

**Where it lives:** The candidate plan comment (`## Candidate plan comment`), read against two sources: the `## Thread highlights` block (maintainer comments already on the issue) and the `## Repo facts` block's stated bug-report template and contribution/AI policy.

**What good looks like:** Two independent things, both must hold. First, if `Thread highlights` contains a maintainer (OWNER/MEMBER/COLLABORATOR) comment that already points at a cause, a file, or a preferred approach, the plan comment says explicitly how it relates to that direction — follows it, or states a reason for diverging. Silently proposing something unrelated (a docs workaround when the maintainer pointed at and patched the actual code) fails this half even when the diagnosis itself is accurate. Second, if `Repo facts`' contribution/AI policy requires disclosing AI assistance (whether stated for contributions generally or specifically for issue comments, with no carve-out that excludes comments), the plan comment must disclose it in some form ("per the repo's AI policy, this will be AI-assisted..."). A policy that explicitly states no disclosure is required for issue comments (only for code/PRs) means this half does not apply. In eval mode, every candidate plan comment is treated as AI-assisted work, since no package ever states the opposite — so the disclosure half is graded against the policy alone, never against whether the text happens to mention AI first.
