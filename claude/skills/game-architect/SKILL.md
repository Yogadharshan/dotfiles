---
name: game-architect
description: Use before implementing any non-trivial gameplay feature. Starts from what the player experiences, not from how the code will be structured. Use whenever the developer says "I want to add X mechanic", "how should the player do X", "design the X system", "what should happen when the player does X", or starts a feature that affects the core gameplay loop, player feedback, or scene flow. Do NOT use for single-node tweaks, parameter adjustments, or changes that don't affect what the player sees or does. This skill designs the player experience first; the technical shape follows from that.
user-invocable: true
---

# game-architect

design what the player experiences before deciding how to build it. a game feature that is well-built but confusing or unfun is a failed feature. get the player experience right first, then find the smallest implementation that delivers it.

## purpose

prevent building the wrong thing. the expensive mistake in game development is not bad code — it is implementing a system whose player experience was never validated. this skill forces you to articulate what the player will feel and do before a single node is added.

the secondary purpose is scope honesty. most game systems start small and expand. this skill identifies that expansion pressure early and explicitly marks what is out of scope, so you ship a playable version instead of waiting for a complete one.

## when to use

- adding any mechanic that changes what the player does or feels
- adding or changing a gameplay system (dialogue, combat, movement, narrative flow, choices, feedback)
- changing how a scene transitions or how story state is tracked
- when something "feels wrong" to play but you're not sure why — use this to re-examine the design, not just tweak values
- when you are about to build something and realize you don't know what the player is supposed to feel during it

## when NOT to use

- adjusting a number (speed, volume, timing) that doesn't change the player's understanding of the game
- fixing a bug that breaks an already-designed behavior
- a node-level change that doesn't affect any other system or scene
- when the developer explicitly wants to prototype something throwaway — just build it, don't spec it

if the feature takes less than an hour to implement and only touches one scene node, skip this and build it.

## inputs

- the developer's description of what they want to add or change
- the relevant scene(s) and node structure (read them — do not assume)
- previous game-architect specs in `.claude/work/architecture/` (check for prior decisions on related systems)
- any stated constraints: release targets, platform, scope limits, existing player expectations

if the goal is "i want to add X" and X is vague, ask one sharp question: "what do you want the player to feel when they use X?" then proceed.

## process

1. **state the player experience goal** in one sentence. not the technical goal — what the player feels or understands. if you can't write this sentence, the feature isn't defined yet; say so.
2. **state the player behavior** — what specific action does the player take, and what do they immediately see/hear in response.
3. **read the relevant scenes and nodes.** name the files. identify the current gameplay loop and where this feature sits inside it.
4. **identify what systems are affected.** every system the new feature touches is a risk surface.
5. **name the risks.** be specific to this game: "the player won't understand they can skip dialogue", not "there is a ux risk". for a narrative game, understanding risks are almost always higher priority than technical risks.
6. **give 2 or 3 real implementation options.** each gets: player experience it delivers, implementation cost, what it does NOT handle. do not include a strawman option.
7. **recommend the smallest playable version.** smallest = fewest systems touched while still delivering the stated player experience goal. not the most flexible, not the most complete.
8. **mark what is out of scope.** if you leave this empty, you are hiding scope creep.
9. **write the spec file. stop.**

## outputs

write to `.claude/work/architecture/YYYY-MM-DD-short-slug.md`. format:

```
# game-architect: <feature in one line>
date: <date>   status: proposed

## player experience goal
<one sentence: what does the player feel or understand that they didn't before>

## player behavior
- action: <what the player does>
- feedback: <what happens immediately in response — visual, audio, text>
- understanding: <what the player should now know about the game world or their options>

## current gameplay loop
<where does this feature sit in the loop? what comes before it, what comes after>

## affected systems
- scenes/nodes: <list of godot scenes and nodes this touches>
- signals/events: <any signals this adds, modifies, or listens to>
- data/state: <any game state, save data, or resources this reads or writes>

## risks
- <specific risk to player experience> — <mitigation>
- <specific technical risk> — <mitigation>

## options
### A: <name>  (recommended / not recommended)
  player experience: ...
  cost: ...
  does not handle: ...

### B: <name>
  player experience: ...
  cost: ...
  does not handle: ...

## recommendation
<the smallest playable version and the one sentence reason it wins over the alternatives>

## out of scope
- <things we are deliberately NOT building in this version>

## learning notes
- important pattern: <the key game design or godot pattern this decision uses, and why it fits here>
- alternative pattern: <what the rejected option would have used, and its specific tradeoff for this game>
- why this recommendation won: <the player experience reason, not the engineering reason>
```

end your chat reply with: the player experience goal, the recommendation, and the file path. nothing else.

## stop conditions

- stop after the recommendation. do not write code. do not write step-by-step implementation instructions — that is game-implement's job.
- if the feature turns out trivially small mid-analysis ("just set autoplay = true on the AnimationPlayer"), say so and stop. don't write a spec for it.
- if the developer hasn't chosen an option, state your recommendation and stop. do not silently implement the recommended option.
- do not design for future story arcs, future characters, or future platforms unless they are stated hard constraints. out of scope is not a failure; it is discipline.

## quality standards

- the player experience goal is written first and the technical design serves it. if the spec reads like a technical design doc with a player experience section bolted on top, rewrite it.
- every risk is specific to this game and this feature. "performance" and "maintainability" are not game risks unless you explain the specific scenario where they hurt the player.
- the out-of-scope section is filled. an empty out-of-scope section on a real feature means scope has not been controlled.
- the recommendation is the smallest thing that delivers the player experience goal. if you recommended the extensible version, you must explain specifically why the simple version fails to deliver the stated experience.
- options are real alternatives, not a "good option" and two bad ones.

## examples

good: developer says "add a way for the player to skip dialogue." game-architect reads the dialogue scene, finds dialogue is driven by a line-by-line advance on input, identifies that skip requires knowing where the current conversation ends (not currently tracked), notes the risk that skipping mid-mythology narration might disorient the player if they land mid-scene. offers: (A) skip to end of current speaker's turn on hold-input, (B) full conversation skip on double-tap, (C) no skip, but faster advance speed. recommends A as smallest that respects narrative pacing while giving players control, marks B out of scope. learning notes: pattern is "interruptible sequence with defined exit points"; alternative (B) uses "atomic skip" which loses the pacing the story depends on; A wins because this is a story game and players who skip still need to understand what was communicated. writes `2026-06-22-dialogue-skip.md`. stops.

bad: same request. game-architect designs a full dialogue management singleton, adds a skip registry, plans a settings menu for skip behavior, proposes a "fast-forward mode" for speedrunners, writes 200 lines of implementation steps, never states what the player actually experiences when they skip, never marks anything out of scope.
