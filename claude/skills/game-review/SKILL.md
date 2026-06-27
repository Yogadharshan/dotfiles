---
name: game-review
description: Use to evaluate an implemented gameplay feature from both a player experience and a technical quality perspective. Reviews gameplay clarity, player feedback, game feel, responsiveness, player understanding, maintainability, and technical risks. Use after game-implement, before considering a feature done, or when the developer says "review this", "does this feel right", "is this implementation solid", "check this before I move on". Do NOT use to redesign gameplay or rewrite large sections of code — review identifies, it mostly does not rebuild. This skill asks both "is it fun and clear?" and "will it stay maintainable?"
user-invocable: true
---

# game-review

evaluate the feature from the player's seat first, the codebase second. a feature with clean code that confuses or bores the player has failed its primary job. a feature with messy code that the player loves is a candidate for cleanup later, not a blocker.

## purpose

surface what the developer cannot see from inside the implementation. this includes: player confusion that seemed obvious to avoid during building, feedback gaps that feel acceptable in the editor but wrong in play, technical shortcuts that will cost real time later, and scope drift that slipped past the NOT done list.

the secondary purpose is the lessons learned section — making every review teach something generalizable, not just flag something fixable. a review that only produces a checklist of fixes teaches nothing about why those problems appear.

## a warning about self-review

if you implemented this feature in the same conversation, you are defending it. you know why every decision was made and your brain will rationalize the bad ones. counter this deliberately: review the running behavior and the spec, not your memory of building it. for high-stakes features (core loop, narrative flow, anything the player sees every session), run the review in a fresh session that only has the diff and the spec. say so when it matters.

## when to use

- after `game-implement`, before marking a feature done
- when a feature works but something about it feels off and you're not sure what
- when the developer wants a quality and risk read before building on top of this feature
- when a feature has been in the game for a while and the developer suspects it's creating drag

## when NOT to use

- to redesign gameplay → that's `game-architect`
- to rewrite large sections of code → review identifies problems and sizes them. small obvious fixes (wrong variable name, dead signal connection, off-by-one in timing) can be applied here. anything bigger becomes a finding that goes back to game-implement or game-architect.

## inputs

- the changed files and the running scene
- the game-architect spec this feature was supposed to satisfy (check if it drifted)
- play it: run the scene and take the player action at least once before reviewing. if you cannot run it, note this limitation explicitly.

## process

1. **play it first.** open the relevant scene in godot, run it, take the specified player action. note your immediate reaction before reading the code.
2. **check it against its spec.** did it deliver the stated player experience goal? did it stay in scope?
3. **review gameplay clarity:** would a player who doesn't know godot or the spec understand what just happened?
4. **review player feedback:** is the response to the player's action immediate, visible, and appropriate in scale?
5. **review game feel:** does the timing, weight, or pacing of the interaction feel correct for the game's tone? (for sphural: mythological, considered, not arcade-frenetic)
6. **review player understanding:** after this interaction, does the player know more about the game world or their options than before?
7. **review maintainability:** will the next developer (you, six weeks from now) understand what this code does and why?
8. **tally technical debt created vs removed.**
9. **write the review. write the lessons learned section last, after you know what the most important finding is.**

## outputs

write to `.claude/work/reviews/YYYY-MM-DD-short-slug.md`. format:

```
# game-review: <feature / spec slug>
date: <date>   reviewed by: <self / fresh session>

## lead finding
<the single most important thing — gameplay or technical — stated first. one specific sentence.>

## gameplay evaluation

### clarity
<does the player understand what happened and why? specific observation.>

### player feedback
<is the response to the player action immediate and appropriately scaled? what's missing or excessive?>

### game feel
<timing, weight, pacing — specific to this game's tone. does it fit sphural's register?>

### player understanding
<after this interaction, what does the player now know? is that the right thing to know?>

## technical evaluation

### strengths
- <specific, tied to the code — not "clean implementation">

### weaknesses
- <specific finding> — <why it matters to player experience or future development> — severity: now / soon / someday

### technical risks
- <what will hurt later if this is left as-is>

## spec compliance
<did the implementation deliver the player experience goal from the spec? did it stay in scope? if it drifted, name the specific drift.>

## technical debt
- created: <what shortcut was taken and what it will cost later>
- removed: <what got better than before>

## playtesting recommendations
- <specific thing to observe in a real playtest, not in the editor>
- <player behavior to watch for that would confirm the feature is working>
- <player confusion to watch for that would indicate a problem>

## lessons learned
<what should the developer understand after reading this review — about game design, godot, or the specific tradeoff made here? name the principle behind the lead finding. connect it to something the developer will encounter again. one to four sentences. if this lesson could have been written without reading the code or playing the feature, delete it and write a better one.>

## verdict
playable / playable with known issues / not playable — one line why, and what to do next
```

## stop conditions

- do not rewrite large sections of code. name the problem and its impact, then stop. small obvious fixes are fine to apply directly.
- do not redesign gameplay here. if the design is wrong, that's a finding pointing back to `game-architect`.
- do not pad the strengths section to soften the weaknesses. if there are two real strengths, list two.
- do not write a lessons learned section that could have been written without seeing the code. "always validate player input" is not a lesson. "the skip-to-end logic fails silently when called on the last line because `current_line == line_count` is not checked before advancing — the player sees a frame of empty text" is a lesson.
- stop after the verdict.

## quality standards

- the lead finding is the most impactful thing, stated specifically. "the dialogue skip works but the advance input isn't buffered, so fast tappers accidentally trigger skip when they meant to advance one line" is a lead finding. "the implementation is mostly good" is not.
- every gameplay evaluation section contains a specific observation, not a verdict. "the feedback is immediate" is not an observation; "the label updates in the same frame as the input, but there's no audio cue, so players who are looking at the portrait won't register that the text changed" is.
- severity is honest. a player-visible bug is always at least "soon." a maintainability issue with no player impact is "someday."
- playtesting recommendations describe what to *watch*, not what to *check*. watching a player try to skip and seeing them accidentally advance three times is playtesting. clicking through yourself in the editor is not.
- lessons learned generalizes from this specific case to a principle the developer will encounter again. it does not restate the finding.

## examples

good: review of dialogue skip feature. played the scene: immediately noticed that holding the advance key for 500ms skips, but the threshold isn't visible to the player — they discover it by accident or not at all. lead finding: "the skip threshold is invisible; the player has no affordance that holding is different from tapping, so the feature exists but won't be found." gameplay: clarity — the advance works, but skip is a hidden interaction. feedback — no visual change during the hold period (no progress indicator, no hint). game feel — fits the game's pacing but can't be used intentionally by players who don't know it exists. technical: strength — the hold detection is frame-accurate without a timer node (good godot practice). weakness — the threshold is hardcoded at 500ms, needs tuning per player. risk — if other inputs get a hold detection later, there's no shared hold utility. debt created: undiscoverable mechanic. debt removed: none. playtesting: watch a first-time player try to get through a long speech — do they try holding? do they discover skip? lessons learned: "discoverable interactions need at least one affordance — a visual hint, a tooltip, or a prompt. a mechanic with no affordance is not a feature players have, it's a feature testers have." verdict: playable with known issues — add a hold-progress indicator before treating skip as done.

bad: review that says "great implementation, the code is clean and the feature works as expected. recommend testing with real players." no specific observations, no gameplay evaluation, no technical risks, no lesson, lead finding is a compliment.
