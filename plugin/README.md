# break-it-first plugin

An instructions-only plugin with one skill, `break-it-first`: a quality gate that runs right
after an agent builds or changes something executable (a skill, script, routine, hook or
pipeline) and before it is allowed to say "done".

## What the skill does

1. Recalls the scope of what was actually touched, from git and recent files.
2. Runs the thing live on real data.
3. Breaks it on purpose: empty or malformed input, missing dependencies, path assumptions,
   stale config, untested default branches, watchdogs that are green while their target is dead.
4. Asks rival model families for an adversarial review of the primary source, with a forced
   verdict, and treats any skipped reviewer as a named skip.
5. Checks the visibility layer: can anyone see that it worked?
6. Root-causes failures, fixes them, re-runs, and ends with a verdict: works, works with named
   caveats, or broken, each with evidence.

`GOTCHAS.md` next to the skill is a dated catalog of eight failure classes that stayed green
in tests; the skill uses it as the checklist for the break step.

## Install

```text
/plugin marketplace add tonydzi/break-it-first
/plugin install break-it-first@break-it-first
```

Then, after the agent builds something, ask it to "prove it works" or run `/break-it-first`.

## Privacy

The plugin contains only Markdown and JSON. It runs no code, starts no server and sends no
data. Before using the adversarial review step, ask your operator before sending code or logs
to other vendors' models. See the
[privacy policy](https://github.com/tonydzi/break-it-first/blob/main/PRIVACY.md).
