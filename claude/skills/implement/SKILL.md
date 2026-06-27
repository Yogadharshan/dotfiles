---
name: implement
description: Use to execute an already-approved architecture spec. Implements only the approved change, preserves existing architecture, avoids unrelated refactors and speculative abstractions, keeps scope minimal. Use after an architect spec exists and the developer has approved an approach, or when the developer says "implement this", "build the approved change", "write the code for X we discussed". Do NOT use to redesign, to introduce new architecture, to add future-proofing, or to touch unrelated files. If no approved spec exists, send the developer to the architect skill first.
---

# implement

execute the approved spec. add nothing it didn't ask for. the discipline this skill enforces is *not doing extra things while you're in there*.

## purpose

turn an approved architecture spec into working code with the least new complexity. scope discipline is the whole point. most damage to a codebase comes from "while i was in here i also..." this skill refuses that.

## when to use

- an architecture spec exists in `.claude/work/architecture/` and an approach is approved
- the developer says implement / build / write the code for the approved change

## when NOT to use

- no spec exists and the change is non-trivial → use `architect` first
- the developer wants to redesign or explore → that's `architect`, not here
- you notice the spec is wrong mid-way → stop, don't improvise, go back to architect

## inputs

- the approved architecture spec file (read it fully first)
- the current code
- the chosen alternative, if the spec listed several

if no spec exists and the change is non-trivial, say so and stop. do not invent the architecture and proceed.

## process

1. read the spec. read the "out of scope" section twice.
2. implement only what the spec covers.
3. preserve existing patterns. match the code that's already there, don't "improve" its style.
4. write tests for the new behavior.
5. report what changed and why, mapped back to the spec.

## outputs

a chat report (and the actual code changes):

```
## implemented: <spec slug>

### files changed
- path/to/file.ext — <what changed> — <why, tied to spec>

### architecture impact
<did this preserve the architecture? any unavoidable deviation from the spec, and why>

### tests added
- <test> — <what it protects>

### NOT done (held to scope)
- <anything tempting you deliberately skipped>

### concepts involved
- <pattern or concept used, e.g. "resource loading" or "observer pattern"> — <one line on why it applies here>
- <only list concepts that actually appear in this implementation; skip this section if the change was mechanical>
```

## stop conditions

- do not redesign systems.
- do not introduce new architecture or new abstractions. if the spec didn't name an abstraction, don't add one.
- do not add future-proofing, config options, or extensibility "for later".
- do not touch files outside the spec's affected-modules list. if you think one needs touching, stop and report it instead of doing it.
- do not refactor unrelated code, even if it's bad. note it for `review` or a future architect run.
- if implementing reveals the spec is wrong, stop and report. do not silently fix the architecture.

## quality standards

- diff is the smallest that makes the approved change work and pass tests.
- no new dependency unless the spec named it.
- new code matches existing conventions in that file/module.
- the "NOT done" section is non-empty if you were tempted to do more, which you usually were. an empty "NOT done" on a real change is suspicious.
- tests cover the new behavior and the failure mode the architect flagged as risky.

## examples

good: spec says "load dialogue from json at runtime, out of scope: branching." implement adds a loader, wires the one scene node to it, converts existing inline dialogue to one json file, adds a test that a malformed json fails loudly. reports 3 files changed, notes it left the other 2 scenes' inline dialogue alone because the spec scoped to one. lists "skipped: caching the parsed json (not needed yet)".

bad: same spec, implement also adds a branching system "since we'll need it", refactors an unrelated input handler "while here", adds a config flag for dialogue speed nobody asked for, touches 9 files, no tests.
