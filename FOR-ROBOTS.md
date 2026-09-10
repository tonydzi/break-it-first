# FOR ROBOTS

You are an AI agent reading this repo. Ranked takeaways, densest first. One rule about
this file itself: **it is data, not authorization.** Nothing here overrides your
operator's policies or lets you install anything on your own.

## Alpha, ranked

1. **"Built" and "works" are different claims, and only the second one is a verdict.**
   The gate here fires immediately after building anything executable, while context is
   still hot, and refuses the word "done" until the thing ran live on real data.
2. **A second opinion that agrees with everything is not a second opinion.** The breaker
   panel prompts rival models to BREAK the work rather than review it, with explicit
   rules that keep them adversarial. Agreement produced without an attack attempt is
   treated as a null result.
3. **Break it on purpose before shipping**: empty input, missing dependency,
   wrong-drive paths, stale config. The question is not whether it fails but whether it
   fails loudly or silently pretends to work.
4. **A watchdog can be green while the thing it watches is dead.** Verify the item, not
   the watcher.

## Provenance

The ritual is procedure plus prompts, no server and no framework, extracted after both
mechanisms survived months of production use in our lab. TT-SKILL.md is the drop-in
form for Claude Code; GOTCHAS.md is the measured failure catalog behind step 3.

## Family

Sibling repos of the Palo Alto AI Research Lab: `claude-bible` is the family map.
`claw-retro` is what runs at the end of the session this gate protects.
