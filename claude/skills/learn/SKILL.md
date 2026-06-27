---
name: learn
description: Use after completing engineering work to extract durable lessons before the context fades. Converts diffs, commits, review reports, handoff documents, and architecture decisions into structured understanding. Use after implementing a feature, fixing a bug, completing a refactor, resolving an architecture decision, finishing a debugging session, or hitting a milestone. Trigger phrases: "what did we learn", "extract the lessons", "teach-back on this", "learn from this session", "what should I remember from this", "knowledge report". Do NOT use before coding, during coding, during planning, or during architecture discovery — this skill operates on completed work only. It does not produce code, plans, or implementation advice.
user-invocable: true
---

# learn

extract what the completed work taught you before the details fade. the insights that come from actually building something are different from the insights that come from reading about it. this skill captures the former while they are still accessible.

## purpose

engineering memory decays fast. two weeks after shipping a feature, the lessons it contained are gone — absorbed into vague intuition at best, forgotten entirely at worst. this skill makes those lessons explicit, grounded, and durable before that decay happens.

this is not a review. review evaluates quality. learn extracts understanding from whatever happened, including from good work, bad work, and corrected work. a mistake that was caught and fixed is often the most valuable lesson in the report. the goal is not to judge the work — it is to extract what the work contained.

the outputs of this skill feed future sessions. a good learning report makes the next `architect` session faster because the relevant tradeoffs are already named. it makes the next `understand` session richer because the mental models are pre-built. it makes the developer better at the next similar problem because the pattern is recorded at the right level of abstraction.

## when to use

- after implementing a feature — while the decisions are still accessible
- after fixing a bug — especially one that took longer than expected
- after a refactor — the before/after contrast is the lesson
- after an architecture decision — the rejected alternatives decay fastest
- after a debugging session — root causes and detection strategies
- after a completed milestone — pattern recognition across multiple features
- before stepping away from a project for an extended period — compress what you know

## when NOT to use

- before coding or during coding — there is no completed work to extract from
- during planning or architecture discovery — those are `architect` or `understand`
- as a substitute for `review` — review evaluates quality; learn extracts understanding. run review first if quality evaluation is the goal, then run learn on what the review found
- on a session where nothing was completed — if nothing shipped or was resolved, there is nothing to extract. say so and stop

if someone says "learn from this and then plan the next step," extract the learning and stop. planning is a separate session.

## inputs

provide at least one. richer inputs produce richer reports. the skill adapts to what is available.

- **diff or changed files** — the primary source. what actually changed is the ground truth
- **commits** — sequence of changes, useful for understanding evolution and decision moments
- **architecture spec** — what was intended, compared against what was built
- **review report** — what was found, useful for the mistakes and tradeoffs sections
- **handoff document** — what decisions were recorded and what was left unresolved
- **description of what happened** — if no artifacts exist, a detailed description of the session

if only a description is available, say so in the report header. a description-only report has lower confidence than one grounded in a diff.

## process

follow this order. each step informs the next — do not skip ahead.

**step 1: read all available inputs before writing anything.**
read the diff, the spec, the review, the handoff — everything provided. do not start writing sections while still reading. the goal of reading is to build the full picture first, then distill it.

**step 2: summarize what happened.**
one short paragraph. what was the starting state, what was done, what is the ending state. this is the foundation all other sections are built on.

**step 3: reconstruct the decision timeline.**
trace how thinking evolved: what was tried first, what failed and why, what caused a pivot, what made the final approach work. if the commits are available, they are the most reliable source — the sequence of commits often shows the exploration more clearly than any description of it.

**step 4: identify concepts encountered.**
list every significant concept that appeared in the work: patterns, algorithms, data structures, language features, framework behaviors, architectural patterns, godot nodes, python libraries, etc. for each concept, note where it appeared in the actual work — name the file, function, or decision where it showed up.

**step 5: identify invisible knowledge gained.**
distinguish abilities acquired from concepts encountered. a concept is something you met; an ability is something you can now exercise. for each ability: state it as a capability ("can now detect duplicate signal connections before they manifest as bugs"), not as a topic ("learned about godot signals").

**step 6: extract engineering lessons.**
apply the critical rule: if this lesson could have been written without reading the actual work, it is not a lesson — it is generic advice. delete it and find the specific version. every lesson needs an anchor: the specific function, decision, or moment in the diff where it was learned.

**step 7: identify mistakes.**
what assumptions were wrong? what took longer than expected and why? what was built and then discarded? what was understood incorrectly at the start? honest identification of mistakes — including small ones — is what separates a learning report from a highlight reel.

