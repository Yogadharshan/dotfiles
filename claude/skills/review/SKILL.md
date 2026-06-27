---
name: review
description: Use to evaluate the quality of an implementation, not to rewrite it. Reviews architecture fit, maintainability, readability, coupling, cohesion, and testing, then reports strengths, weaknesses, future risks, and technical debt created vs removed. Use after implementing a change, before committing/merging, or when the developer says "review this", "is this good code", "what's wrong with this", "check this before I commit". Do NOT use to redesign systems or to rewrite the code unless a fix is small and obvious. This skill judges; it mostly does not edit.
---

# review

evaluate the change honestly. the developer wrote it (or Claude did, in the same session, which is worse). your job is to find what's weak before it ships, and to teach by naming *why* it's weak.

## purpose

surface quality and risk the author can't see, and convert each finding into something the developer learns. a review that only says "looks good" taught nothing and caught nothing.

## a warning about self-review

if you implemented this change earlier in the same conversation, you are biased toward defending it. counter this deliberately: review the **diff and the spec**, not your memory of why you wrote it. for high-stakes changes, the developer should run review in a fresh session that only sees the diff. say so when it matters.

## when to use

- after `implement`, before commit or merge
- when the developer wants a quality/risk read on existing code
- when something works but the developer suspects it's not clean

## when NOT to use

- to redesign the system → that's `architect`
- to rewrite large chunks → review identifies, it doesn't rebuild. small obvious fixes are fine to apply; anything bigger becomes a finding.

## inputs

- the diff or the changed files
- the architecture spec the change was supposed to satisfy (so you can check it actually did)
- the surrounding code the change touches

## process

1. check the change against its spec. did it do what was approved, and only that?
2. read for coupling and cohesion: does this code know about things it shouldn't? does it belong where it is?
3. read for maintainability and readability: will the developer understand this in 6 months cold?
4. check tests: do they protect the risky behavior, or just the happy path?
5. tally debt created vs debt removed.
6. report. lead with the most important weakness.

## outputs

```
## review: <change / spec slug>

### lead finding
<the single most important thing, good or bad, first>

### strengths
- <specific, with reason — not "clean code">

### weaknesses
- <specific> — why it matters — severity (now / soon / someday)

### future risks
- <what will hurt later if left>

### technical debt
- created: <what shortcut was taken, and the interest it'll charge>
- removed: <what got better>

### lessons learned
<what should the developer understand after reading this review? name the engineering principle behind the lead finding, not just the finding itself. if the weakness is "mixed responsibility", say what single responsibility principle is and why violating it creates the specific pain you identified. one to three sentences that generalize beyond this change.>

### verdict
ship / ship after fixing [X] / don't ship — one line why
```

## stop conditions

- do not rewrite the code, beyond small obvious fixes (typo, dead import, an obviously wrong condition).
- do not redesign. if the architecture is wrong, that's a finding pointing back to `architect`, not a rewrite here.
- do not pad with strengths to soften weaknesses. if there are 3 real strengths, list 3.
- stop after the verdict.

## quality standards

- lead with the most useful thing, usually the worst problem.
- every weakness has a *why it matters*, not just a label. "high coupling" is useless; "this module imports the renderer, so it can't be tested or reused without the whole engine" is a finding.
- severity is honest. don't flag style nits at the same level as a data-corruption risk.
- no praise without a concrete reason tied to the code.
- the debt section is filled in both directions. a change that created zero debt and removed zero is rare; if you wrote that, look harder.
- if the change quietly drifted from its spec (did more, did less, did different), that is always a finding.

## examples

good: review of the dialogue loader. lead finding: "no validation on the json schema, a typo in a content file crashes the game at runtime with a null deref, not a clear error." strengths: loader is decoupled from the scene, testable in isolation. weakness: the loader also formats display strings (mixed responsibility, soon). debt created: silent failure mode. debt removed: dialogue no longer hardcoded across 3 files. verdict: ship after adding schema validation.

bad: review that says "great work, clean implementation, very maintainable!", lists no severity, ignores that there are no tests, rewrites half the file unprompted.
