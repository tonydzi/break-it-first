---
name: tt
description: >-
  Post-build quality gate: immediately after building or changing anything executable (a skill,
  script, routine, hook, pipeline), prove it works BEFORE saying "done" — run live on real data,
  break it on purpose, check the visibility layer, root-cause failures, verdict with evidence.
  Trigger on "/tt", "/test", "prove it works", "test what we built".
---

# /tt — the quality gate right after the build

This is **not** a retrospective. Retro packs the whole session at its close; /tt is a narrow check
of **the one thing just built**, while context is hot. The pain it kills: "built it, said done,
moved on — and it was two-thirds done."

**When:** right after building/changing a skill · script · routine · hook · pipeline · rule-note —
anything that is supposed to DO something. Not for conversational answers or trivial text edits.

## Step 0 — Recall the scope

List what this task actually created/touched — from facts (git status, recent files), not memory.
Then recall what is already KNOWN about this area (your notes, your rules file, parallel work) —
a parallel session may have already done or documented it.

## Step 1 — Run it live (real data, not theory)

- a skill/procedure → execute it here, now;
- a script → run it (read-only/dry-run if it has side effects);
- a hook/routine → trigger it for real, or prove it fires — not "it should";
- a rule-note → resolve every link, validate the frontmatter.

"Theoretically works" is not evidence. You need actual output in the transcript.

## Step 2 — Break it on purpose (negative + edge)

- empty / malformed input; missing dependency or key;
- path assumptions (wrong drive, wrong user, hardcoded machine) — shared code must resolve paths
  through one ladder, or it dies silently on the next machine;
- stale-source traps: does it read the LIVE config/state, or a copy that stopped updating?
- does it degrade LOUDLY (clear error) or silently pretend it worked?
- **watchdogs/gates need double proof:** (1) shown RED on a deliberately broken target, with the
  break-case kept in a self-test; (2) run from the real scheduler context (cron / task scheduler)
  with its OUTPUT read — a watchdog can be green from your shell and dead in its scheduler.
- **routing/choice changes:** provoke the DEFAULT branch too, not just the explicit-error branch.
  Call it with no selector and prove the default is what's claimed, not a silent first-vendor.
- **detectors:** if the change touches a gate/filter/precheck, check it judges STRUCTURE, not a
  substring (a word inside another word, a grep hitting a JSON key instead of a value).
- **ratios/percentages:** write down what ONE row of the numerator and ONE row of the denominator
  physically mean. Different units (hook event vs process start vs session) = a ratio that lies.
- **fallback metrics:** a degraded measurement may be REPORTED, never ACTED on (kill/send/write).

## Step 2.5 — Second opinion: adversarial panel

Send the changed artifact (the PRIMARY SOURCE — live code, not your summary of it) to rival
model families with an adversarial contract: hunt for defects, forced verdict ACCEPT/COUNTER/BLOCK.
Rules that keep it honest: no silent vendor substitution (unknown engine = explicit skip);
no cheap flash/lite tiers as judges; a rail returning fluent "I could not read the file" prose is
a NAMED skip, not an opinion; panel findings are hypotheses — verify each against the source.
Any explicit skip caps the final verdict at ⚠️.

## Step 3 — Visibility layer

Can anyone SEE that it worked — a counter, a log line, an artifact with a fresh timestamp inside?
Silent success is indistinguishable from silent failure. New durable parts are born WITH a test
and an in-file docstring (purpose · inputs/outputs · who calls it · updated date); editing the
code edits the docstring in the same commit.

## Step 4 — Root cause, fix, re-run

Anything that broke in Steps 1-3: find the root (not the symptom), fix, re-run the failed step.
The test for symptom-patching: remove the fix — does the symptom return?

## Step 5 — Verdict with evidence

- ✅ works — with the live output / counter that proves it;
- ⚠️ works with named caveats (skipped check, missing test, degraded rail) — list them;
- ❌ broken — say so plainly, with the failing output.

Only ✅ = "done". A claimed-done without proof is flagged, not celebrated. If the fix closes a
CLASS of problem others likely hit, share it where sufferers are searching (issue threads), with
your measurements.
