---
name: handoff
description: Use to save the project's state to disk so a future session can resume without reading the whole conversation. Summarizes what changed and why, the architectural decisions made, current project state, unresolved issues, known limitations, risks, and the next recommended actions. Use at the end of a work session, before stepping away from a feature, after a review when a change is shipped, or when the developer says "handoff", "save state", "wrap up", "write a handoff", "where did we leave off". Do NOT use mid-implementation, and do NOT use to fix code or make decisions; it only records. If nothing meaningful changed this session, say so and don't write a file.
---

# handoff

write down what the next session needs to pick up where this one stopped. the next session is probably you, two weeks from now, with zero memory of why you did any of this. the chat is gone. the code shows *what* exists. handoff preserves *why*, *where you are*, and *what's next* — the three things that are otherwise lost.

## purpose

context recovery, not record-keeping. the test for every line is: would a cold session need this to resume correctly? if not, cut it. the expensive failure in a long solo project is not lost code, it's lost reasons — re-deriving why a decision was made, re-discovering a half-finished edge case, re-litigating a tradeoff you already settled. handoff stops that re-work.

the secondary purpose is honesty about state. a handoff that says "all done, clean" when there are three known bugs and a skipped test is worse than no handoff, because the next session trusts it.

## when to use

- end of a work session, even an unfinished one
- after a change ships (post-review) and you're moving on
- before stepping away from a feature for more than a day
- when you're about to hit context limits and want the thread captured
- when the developer says handoff / save state / wrap up / where did we leave off

## when NOT to use

- mid-implementation, with the build half-broken → finish or stash first, then handoff the real state
- to make a decision or fix code → that's `architect`, `implement`, or `review`. handoff only records.
- when nothing meaningful happened this session → say "nothing to hand off" and stop. do not manufacture a file to look productive.

if you're tempted to write a handoff for a session where you only read code and made no decisions, don't. a handoff with no decisions and no changes is noise the next session has to wade through.

## inputs

- the conversation / work done this session
- the architecture spec(s) touched, in `.claude/work/architecture/`
- the review(s) from this session, in `.claude/work/reviews/`
- the previous handoff, if one exists, in `.claude/work/handoff/` (read it — you're continuing a chain, not starting fresh)
- the actual state of the code (is it committed? does it build? do tests pass?)

if you can't tell whether the code builds or tests pass, say so in the handoff instead of guessing. an unknown state recorded honestly beats a green checkmark you invented.

## process

1. read the previous handoff so this one continues the thread instead of repeating it.
2. list what actually changed this session — tie each item to its spec or review if it has one.
3. capture the *decisions* and the one-line reason each was made. this is the part code can't store.
4. state the real current state: committed or not, builds or not, tests pass or not, what's half-done.
5. list unresolved issues, known limitations, and live risks. be specific and honest.
6. write the single most useful next action, and why it's next. then a short ordered list after it.
7. write the file. end your chat reply with the file path and the one-line next action. stop.

## outputs

write to `.claude/work/handoff/YYYY-MM-DD-short-slug.md`. if you write more than one handoff on the same day, append `-v2`, `-v3`. format:

```
# handoff: <one-line summary of this session>
date: <date>   session focus: <what this session was about>
state: <committed | uncommitted | partial>   build: <pass | fail | unknown>   tests: <pass | fail | none | unknown>

## what changed
- <change> — <why> — (spec: YYYY-MM-DD-slug / review: YYYY-MM-DD-slug, if any)

## decisions made (and why)
- <decision> — <the one-line reason, the thing you'd otherwise forget>

## current state
<2-4 sentences a cold reader needs to rebuild the mental model. what works now that didn't before, what's mid-flight.>

## files affected
- path/to/file.ext — <what's different about it now>

## unresolved / known limitations
- <thing that is knowingly not handled, and whether it matters>

## risks
- <what could bite the next session if they don't know it>

## next steps
1. <the single most important next action> — <why it's first>
2. ...
```

## stop conditions

- do not write code or change anything. handoff records, it does not act.
- do not make decisions the session didn't actually make. if a decision is still open, list it under unresolved, don't resolve it here.
- do not dump the whole conversation. if your handoff is longer than the architecture spec it summarizes, you're transcribing, not handing off. cut it.
- do not claim a state you didn't verify. unknown is a valid, honest value.
- stop after the file is written and the path + next action are reported.

## quality standards

- a cold reader can resume from this file alone, without the chat. that is the only real pass/fail.
- every decision has its reason. a decision with no "why" will be re-litigated next session — that's the exact waste this skill exists to prevent.
- state fields (committed / build / tests) are filled and honest. "unknown" is allowed; a fabricated "pass" is not.
- next steps lead with one concrete action, not a vague direction. "wire the loader to scene 2 and add a missing-file test" is a next step; "continue the dialogue work" is not.
- unresolved and risks are non-empty on any real session, or you're hiding something. a feature that shipped with zero limitations and zero risks is rare; look again before writing that.
- it links back to the spec and review files instead of restating them. the chain is the memory; handoff is the index into it.

## examples

good: end of the dialogue-system session. handoff records: state uncommitted, build passes, tests pass. changed: added json loader (spec 001), wired scene 1, converted scene 1's inline dialogue. decision: kept scenes 2 and 3 on inline dialogue for now — reason: scope was one scene, migrating all three was untested risk. current state: scene 1 loads dialogue from `dialogue/scene1.json`; the loader is isolated and tested; scenes 2-3 unchanged. unresolved: no schema validation (review 001 flagged it, deferred). risk: a content-file typo crashes at runtime, not at load. next: 1) add schema validation before touching scenes 2-3 (review called this ship-blocking for content editors), 2) migrate scene 2. writes `2026-06-22-dialogue-scene1.md`. stops.

bad: handoff that says "worked on dialogue today, made good progress, lots done, will continue tomorrow." no state, no decisions, no reasons, no files, no next action. the next session learns nothing and re-reads the whole codebase to find out where things stand — which is the cost this skill was supposed to remove.
