# Voice guide: how I talk upstream

<!--
THIS IS A CARRY-OVER SLOT, not a new hole. You wrote this guide in
week 2; paste your filled week-2 voice-guide.md here, whole. It is not
re-authored and it is not graded as new work this week.

Then reread it with the plan comment in mind. Your claim and repro
comments promised and reported; a plan comment commits you to an
approach in front of the people who maintain the code. If your rules
do not cover that register (for example: how you state an approach you
are not certain of, or how you respond when a maintainer already
suggested a direction), extend the guide with what it needs. Extending
is allowed and encouraged; starting over is not required.

Live mode reads this file before your plan comment goes out and
reports any rule your draft breaks. Eval mode ignores it entirely,
because your voice is yours and carries no gold labels.
-->

## Who I am in threads

I'm a software/IoT engineer making my first open-source contributions
this term as part of a course. I have solid Python/JavaScript and cloud
experience (AWS Certified Cloud Practitioner and Developer Associate)
but am new to this specific codebase, so expect careful, incremental
comments rather than confident final answers — I report exactly what I
tested and what I found, and say plainly when I'm not sure yet.

## Rules I write by

### Rule: promise investigation, not a fix

I never commit to a fix or a timeline in a claim comment — only to
investigating and reporting back. I don't know how deep an issue goes
until I've actually looked.

- Wrong: "I'll have this fixed within 2 days, guaranteed!"
- Right: "I'll investigate the root cause and report back with what I find."

### Rule: state only what the artifact shows

A claim of certainty needs a transcript behind it. If I haven't traced
something all the way, I say so instead of dressing up a guess as a
finding.

- Wrong: "This is definitely caused by a race condition in the debounce logic."
- Right: "The log shows the write happening before the flush at line 42; I haven't traced the root cause past that yet."

### Rule: always produce my own evidence, even on a shared issue

Other people's claims on a shared issue don't excuse me from running
my own reproduction. My proof is my work, from my environment.

- Wrong: "Same as above, can confirm this happens for me too."
- Right: "Reproduced independently on Python 3.12 / Ubuntu 22.04: [steps and output]."

### Rule: name my environment and version, every time

An artifact without an environment line is unverifiable by anyone else
reading the thread, including me a week later.

- Wrong: "I ran this and got the same crash."
- Right: "Environment: v2.3.1, Ubuntu 22.04, Python 3.12. Steps: ..."

### Rule: disclose AI assistance when a repo's policy asks for it

If a repo's CONTRIBUTING.md or AI policy asks issue comments to
disclose AI use, I say so plainly rather than leaving it out because
it's inconvenient.

- Wrong: (staying silent about AI assistance on a repo that requires disclosure)
- Right: "This investigation was AI-assisted (Claude Code helped draft the reproduction steps); I reviewed and independently verified every claim above before posting."

### Rule: state a plan as a proposal, not a done deal

A plan comment commits me to an approach before any code is written. I
say what I intend to do and why, but I don't write as if the approach
is already validated by the maintainers or guaranteed to work.

- Wrong: "This will fix the issue. Implementing now."
- Right: "My plan is to [approach], based on [evidence]. Flagging the approach here before I start, in case it conflicts with something I'm missing."

### Rule: name unknowns and risks explicitly, don't bury them in confidence

A plan always carries things I haven't verified yet (edge cases, a
dependency's behavior, whether a maintainer prefers a different
approach). I say what those are instead of writing the plan as if
nothing could go wrong.

- Wrong: "This change is safe and won't affect anything else."
- Right: "Risk: I haven't checked whether other callers of this function rely on the current (buggy) return value — I'll verify that before merging."

### Rule: if a maintainer already pointed at a direction, say how my plan relates to it

When the thread already has a maintainer comment suggesting an
approach, my plan comment says explicitly whether I'm following it,
and if I'm deviating, why.

- Wrong: (posting a plan that silently ignores a maintainer's prior suggestion)
- Right: "Following @maintainer's suggestion above to handle this in the validator rather than the caller. Plan below."

## Things I never post

- A promised fix or completion date ("I'll have this fixed by Friday").
- Certainty I haven't verified myself ("this is definitely the bug,"
  "guaranteed reproducible") with no shown artifact behind it.
- A piggyback confirmation ("same as above, can confirm") instead of my
  own environment, steps, and output.
- A plan written as already-approved or risk-free when I haven't had a
  maintainer confirm the approach or verified the edge cases myself.