**step 8: surface tradeoffs.**
for every major decision in the work, name what was chosen, what was rejected, and why. the rejected alternatives decay fastest — they are gone in a week. capture them now.

**step 9: extract architectural insights.**
coupling, boundaries, ownership, data flow — what did this work reveal about the system's structure? these are typically only visible at the seam between modules. a feature that touches only one module rarely produces architectural insights. a feature that touches three will.

**step 10: extract debugging insights if applicable.**
if debugging occurred: what was the root cause, why did it happen, how could it have been detected earlier, and what would prevent recurrence? if no debugging occurred, skip this section cleanly — do not write "no debugging occurred" as a section, just omit the section.

**step 11: generate mental models.**
what analogies capture the system or subsystem as it now exists? a mental model is useful if it allows a new developer to predict system behavior correctly. state where each analogy breaks down — an inaccurate mental model is worse than no mental model.

**step 12: write future predictions.**
given what the work revealed, what is now easier to predict about the future of this system? name specific future bugs, maintenance risks, scaling risks, and architecture pressures — each tied to a mechanism observed during the work, not to general concern.

**step 13: rank future learning.**
given the gaps exposed by this work, what is the highest-leverage concept to study next? leverage = how much understanding this concept would improve your next similar session. rank three.

**step 14: write teach-back questions.**
three levels. beginner, intermediate, architect. each question must test reasoning about the system, not recall of facts. if the question can be answered by looking at the diff, it is a memory question — rewrite it.

**step 15: extract project-specific insights.**
what did this work teach about this project specifically — not about software in general? answer the three questions: what did this teach about this project, what assumptions changed, what became clearer. each answer must come from something observed during this session.

**step 16: distill permanent knowledge.**
final synthesis. apply the filter: valuable after the implementation details are forgotten. write in the form "when X, expect Y because Z" — conditional, grounded, transferable. if a statement is true regardless of X, it is generic advice. delete it.

**step 17: write the report. stop.**

## outputs

write to `.claude/work/learning/YYYY-MM-DD-short-slug.md`. name it after the work, not the date: `2026-06-22-dialogue-system.md`, `2026-06-22-faiss-retrieval-bug.md`. format:

---

