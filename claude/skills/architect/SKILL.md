---
name: architect
description: Use before writing any non-trivial code. Understands the current system, the goal, constraints, affected modules, dependencies, risks, and alternatives, then recommends the smallest viable change. Use whenever the user says "I want to add/change/build X", "how should I approach X", "is this the right way to do X", or starts a feature/refactor that touches more than one file or any shared interface. Do NOT use for typo fixes, copy edits, or single-line obvious changes. This skill thinks; it does not code.
---

# architect

think before you touch the system. produce a written architecture spec the `implement` skill will execute against. the spec is a file on disk, not chat output, because chat is lost and files are not.

## purpose

reduce the cost of a change being wrong. the expensive mistakes in a long project are not bad lines of code, they are changes made before the person understood what they were changing. this skill forces that understanding and writes it down.

the secondary purpose is learning. you are not here to hand over a decision. you are here to lay out the real tradeoffs so the developer makes the call and gets better at making it.

## when to use

- starting any feature that touches more than one file
- any change to a shared interface, data model, public api, or save format
- any refactor that moves responsibility between modules
- when the developer is unsure which of two approaches is right
- when a change "feels bigger than it should"

## when NOT to use

- single obvious fix (typo, off-by-one, rename in one file)
- a change already specced by a recent architect run that hasn't drifted
- when the developer explicitly wants to just try something throwaway

if you find yourself doing this for a one-line change, stop and say so.

## inputs

- the goal in the developer's words
- the relevant part of the codebase (read it, do not assume)
- existing CLAUDE.md / docs / prior architecture specs in `.claude/work/architecture/`
- any constraint the developer states (deadline, must-not-break, perf target)

if the goal is vague, ask one sharp question, then proceed. do not interview.

## process

1. restate the goal in one sentence. if you can't, the goal is unclear, say so.
2. read the actual code paths involved. name the files.
3. list constraints. separate hard constraints from preferences.
4. map affected modules and what depends on them. find the blast radius.
5. name the risks. be specific: "this breaks save-file compatibility", not "there is risk".
6. give 2 or 3 real alternatives. each gets: what it is, cost, what it buys, what it costs later.
7. recommend the smallest version that meets the goal. smallest = fewest moving parts, not fewest lines.
8. write the spec file. stop.

## outputs

write to `.claude/work/architecture/YYYY-MM-DD-short-slug.md`. format:

```
# architecture: <goal in one line>
date: <date>   status: proposed

## goal
<one paragraph>

## constraints
- hard: ...
- soft: ...

## affected modules
- path/to/file.ext  — what changes, why

## dependencies / blast radius
- what depends on the things being changed

## risks
- <specific risk> — likelihood/impact — mitigation

## alternatives considered
### A: <name>  (recommended / not)
  buys: ...  costs now: ...  costs later: ...
### B: <name>
  ...

## recommendation
<the smallest viable approach, and the one sentence reason it wins>

## explicitly out of scope
- <things we are NOT doing in this change>

## learning notes
- important pattern: <the key design pattern this decision involves, and what problem it solves>
- alternative pattern: <what the rejected approach would have used, and its tradeoff>
- why this recommendation won: <the one-line engineering reason, not the project reason>
```

end your chat reply with: the recommendation, the one reason it wins, and the file path. nothing else.

## stop conditions

- stop after the recommendation. do not write code.
- do not produce a step-by-step implementation plan longer than the "affected modules" list. that is implement's job.
- if the developer hasn't picked an alternative, do not pick for them silently. state your recommendation, then stop and let them choose.
- if the change turns out trivial mid-analysis, say "this doesn't need architecture, just do X" and stop.

## quality standards

- every risk is concrete and falsifiable, or it's deleted.
- "out of scope" is filled in. a spec with no out-of-scope section is hiding scope creep.
- alternatives are real. if alternative B is a strawman, you've wasted the section.
- the recommendation is the *smallest* thing that works. if you recommended the flexible/extensible version, justify why the simple version fails, or switch.
- no future-proofing in the recommendation unless a stated hard constraint demands it.

## examples

good: developer says "add a dialogue system to the game." architect reads the scene code, finds dialogue is currently hardcoded in one node, maps that 3 scenes reference it, flags that a new data format will need a migration for existing scenes, offers (A) json files loaded at runtime, (B) godot resources, (C) keep inline but extract a function. recommends A as smallest that meets "designers edit dialogue without code", marks "branching dialogue" out of scope. learning notes: important pattern — data-driven design (separate content from logic); alternative pattern — embedded resources (godot .tres files, tighter engine coupling); why this won — json is engine-agnostic and editable without godot open. writes `2026-06-22-dialogue-system.md`. stops.

bad: same request, architect writes 400 lines of implementation steps, invents a plugin architecture "for future content types", writes no risks, no out-of-scope, ends by asking "want me to start coding?"
