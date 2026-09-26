# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | The repro report's environment statement, read against any build/driver/dependency-version variant the issue's own text or repo-facts calls out as relevant to whether the bug triggers | Names at least the tool/library version and the OS or platform used. If the issue identifies a variant (debug vs. release build, a specific driver/backend, a dependency version) as relevant to the bug, that variant is also named. Fails if there is no environment statement at all, or a called-out variant is silently missing | required |
| steps-reproducible | The repro report's steps (commands, config, starting state), read against what a stranger with only the posted comment would need, and against any starting-state detail (a driver flag, a platform) the issue's own repro depends on | A stranger reading only the posted comment could reconstruct and run the same steps: every input, parameter, or config the steps depend on is either shown verbatim or stated precisely enough in prose (exact values, not vague description) that a stranger could rebuild it themselves, and no starting-state detail the issue's repro depends on is silently dropped or swapped for a default. Fails only when a referenced resource is withheld or inaccessible (a private repo, credentials, an unshown application-specific config) such that no amount of care lets a stranger reconstruct it | required |
| faithful-target | The candidate's actual command/input/version, compared against the issue's exact stated trigger (syntax, flags, version) and its stated symptom (the kind of failure reported), plus any explicit note of a deviation | Passes when the candidate used the issue's own trigger (same syntax/flags and the same or current version, or an explicitly-acknowledged different version) and the shown artifact demonstrates the SAME class of behavior the issue reports. An honest cannot-reproduce that used the correct trigger passes. Fails when a substituted trigger or version is narrated as confirming the original report without disclosing the substitution, or the shown artifact is a different, adjacent class of outcome (e.g. a graceful validation error presented as the reported panic) | required |
| honest-outcome | Every claim of an outcome in the report (a stated conclusion, a diagnosis, "confirmed"/"verified"), read against what the report's own shown artifact actually displays | Every claim is backed by something actually shown (an output excerpt, a log line, a concrete observation); a report claiming the issue's specific failure needs that failure's own symptom visibly present, not just that the tool ran. A plainly-stated cannot-reproduce with a real attempt shown passes. Fails when an outcome, diagnosis, or certainty is asserted with nothing shown to back it, or a stated "expected"/"actual" contradicts what the shown artifact displays | required |
| comms-conventions | The claim comment's own wording, and the repo-facts contribution/AI-use policy (including whether disclosure is asked for issue comments specifically, or only for pull requests) | The claim comment states genuine, specific intent without promising a fix, a completion date, or guaranteed exclusive ownership. When the repo's stated policy requires disclosing AI assistance in issue comments, at least one posted comment (claim or repro) discloses it; when the policy is silent, permissive, or scoped only to pull requests, no disclosure is needed | required |

## Verdict rule

Accept (ready to post) only if every required check passes:
`env-recorded`, `steps-reproducible`, `faithful-target`,
`honest-outcome`, and `comms-conventions`. There are no preferred checks
in this rubric — every one of the five proof families can independently
sink a package, so none is a nice-to-have. Any check graded `unclear`
counts as a fail: proof that cannot be verified is proof that is not
ready to post. In live mode, a claim-only draft leaves the checks that
need the repro report (`steps-reproducible`, `faithful-target`,
`honest-outcome`) out of the verdict rule per the skill's claim-only
rule, and the verdict then answers only whether `env-recorded` (if
applicable to the claim) and `comms-conventions` pass for the claim
comment itself.
