# Voice guide: how I talk upstream

<!--
THIS IS THE PART YOU WRITE (new this week). Live mode reads this file
before any comment of yours goes out the door; eval mode ignores it
entirely, because your voice is yours and carries no gold labels.

This is not etiquette. "Be polite and concise" is advice for everyone
and therefore rules for no one. Write rules YOU need, in your own
words, each one concrete enough that the skill can hold a draft
against it and say which rule it breaks.

Three sections. Fill all three.
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

## Things I never post

- A promised fix or completion date ("I'll have this fixed by Friday").
- Certainty I haven't verified myself ("this is definitely the bug,"
  "guaranteed reproducible") with no shown artifact behind it.
- A piggyback confirmation ("same as above, can confirm") instead of my
  own environment, steps, and output.
