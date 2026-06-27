---
name: game-implement
description: Use to implement an approved game-architect spec. Builds only the approved gameplay feature, prioritizes it being playable and feeling correct to the player, and avoids scope expansion. Use after a game-architect spec exists and the developer has approved an option, or when the developer says "implement the approved design", "build the X feature we specced", "write the code for X". Do NOT use to redesign gameplay systems, add unspecced feedback or mechanics, or implement speculative future features. If no approved spec exists for a non-trivial feature, go to game-architect first. Prefers a working, playable result over an architecturally clean one that isn't done.
user-invocable: true
---

# game-implement

build the approved feature so it is playable. the exit criterion is not "the code is clean" — it is "a player can interact with this and it behaves as the spec described." clean up later if needed. do not wait for clean to ship playable.

## purpose

close the gap between design and something the developer can actually run and play. a spec that never becomes a playable scene produces no learning. this skill's job is to make the feature real, as quickly and simply as possible, without adding things the spec didn't ask for.

the hardest discipline here is the same as in general implementation: *not doing extra things while you're in there.* game features are especially vulnerable to this because "while i was in the dialogue node i also added a typewriter effect, an auto-advance timer, a history scroll, and a speaker portrait system." none of that was asked for.

## when to use

- an approved game-architect spec exists in `.claude/work/architecture/`
- the developer approves an option and says to build it
- the feature is small enough that it clearly doesn't need architecture (one scene, one node, obvious behavior) — just build it and write a brief report

## when NOT to use

- no spec exists for a non-trivial feature → use `game-architect` first
- the developer wants to explore or try something throwaway → just try it, no skill needed
- the spec is wrong, incomplete, or the feature has changed since it was written → stop, report the mismatch, go back to game-architect if needed. do not silently implement a different thing.

## inputs

- the approved game-architect spec (read it fully, especially the out-of-scope section)
- the current scene and node files the spec named
- the approved option (A, B, or C from the spec)
- the godot project state (what scenes exist, what autoloads are registered, what signals are in use)

## process

1. **read the spec.** read the out-of-scope section twice. it tells you what you are not allowed to build even if it seems obvious to add.
2. **identify the minimum godot change.** which nodes change, which signals are added, which scripts are modified. if this is more than the spec's affected-systems list, stop and ask.
3. **build for playability first.** get it to a state where it can be played and tested. polish and cleanup come after the behavior is confirmed correct.
4. **match existing godot conventions in the project.** if the project uses `@onready`, use it. if signals are named `snake_case`, follow that. do not "improve" conventions while implementing.
5. **add the minimum feedback the player needs.** if the spec says "player sees a response," implement the simplest version of that — a label, a sound cue, an animation call — don't design a new feedback subsystem.
6. **write the implementation report.**

## outputs

a chat report (plus the actual code changes):

```
## implemented: <spec slug>

### files changed
- res://path/to/file.gd — <what changed> — <why, tied to spec>
- res://path/to/scene.tscn — <what changed> — <why>

### systems affected
- <system name> — <how it's different now>

### gameplay impact
<what the player can now do or experience that they couldn't before. one paragraph. honest about what's placeholder vs finished.>

### architecture impact
<did this preserve the existing scene structure? any deviation from the spec and the specific reason>

### testing recommendations
- <how to run this feature right now in the godot editor>
- <the specific player action to test>
- <the edge case most likely to break, and how to trigger it>

### NOT done (held to scope)
- <anything from the spec's out-of-scope list you were tempted to add>
- <anything not in the spec that felt obvious to add but wasn't>

### concepts involved
- <godot pattern or game programming concept used> — <why it applies here and what it gives you>
- skip this section if the implementation was mechanical (wiring nodes, setting properties)

### game feel notes
- <one honest observation about how this feels to interact with right now>
- <what specifically would make it feel better, flagged for a future polish pass — not done now>
```

## stop conditions

- do not redesign the gameplay system while implementing it. if the spec's recommended approach turns out to be wrong mid-implementation, stop and report the specific problem. go back to game-architect if needed.
- do not add mechanics, feedback, or systems the spec didn't name. a "quick typewriter effect" is a scope expansion. "just a little screen shake" is a scope expansion. note them under NOT done and keep moving.
- do not add godot autoloads, singletons, or new architectural patterns unless the spec specifically required them.
- do not refactor unrelated scripts or scenes. if you notice something broken nearby, add it to the handoff as a known issue, not to this implementation.
- if the implementation reveals the spec was wrong about something concrete (a node doesn't exist, a signal isn't available, a dependency is missing), stop and report it before guessing a fix.

## quality standards

- the feature is playable in the godot editor when you finish. "playable" means you can run the scene, take the specified player action, and observe the specified feedback. not production-ready — playable.
- the NOT done section is honest and non-empty on any real feature. if you didn't want to add anything, look again — you almost certainly had at least one temptation.
- game feel notes are specific observations, not aspirations. "the dialogue advance feels slightly laggy because there's no input buffer — could add 100ms buffering in a later pass" is a game feel note. "it would be cool to add animations later" is not.
- testing recommendations name the specific godot scene to open, the specific key or click to test, and the specific edge case. "test the dialogue system" is not a testing recommendation.
- new code matches existing project conventions. if it doesn't, name the deviation and the reason in architecture impact.

## examples

good: spec says "add hold-to-skip for dialogue advance, out of scope: full conversation skip, skip settings." implement adds an `_input` check for `ui_accept` hold duration in the dialogue node's script, triggers a `skip_to_line_end()` function that advances to the last line of the current speaker's block, updates the label in one frame. report: 1 file changed (dialogue_box.gd), 1 scene touched (dialogue.tscn — label node path updated), gameplay impact: player can now hold advance to skip to speaker's end. testing: open `scenes/dialogue_test.tscn`, hold enter during murugan's first speech, should jump to last line of his block. NOT done: conversation-level skip (out of scope), input buffering for fast tappers (game feel note for later). concepts involved: input hold detection with `Input.is_action_pressed` — gives frame-accurate hold time without a timer node. game feel: currently snaps with no visual transition; a 1-frame fade would smooth it significantly.

bad: same spec. implement adds a typewriter effect, a dialogue history scroll node, a settings flag for skip behavior, an autoload DialogueManager singleton "because we'll need it," refactors the dialogue scene to use a custom resource type, touches 7 files, and the dialogue skip itself isn't actually hooked up at the end.
