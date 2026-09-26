# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks
| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| community-alive | The last 5 default-branch commit dates, and the maintainer first-response sample (days to first owner/member/collaborator comment), both in repo-facts | At least one of the last 5 default-branch commits occurred within 90 days of the capture date, OR at least one thread in the maintainer first-response sample got a reply within 30 days of the capture date | required |
| repo-in-use | The releases list and project version history in repo-facts | At least one official release or version update has shipped within the last 180 days | preferred |
| unclaimed | The issue assignees list and linked pull requests section on the issue page | There are zero assignees and zero open pull requests linked to this issue | required |
| scope-fits | The issue body, the full comment thread (age, claim history, closed/abandoned linked PRs), and any maintainer statements about design or internals | Passes unless any of the following hold: (1) the issue itself is a coordinator for other separate issues/PRs (it lists other issue numbers as the individual work items to pick from), or a maintainer frames the work as open-ended/incremental with no stated completion boundary; (2) the issue or thread flags part of the implementation as undecided (e.g. "TBD", "possibly", "not yet decided") and no comment resolves it; (3) a maintainer states the fix requires changes to core internals; (4) the request is a usage/support question, not a change to make; (5) the repo-facts linked-PRs list shows 2 or more closed (unmerged) prior attempts at this issue. Brevity, a missing file list, or missing reproduction steps do NOT by themselves fail this check, especially when the issue is maintainer/collaborator-filed or labeled "good first issue" | required |
| ai-policy-ok | The "contribution policy" line under repo-facts (or CONTRIBUTING.md / a dedicated AI-policy file in live mode) | The stated policy does not contain an outright ban on AI-generated code or documentation. Conditions (disclosure, required human review, required testing/understanding) are not bans and pass. Silence / no stated policy passes. An AGENTS.md file is a positive signal, not a ban | required |

## Verdict rule

Accept if every required check (`community-alive`, `unclaimed`, `scope-fits`, `ai-policy-ok`) passes. The preferred check (`repo-in-use`) never changes the verdict and is used only to rank accepted issues. Any check resulting in `unclear` counts as a fail.
