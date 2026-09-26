# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: in an eval bundle, the candidate repro report's own
"Environment:" line or section. In live mode, the same line in the
student's draft repro report, read against the issue's own text and the
repo-facts block (release/version, and any platform-specific framing in
the title or body).

What good looks like: names the tool/library version and the OS or
platform actually used. When the issue's own text or the repo-facts
block identifies a variant that changes whether the bug triggers —
a build profile (debug vs. release), a specific driver or backend, a
dependency version, a browser — that variant is named too, not left
implicit. A report with no environment line at all is never sufficient,
even when the artifacts elsewhere look right.

## Steps

Where it lives: in an eval bundle, the candidate repro report's steps
(commands, config, starting state) plus anything it says about where
those files or that setup live. In live mode, the same content in the
draft, read against the issue's own repro recipe (its exact commands,
flags, and any starting-state detail such as a required driver or
config option).

What good looks like: a stranger who has only the posted comment (no
access to the student's machine, private repos, or unshared configs)
could reconstruct and run the same steps. Every file, config, or input
the steps depend on is either shown verbatim, or described in prose
precisely enough (exact input text, exact parameter values, exact
flags) that a stranger could rebuild it themselves without guessing —
a script's contents don't need to be pasted as a code block if the
exact values that would go into it are all stated. This only fails when
something is genuinely withheld or inaccessible: a private repo, an
application-specific config nobody else has, credentials — not merely
"described instead of pasted." A starting-state detail the issue's own
repro depends on (a specific driver flag, a platform, a build profile)
is carried into the steps, not silently dropped or swapped for a
default.

## Behavior shown (faithful target)

Where it lives: in an eval bundle, the candidate repro report's shown
artifact (command output, log excerpt, error text, exit code,
screenshot description), read side by side with the issue's own stated
trigger (its exact syntax/input/version) and its own stated symptom
(what kind of failure it reports — a panic, a specific wrong value, a
hang). In live mode, the same comparison against the live issue's body.

What good looks like: the candidate's actual input matches the issue's
trigger (same syntax, flags, and the same or current version — or, if a
different version was used, the report says so plainly rather than
presenting it as equivalent), AND the shown artifact demonstrates the
SAME class of behavior the issue reports, not a different, adjacent one
dressed up in confident language. A graceful validation error is not a
panic, even when a report calls it one. An honest cannot-reproduce that
used the correct trigger and says plainly that the bug did not show is
a faithful attempt, not a failure of this family — faithfulness is about
testing the right thing, not about succeeding.

## Honesty

Where it lives: in an eval bundle, every claim the candidate report
makes (a stated conclusion, a diagnosis, a word like "confirmed" or
"verified"), read against what the shown artifact in the same report
actually displays. In live mode, the same comparison in the draft.

What good looks like: every claim of an outcome is backed by something
actually shown — an output excerpt, a log line, a concrete observation
— not asserted from confidence or enthusiasm alone. A report claiming
the issue's specific failure occurred needs that failure's own symptom
visibly present in the shown transcript, not just that the tool ran
without crashing, and not just "same as above" agreement with someone
else's claim. A report that states plainly it could not reproduce the
bug, with a real attempt shown, is honest and passes; a report that
asserts certainty, a root cause, or a "guaranteed" result with nothing
shown to back it does not.

## Comms

Where it lives: in an eval bundle, the candidate claim comment's own
wording, plus the repo-facts block's contribution policy and any
stated AI-use policy (and whether that policy asks for disclosure in
issue comments specifically, or only in pull requests). In live mode,
the student's own draft claim/repro comments, `voice-guide.md`, and the
live repo's CONTRIBUTING.md / AI-policy files (`references` in the
Unit 1 evidence guide named the usual locations: CONTRIBUTING.md,
`.github/`, a dedicated AI_POLICY.md).

What good looks like: the claim comment states genuine, specific
intent (what will be checked or tried next) without promising a fix, a
completion date, or exclusive ownership it cannot actually guarantee —
boilerplate like "assign it to me, I'll fix it within 2 days
guaranteed" fails this regardless of how good the repro report is.
When the repo's stated policy requires disclosing AI assistance in
issue comments, at least one posted comment (claim or repro) contains
that disclosure; when the policy is silent, permissive, or scoped only
to pull requests (not issue comments), no disclosure is needed to pass.
