# The gotcha catalog — failure classes that stay green

Dated, measured failure classes from our own fleet. Every one of these passed tests and review
before it bit. Sanitized (internal names generalized), numbers real.

## 1. The substring detector

`"word" in text` and bare-word regexes judge SUBSTRINGS, not structure. Our tally: **5 dated
breakages in 8 days** from this one class, including:

- a two-letter word matched *inside another word* and flipped a private record to `visibility: public`;
- a negation ("not delivered") matched inside a TITLE and silently unpublished a live post;
- `grep -E "dispatch|collect|deliver"` run over JSON where those words are KEYS, not values — an
  hourly job woke an LLM for nothing 7 ticks in a row.

**Rule:** if a verdict GATES something (skip/block/publish/wake), it must parse the structure
(JSON keys vs values, YAML frontmatter stripped, word boundaries). Over plain human text where a
false hit is visible and cheap, grep stays the right tool — don't turn every grep into a parser.

## 2. The unit-mismatched ratio

A percentage whose numerator and denominator count DIFFERENT physical events lies silently.
Ours compared driver process starts against per-click tool calls for 9 days: **1.6% "adoption"
raw vs 7.6% honest** once units matched. Three independent external reviewers missed it — each
checked one rail; nobody checked that the units matched.

**Rule:** before trusting any ratio, write down what ONE row of each side physically means.

## 3. The fallback metric that acts

"Honest measurement unavailable → use a neighboring worse one" is fine for a REPORT and a hole
for an ACTION. Our promise "judge only by the delta" lived in a docstring and 54 green tests;
ONE fallback line to raw `ps %CPU` overrode it — found by an external breaker, not by tests.

**Rule:** for every fallback, look at what sits BELOW it in the flow: `print` is ok;
`kill`/`send`/`write`/`commit` is a hole.

## 4. The default branch nobody tested

We fixed vendor routing and tested the explicit selector on 13 cases — all green. The untested
branch: *no selector given* → silently fell back to one favorite vendor, defeating the whole
"independent second opinion" design. A human caught it by asking "why is it always the same vendor?"

**Rule:** a routing bug always has two doors — "named wrong" and "not named at all". Closing one,
test the other.

## 5. The watchdog that was green during the outage

Our session reaper produced **15 green scheduled runs during a live incident** — it ran fine from
a shell, and its scheduler context was broken in a way it never checked.

**Rule:** watchdog proof has two halves: RED shown on a deliberately broken target (break-case kept
in the self-test), AND a run from the real scheduler with its output read. Either half alone is
theater.

## 6. The rail that answers without working

A vendor CLI returned exit 0 and fluent prose: "the sandbox denied file access, so here are
general thoughts…". Counted as a completed review for weeks.

**Rule:** detect no-verdict answers and convert them to NAMED skips (`rail-broken:<cause>`) that
cap the verdict at ⚠️. Keep the detector narrow — only when no valid verdict tag is present —
or you'll silently discard genuine reviews of your sandbox bug.

## 7. The tests that polluted the production ledger

Unit tests exercised fake engines through the REAL usage logger; their rows sat in the production
ledger for weeks and skewed a subscriptions-vs-fallback utilization measurement.

**Rule:** tests write to their own ledger via an env-var redirect at the source. Fixing the report
with a filter treats the symptom; fix the machine.

## 8. The reviewer who judged your summary

A review panel judging our RETELLING of the code: 25% precision — 2 of 3 "findings" grew out of
the retelling's wording, not the code. Same panel on live code: 10/10.

**Rule:** reviewers get primary sources. Your summary is navigation, never the evidence.
