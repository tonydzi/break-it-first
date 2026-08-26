# break-it-first

**A post-build quality gate for agent work: run it live, break it on purpose, get an adversarial second opinion from rival models, and only then say "done" — because "built" and "works" are different claims.**

## The pain, in your words

- *"The agent said done. It was two-thirds done."*
- *"Tests were green, and the feature was still broken — the tests never attacked it."*
- *"I asked another LLM to review and it agreed with everything. Useless."*
- *"The watchdog was green for two weeks while the thing it watched was dead."*

## What this is

Two mechanisms we run on every substantive build in our lab, extracted after they survived months of production:

1. **The /tt ritual** — a 6-step gate that fires immediately after building anything executable, while context is hot ([TT-SKILL.md](TT-SKILL.md), drop-in for Claude Code).
2. **The breaker panel** — an adversarial multi-vendor review: rival models are prompted to BREAK the work, not to praise it, with hard rules that keep the second opinion honest ([the rules below](#the-breaker-panel-honest-second-opinions)).

Both are plain procedure + prompts. No server, no framework.

## The ritual (6 steps)

1. **Scope recall** — list what THIS task actually touched (from git and the session, not from memory).
2. **Run it live** on real data. "Theoretically works" is not evidence; you need actual output.
3. **Break it on purpose** — empty input, missing dependency, wrong-drive paths, stale config; does it degrade loudly or silently pretend? Our measured catalog of the failure classes that slip through green tests: [GOTCHAS.md](GOTCHAS.md).
4. **Visibility layer** — can you SEE that it worked (counters, logs)? This is what catches *silent* success and *silent* failure. A watchdog must be proven two ways: shown RED on a deliberately broken target, and run from its real scheduler context — our session reaper once produced 15 green scheduled runs during a live outage, because nobody had checked the second half.
5. **Root-cause anything that broke**, fix, re-run. Patch-the-symptom is banned; the test is "remove the fix — does the symptom return?"
6. **Verdict with proof**: ✅ / ⚠️ / ❌. Only ✅ with evidence counts as "done". An explicit skipped check caps the verdict at ⚠️ — no silent skips.

## The breaker panel (honest second opinions)

Rules that took us three dated failures each to learn:

- **Adversarial contract.** The reviewer's system prompt demands defect-hunting with a forced verdict tag (ACCEPT / COUNTER / BLOCK) — agreement must be earned, not defaulted. And keep a separate **generative door** (no adversarial contract) for tasks that ASK for generation: when we forgot, 4 of 5 models *reviewed* a naming task instead of doing it.
- **Feed primary sources, not your summary.** Measured: a panel judging our RETELLING of the code ran at 25% precision (2 of 3 "findings" grew out of the retelling's wording); the same panel on the live code went 10/10. Panel findings are hypotheses — verify each against the source and report both columns (accepted / refuted with evidence).
- **No silent vendor substitution.** An unknown engine is an explicit SKIP, never a silent fallback to the default vendor. "Two rails" where one rail is quietly answering twice = fake independence. Same trap one level down: cheap flash/lite model tiers gave us confident-but-shallow agreement three dated times — we ban them from panel duty and run fallback rails at full reasoning.
- **A rail can answer without working.** A vendor CLI can return exit 0 and fluent prose saying "sandbox denied, could not read the file". That is not an opinion — detect it and convert to a NAMED skip (`rail-broken:sandbox`), which caps the ritual verdict at ⚠️.
- **Subscriptions first, metered API as fallback.** Route panel calls through the flat-rate subscriptions you already pay for (Codex / Grok / Gemini CLIs in our case); a metered aggregator (OpenRouter) joins only when fewer than two independent model families answered. Log WHICH rail answered (`via: panel` vs `via: panel-fallback`) so utilization is measurable, not vibes.
- **Tests never write to the production ledger.** Our unit tests' fake engines polluted the real usage ledger for weeks and skewed the subscriptions-vs-fallback measurement; the fix is an env-var redirect at the source, not a filter in the report.

## Self-diagnosis in 30 seconds

Take the last thing your agent declared "done". Ask: *what actual output proved it, and who tried to break it?* If the answer is "the agent's own description" — this repo is for you.

## Order of operations

**BUILD → /tt (per artifact, hot context) → … → session end → retro.** The retro ([claw-retro](https://github.com/tonydzi/claw-retro)) only audits the "tested? ✅/❌" line — testing at session close is too late and too cold.

## Design choices

- Markdown procedure + prompts. Zero dependencies, no framework to adopt.
- Fail-closed verdicts: skipped check ⇒ at most ⚠️; unverifiable claim ⇒ marked 🤔 in the report, never asserted.
- Vendor-agnostic: the panel rules assume nothing beyond "you can reach more than one model family".

## FAQ

**Isn't this just code review by LLM?** No — the contract is adversarial with a forced verdict, the inputs are primary sources, and the independence rules (no silent substitution, no cheap-tier judges, named skips) are the actual product. Review without them converges to agreeable noise.

**Why break it on purpose if tests are green?** Because green tests attack what the author thought of. [GOTCHAS.md](GOTCHAS.md) is our dated catalog of classes that stayed green while broken — substring detectors, unit-mismatched ratios, fallback metrics that act, default-branch routing.

**Does the panel slow things down?** One command, all rails in parallel, 300s budget. The alternative was shipping at two-thirds done.

## Attribution & license

Invented by **Mycroft** (synthetic cofounder) & **Tony** — [Palo Alto AI Research Lab](https://github.com/tonydzi). MIT license.

Siblings: [claw-retro](https://github.com/tonydzi/claw-retro) (session-close ritual that audits this gate's verdicts) · [compact-canon](https://github.com/tonydzi/compact-canon) (measured context-compaction format) · [always-loaded-diet](https://github.com/tonydzi/always-loaded-diet) (keeping agent memory files lean).

We hand free working seeds of our lab tooling to engineer-testers — WhatsApp **+1 (341) 222-9178**.

---

<!--ecosystem-map:start-->

## 🧩 One piece of a working system

This repository is one piece lifted out of a live operation: one non-technical founder, an AI
cofounder, and a fleet of machines that reach consensus with each other and wake the human only
for money or the irreversible. It was extracted after it survived production, not written as a
demo — and it runs on its own: nothing here phones home to the rest.

**See how the whole thing fits together → [SYSTEM.md](https://github.com/tonydzi/tonydzi/blob/main/SYSTEM.md)**

Its closest neighbours in the **gates** layer: [`verbatim-citation-gate`](https://github.com/tonydzi/verbatim-citation-gate) · [`verdict-contract`](https://github.com/tonydzi/verdict-contract) · [`claim-check`](https://github.com/tonydzi/claim-check)

<!--ecosystem-map:end-->

## AI contributors

This project is built by a human + AI team, and the git log says so: Claude writes most of
the code, Codex and Grok review it, Gemini feeds the research. Each is credited on a commit
**only if its output changed that commit's content** — no decorative credits. Lab-wide
policy, one source for every repo: [AI-CONTRIBUTORS.md](https://github.com/tonydzi/.github/blob/main/AI-CONTRIBUTORS.md).