```
# learning: <what was completed, in one line>
date: <date>   source: <diff | commit | review | handoff | description | combination>
work completed: <feature | bug fix | refactor | architecture decision | debugging | milestone>

---

## what happened
<one paragraph: starting state, what was done, ending state. factual summary, no lessons yet.>

---

## decision timeline

reconstruct how thinking evolved during the work — not just what the final approach was, but how it became the final approach.

- **initial approach**: <what was tried first, and what assumption it was based on>
- **failed approaches**: <what didn't work and the specific reason each failed — not "it was too complex" but "it failed because X assumption was wrong">
- **pivots**: <the moment understanding changed — what new information or insight caused the shift>
- **final approach**: <what was ultimately built and the understanding that made it possible>

the goal is to preserve the path, not just the destination. the path is where the real learning lives. if the initial approach worked on the first try, say so — that is itself a signal worth recording.

omit this section only if the work was a single-step change with no exploration. if anything was tried and discarded, it belongs here.

---

## concepts encountered

### <concept name>
- what it is: <explanation grounded in how it appeared in this work, not a textbook definition>
- where it appeared: <specific file, function, or decision>
- why it mattered: <what would have gone wrong without understanding it>
- how it affected the solution: <concrete effect on what was built or decided>

### <next concept>
...

---

## invisible knowledge gained

identify capabilities that now exist because of this work — things you can now do or recognize that you could not before. these are not concepts encountered. a concept is something you met. an ability is something you can now exercise.

examples of the distinction:
- "learned about godot signal lifecycle" → concept encountered
- "can now debug duplicate signal connections by reading connection count before `_ready()` fires" → invisible knowledge gained

- **<ability>**: <what you can now do, detect, or reason about that you couldn't before — stated as a capability, not a topic>

the "invisible" in the name matters. these are abilities that won't appear on a diff, won't be in the commit message, and won't be in the review. they exist only in the developer's head. writing them down makes them visible and transferable.

if this session produced no new capabilities — only reinforced existing ones — say so. "confirmed existing understanding of X" is a valid entry.

---

## engineering lessons

critical rule applied to every item: if this could have been written without reading the work, it is deleted.

- **<specific lesson>**: <the concrete situation that produced this lesson — name the file, function, or decision. explain the mechanism. explain what it means for future work of this type.>

- **<next lesson>**: ...

---

## mistakes made

- **<mistake>**: what was assumed → what was true → why the assumption was wrong → how long or how much effort was wasted → what would have caught it earlier

---

## tradeoffs

### <decision name>
- chosen: <what was built>
- rejected: <what was considered and not built>
- benefits of chosen: <specific to this situation>
- costs of chosen: <what it cannot do, or what it will cost later>
- why the choice was made: <the actual reason, not the polished reason>

---

## architectural insights

- **<insight about coupling, boundaries, ownership, or data flow>**: <what the work revealed, where the evidence is, why it matters for future work in this area>

---

## debugging insights
(include only if debugging occurred — omit this section entirely if not)

- root cause: <what was actually wrong>
- why it happened: <the upstream condition that made this possible>
- detection: <what signal would have revealed this earlier, and where that signal exists in the system>
- prevention: <the specific change — to code, to process, or to mental model — that would prevent recurrence>

---

## mental models

- **<system or subsystem name>** behaves like <analogy>. specifically: <what the analogy explains correctly>. where it breaks down: <where the analogy fails — state this to prevent wrong predictions>.

---

## future prediction

based on what this work revealed, identify what is now easier to predict about the future of this system. the goal is developing engineering foresight — the ability to see problems before they arrive.

- **future bugs now easier to predict**: <specific scenario where a bug is likely to surface, grounded in what the work exposed. name the mechanism: "if X is called before Y initializes, the result will be Z because...">
- **future maintenance risks**: <what will be painful to change later, and why — tied to a specific coupling or design decision made or observed during this work>
- **future scaling risks**: <what will break or degrade under increased load, users, content volume, or team size — specific to what was built or touched>
- **future architecture pressures**: <what will push toward a redesign of this area, and what the trigger condition likely looks like>

quality rule: every prediction must be traceable to something observed in the actual work. "this will be hard to scale" without a mechanism is not a prediction — it is a worry. "the dialogue loader reads the full json file on every scene load; once dialogue files exceed ~500 lines this will cause noticeable hitching on mobile because there is no lazy loading and no caching" is a prediction.

omit sub-items that genuinely don't apply. not every session surfaces all four risk types.

---

## future learning

ranked by leverage: how much would understanding this concept improve the next similar session?

1. **<concept>** — leverage: <high/very high>. why: <specific gap this work exposed that studying this would close>. good starting point: <a specific resource type, not a generic "read the docs">
2. **<concept>** — leverage: <medium/high>. why: ...
3. **<concept>** — leverage: <medium>. why: ...

---

## teach-back challenge

quality standard for all questions: answerable only by reasoning about the system, not by looking at the diff. if a question can be answered by ctrl+f, rewrite it.

### beginner (explain why)
1. <question that requires understanding the purpose of a decision, not recall of what the decision was>
2. <question that requires explaining a tradeoff in plain terms>
3. <question that requires connecting a concept to the problem it solved>

### intermediate (what would happen if)
1. <question that requires understanding how two parts of the system interact>
2. <question that requires reasoning about a change to one part and its effect on another>
3. <question that requires identifying the boundary where a technique applies vs where it breaks down>

### architect-level (how would you design)
1. <question that requires synthesizing the full picture of the work and projecting it into a new situation — one that didn't exist in the actual work>

---

## project-specific insights

extract what this work taught about this specific project — not about software in general. answer these three questions with evidence from the actual work:

- **what did this teach us about this project?** <something about how this project works, is structured, or behaves that was not obvious before this session — and the specific moment or finding that revealed it>
- **what assumptions about this project changed?** <a belief held going in that turned out to be wrong, incomplete, or more nuanced than expected — name the old assumption, the new understanding, and what corrected it>
- **what parts of the project became clearer?** <areas that were previously opaque or uncertain that now make sense — be specific about what was unclear and what made it click>

the distinction from engineering lessons: engineering lessons are transferable to other projects. project-specific insights are about this codebase, this team's decisions, this domain. they answer "what is true here" not "what is true in general."

if all three questions produce the same answer ("i understand the dialogue system better"), the section is too vague. each question should surface a different dimension of project understanding.

---

## permanent knowledge

filter applied: valuable after the implementation details are forgotten. written in the form: "when X, expect Y because Z."

- when <situation or condition>, <what to expect or do> because <the underlying mechanism that makes this true>. (evidence from this work: <brief anchor>)

---
```

end your chat reply with: the one most important engineering lesson from this session, and the file path. nothing else.

## stop conditions

- do not write code. if you notice something that should be changed while reading the diff, add it to mistakes or architectural insights — do not fix it.
- do not produce implementation plans. if a lesson implies future work, note it in future learning — do not plan the work.
- do not evaluate quality or suggest improvements. that is `review`. this skill extracts understanding, not recommendations.
- do not produce generic lessons. if the engineering lessons section contains "always test edge cases," delete it before writing the file. every item in that section needs a specific anchor in the actual work.
- if the inputs are too sparse to produce grounded lessons (only a one-line description, no diff, no review), say so clearly, produce what is possible, and mark low-confidence sections explicitly.
- stop after the file is written and the one most important lesson + file path are reported.

## quality standards

**engineering lessons:** every item needs an anchor. the anchor is the specific file, function, decision, or moment in the work that produced the lesson. no anchor = no lesson. delete and find the specific version.

**mistakes:** honesty is the quality standard. a learning report with no mistakes either covered work that was perfect (rare) or covered work that was not examined honestly (common). if the work was clean, say so and explain why — it is itself a lesson. but examine carefully before concluding there were no mistakes.

**mental models:** the "where it breaks down" field is mandatory. a mental model without a stated failure boundary is a trap — a developer will apply it confidently in a situation where it fails. if you cannot state where the analogy breaks down, the analogy is not understood well enough to include.

**teach-back questions:** the test for a good question is: can a developer who read the diff but didn't build the feature answer it? if yes, it is too easy. a good question requires the understanding that only comes from building or reasoning deeply about the system.

**permanent knowledge:** apply the filter twice. first pass: remove anything that could have been written before this project started. second pass: remove anything that only applies to this specific code and nowhere else. what remains is the target.

**decision timeline:** the pivot moment is the most important entry — it names what changed the developer's understanding. "tried X, switched to Y" is a timeline entry. "tried X, which failed because the godot scene tree isn't initialized at `_init()` time — only at `_ready()`, switched to Y which defers the connection" is a decision timeline entry. the pivot explanation must name the specific cause.

**invisible knowledge gained:** every item is stated as a capability, not a topic. the test: does it start with "can now" or "able to"? if it starts with "understands" or "learned about," it belongs in concepts encountered, not here. "can now identify duplicate signal connections before they manifest by checking `get_signal_connection_list()` length in `_ready()`" is an ability. "understands godot signal lifecycle" is a concept.

**future prediction:** every prediction needs a mechanism. "this will be hard to maintain" is not a prediction. "adding a new dialogue speaker will require updating three separate match statements in `dialogue_manager.gd` because speaker routing is not data-driven" is a prediction with a mechanism, a trigger condition, and a specific location.

**project-specific insights:** the three questions must produce three distinct answers. if they overlap significantly, the section hasn't been examined carefully enough. the distinction from engineering lessons: engineering lessons generalize. project-specific insights apply specifically here and may not generalize at all.

**section completeness:** not every section applies to every session. debugging insights should be omitted entirely when no debugging occurred. decision timeline should be omitted only for genuinely single-step changes. invisible knowledge should record "confirmed existing understanding of X" rather than being left empty. the report should reflect what happened, not fill every template field.

## examples

good — engineering lesson: "in godot's signal system, connecting a signal inside `_ready()` without checking `is_connected()` first causes duplicate connections when the scene is reloaded. the duplicate-advance bug in `dialogue_box.gd` traced to exactly this: `line_advanced` was connected twice on scene reload, advancing two lines per input. detection: a print statement in the signal callback was the only clue — godot does not warn on duplicate connections by default."

bad — engineering lesson: "always be careful with signals in godot." (this could have been written before the diff existed. it has no anchor. it teaches nothing specific.)

good — permanent knowledge: "when a system has multiple callers that each partially understand its state, adding a new state dimension requires updating every caller simultaneously or introducing a transition period where they disagree. in this case, adding `paused` state to the dialogue system required finding four places that checked `is_active` and updating each. the cost was proportional to the number of callers, not to the complexity of the state itself."

bad — permanent knowledge: "state management is complex and requires careful design." (true of all software, everywhere, always. teaches nothing.)

good — teach-back question (architect): "the current dialogue skip is implemented as a hold-duration check inside `_input()`. if the game required skip to work identically on both keyboard and touch, what would need to change, and where would the new abstraction need to live to avoid duplicating the hold-duration logic?"

bad — teach-back question: "how does dialogue skip work?" (answered by reading the diff. tests memory, not understanding.)
