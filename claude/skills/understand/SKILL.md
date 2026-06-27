---
name: understand
description: Use to rapidly build an accurate mental model of an unfamiliar codebase. Maps major systems, entry points, data flow, execution paths, and architectural decisions, then produces a structured report on disk. Use when first opening a repository, before implementing a feature, before refactoring, before architecture work, before debugging a system you don't know, or before contributing to an open source project. Trigger phrases: "understand this codebase", "explain this repo", "map this project", "how does this work", "onboard me to this", "what is this project", "where do I start". Do NOT use for implementation, code generation, bug fixing, or code review — this skill only builds understanding, it does not produce changes.
user-invocable: true
---

# understand

read the codebase systematically and produce an accurate mental model. the goal is not to list files — it is to explain how the system thinks, where it draws its boundaries, and where things actually live versus where a new developer would expect them to live.

## purpose

eliminate the weeks of confusion that come from starting in the wrong place. a developer who reads a codebase file by file, alphabetically or randomly, will have a partial, misleading mental model for weeks. this skill forces a structured approach: start from the manifest, find the entry points, trace execution, map boundaries, then report with honest gaps.

the secondary purpose: produce a durable artifact the other skills can use. `architect` and `implement` are faster when this report already exists — they don't have to rediscover module boundaries and dependency graphs before making a change.

## when to use

- first session with an unfamiliar repository
- before any architect, implement, or refactor session in an unfamiliar area
- before debugging a system whose behavior surprises you
- before contributing to an open source project you haven't read before
- when the developer says: "explain this repo", "how does this work", "onboard me", "where do i start"
- after returning to a project after a long gap — run understand again to verify the mental model is still accurate

## when NOT to use

- you already have an understanding report for this codebase that is less than a month old and the codebase hasn't changed significantly — read the existing report instead, update it if needed
- implementation: writing code, generating boilerplate, fixing bugs → those are `implement`, `game-implement`, or direct work
- code review: evaluating a diff → that is `review`
- architecture: proposing changes → that is `architect`. understand comes before architect; understand does not become architect

if someone asks "understand this and then tell me how to fix X," complete the understanding first, write the report, stop — then a separate session handles the fix.

## inputs

- the repository root (read the directory tree first, before any individual file)
- any existing understanding reports in `.claude/work/understanding/` (read them — update, don't duplicate)
- any `CLAUDE.md`, `README.md`, `CONTRIBUTING.md`, `ARCHITECTURE.md`, or `docs/` that exist
- the developer's stated goal if they have one ("i need to add X" or "i'm debugging Y") — use it to prioritize which paths to trace

if the developer names a specific area ("just understand the auth system"), scope the report to that area and name the scope explicitly at the top.

## process

follow this order. the order matters — later steps depend on earlier ones.

**step 1: read the manifest files.**
find and read: `package.json`, `pyproject.toml`, `setup.py`, `Cargo.toml`, `go.mod`, `pom.xml`, `build.gradle`, `project.godot`, `*.csproj`, or whatever dependency manifest exists. these give you: what this project declares itself to be, its external dependencies (which reveal its domain), and its declared entry points. if no manifest exists, note it — that itself is a structural signal.

**step 2: read the top-level directory structure.**
`view` the root, then each top-level directory. do not read individual files yet. build the shape: what are the named areas of the codebase? what's missing that you'd expect? what's present that's surprising? top-level directory names are the system's vocabulary — read them before reading code.

**step 3: read documentation.**
read `README.md`, `CLAUDE.md`, `CONTRIBUTING.md`, `ARCHITECTURE.md`, any file in `docs/`. if documentation contradicts what you see in the structure, that contradiction is a finding. if documentation is absent, name it — absence of docs in a large codebase is information.

**step 4: find and read entry points.**
entry points are where execution begins. common locations: `main.py`, `index.js`, `app.py`, `server.py`, `cmd/`, `src/main.rs`, `__main__.py`, `bin/`, `entrypoint.*`. for godot projects: the main scene. for libraries: the public API surface (`__init__.py`, `mod.rs`, `index.ts`). read entry points fully — they reveal the system's shape from the outside.

**step 5: trace the primary execution path.**
pick the most important user action or request type and trace it from entry point to output. follow the code — do not describe what you expect, describe what you read. trace at least one complete path. for a web server: one request from router to response. for a CLI: one command from parse to exit. for a game: one frame from input to render. name every function call boundary you cross and every file you enter.

**step 6: map module boundaries.**
after tracing execution, you have touched most of the important modules. now map them: what is each module responsible for? what does it import from other modules? draw the dependency direction — does module A know about module B, or is it the other way? identify any circular dependencies. identify any module that "knows too much" (imports from many other modules).

**step 7: sample the tests.**
read the test directory structure and 3-5 representative test files. tests reveal intended behavior and correct usage better than source code, because tests are written from the outside. note: what is tested heavily? what is not tested at all? what does the test structure suggest about what the developers thought was risky?

**step 8: identify patterns and history signals.**
having read entry points, traced execution, and sampled tests, do two things. first, name the architectural patterns in use. common ones: dependency injection, event bus / signals, repository pattern, command pattern, factory, singleton, observer, layered architecture, hexagonal / ports and adapters. for each pattern found: name where it's used, why it's likely there, and what to expect if you're adding to that area. second, scan for architectural history signals — dead code, duplicate systems, TODO/FIXME clusters, partially adopted abstractions. these are evidence of what the codebase used to be, which explains constraints in what it is now.

**step 9: write the report. stop.**

## for large codebases

when you cannot read everything (projects with hundreds of files):
- prioritize entry points and execution traces over utility files
- prioritize interface files (abstract classes, protocols, `.d.ts`, trait definitions) over concrete implementations — interfaces describe the contract, implementations fill it
- prioritize test files with descriptive names over source files with unclear names — `test_auth_invalid_token.py` tells you more than `auth_utils.py` often does
- use directory listings at multiple levels to understand shape before drilling into any single file
- explicitly record in the understanding level section which areas were NOT read. a gap named is a gap that can be filled; a gap unnamed becomes a false assumption

## outputs

write to `.claude/work/understanding/YYYY-MM-DD-short-slug.md`. use a name that identifies the codebase or area: `2026-06-22-repodanta.md`, `2026-06-22-sphural-dialogue-system.md`. format:

---

```
# understanding: <repository or system name>
date: <date>   scope: <full repository | specific area: name>
read depth: <deep | sampled | surface>   files read: <count or range>

## one-line summary
<what this repository is, in one sentence, written for someone who has never heard of it>

## repository summary

### what is this?
<2-3 sentences: what problem it solves, for whom, in what context>

### what kind of system is it?
<library / CLI tool / web server / game / data pipeline / framework / other>

### primary users
<who runs or uses this? developers? end users? other systems?>

### how mature is it?
<signal from: test coverage, documentation quality, commit history if visible, version number, presence of deprecation warnings, presence of TODO/FIXME density>

---

## architecture overview

### major systems
| system | responsibility | primary files |
|--------|---------------|---------------|
| <name> | <what it does> | <key files> |

### module boundaries
<which modules exist, what each owns, and critically: what each does NOT own>

### dependency direction
<which modules import which — described as arrows. example: "api → service → repository → database. nothing imports from api except the entry point.">

### dependency map (text)
<a simple text diagram of the key dependencies. no need for graphviz — plain indented text is fine>

---

## architectural history signals

identify evidence that the codebase changed shape over time. the goal is to reconstruct what likely happened — not to judge it.

look for:
- **abandoned approaches**: dead code, commented-out blocks, modules that are imported but never called, config keys with no readers, feature flags that are always false
- **migrations in progress**: two systems doing the same job (e.g. two ORM patterns, two routing styles, two auth mechanisms), one newer and one older, both still present
- **legacy systems**: modules with significantly older naming conventions, documentation styles, or patterns than the surrounding code — signals they predate a team or philosophy change
- **technical debt markers**: dense clusters of TODO/FIXME/HACK comments, unusually defensive error handling in one area vs none in others, a module with 10x the line count of its peers
- **unfinished refactors**: a new abstraction that is partially adopted — some callers use it, others still use the old approach directly. the boundary between old and new callers marks the refactor's waterline

for each signal found:
- what is the evidence (specific file or pattern)
- what likely happened (the story behind the evidence)
- what it means for a developer making changes today (is this area safe to touch? is it load-bearing despite looking abandoned?)

if no history signals are detectable (clean, consistent codebase), say so — that itself is useful information about the project's age and team discipline.

---

## execution flow

### entry points
- <file/function> — <what triggers it> — <when to read it>

### primary execution path
<trace one complete request or action, step by step, naming each file and function boundary crossed. be specific: "request hits `server.py:app()` → routed to `routes/auth.py:login()` → calls `services/auth_service.py:authenticate()` → queries `repositories/user_repo.py:find_by_email()` → returns JWT from `utils/token.py:generate()`">

### other important paths
- <path name> — <trigger> — <brief trace>

---

## important files

ranked by: "reading this file gives you the most understanding per minute."

| rank | file | why it matters | when to read it |
|------|------|----------------|-----------------|
| 1 | <path> | <specific reason> | <first / after X / when debugging Y> |
| 2 | ... | | |

---

## learning path

a recommended reading order for a developer starting from zero. ordered by dependency — read A before B because understanding A is required to understand B.

1. `<file>` — <why first: what it establishes that everything else depends on>
2. `<file>` — <what this adds to the picture built by step 1>
3. ...

---

## core concepts

the abstractions and decisions a developer must internalize to work effectively here.

### <concept name>
- what it is: <specific to this codebase, not a textbook definition>
- why it exists: <the problem it solves — why wasn't the simpler approach used?>
- where it appears: <files or modules>
- consequences: <what a developer who doesn't understand this will get wrong>

---

## mental model

<a plain-language explanation of how this system works, written for a smart developer who has never seen this codebase. use an analogy if it clarifies — but only if the analogy is accurate, not just vivid. the test: can a developer read only this section and immediately ask correct, useful questions about the system?>

---

## common mistakes

things a new developer will get wrong — specific to this codebase, not generic advice. each item must come from something actually read in the code, not from general developer wisdom.

- **<misleading name or concept>**: new developers assume X. the codebase actually does Y. evidence: `<file:line or module>`.
- **<hidden coupling>**: modules A and B appear independent but share Z. breaking one breaks the other. evidence: `<specific>`.
- **<surprising behavior>**: when you do X, you expect Y but get Z. the reason is `<specific>`.
- **<wrong starting point>**: developers new to this system typically start by reading `<file>`. the correct starting point is `<file>` because `<reason>`.

if fewer than three genuine mistakes are found, say so — do not invent generic ones to fill the section.

---

## investigation questions

questions to deepen understanding beyond this report. not rhetorical — these should guide real reading sessions.

### beginner (what does X do?)
- <specific, answerable by reading one file>
- ...

### intermediate (why does X work this way?)
- <requires understanding two or more modules to answer>
- ...

### architect-level (what would happen if X changed?)
- <requires understanding the full system to answer>
- ...

---

## codebase map

where things live — for a developer who needs to make a change.

| concern | where it lives | notes |
|---------|---------------|-------|
| feature business logic | <path> | |
| data models / schemas | <path> | |
| configuration | <path> | |
| entry points / routing | <path> | |
| external integrations | <path> | |
| tests | <path> | |
| utilities / helpers | <path> | |
| build / deploy | <path> | |

---

## leverage points

if you only had 2 hours to understand this repository, where should you spend them?

identify the files and modules where understanding is disproportionately valuable — not because they are the most complex, but because everything else depends on or flows through them.

### high-impact modules
<modules where a change or a misunderstanding has the widest blast radius. if you get these wrong, things break in unexpected places.>

| module | why it's high-impact | what depends on it |
|--------|---------------------|-------------------|
| <name> | <specific reason> | <what breaks if this is wrong> |

### central abstractions
<the 1-3 concepts or classes that appear in every other module. understanding these unlocks the rest. examples: a base class all handlers extend, a config object passed everywhere, a core data type everything transforms.>

- **<abstraction>**: <what it is, where it lives, why everything touches it>

### bottlenecks
<places where all requests, events, or data must pass through — by design or by accident. these are both the highest-leverage points for understanding and the highest-risk points for making changes.>

- **<bottleneck>**: <what flows through it, why it exists here, what breaks if it's slow or wrong>

### the 2-hour reading list
if time is limited, read these in this order:
1. `<file>` — <what understanding this file unlocks that nothing else does>
2. `<file>` — ...
3. `<file>` — ...

---

## where to make changes

for a developer who needs to do something specific — where do they start?

| task | primary files | secondary files | watch out for |
|------|--------------|-----------------|---------------|
| add a new feature | <path(s)> | <path(s)> | <coupling risk or convention to follow> |
| fix a bug | <path(s)> | <path(s)> | <where bugs in this system typically hide> |
| change behavior | <path(s)> | <path(s)> | <what else changes when this changes> |
| change configuration | <path(s)> | <path(s)> | <env vars, defaults, validation locations> |
| add or modify UI | <path(s)> | <path(s)> | <theming, state, or routing implications> |
| add tests | <path(s)> | <path(s)> | <what the test infrastructure requires> |

notes:
- "watch out for" is the most important column. fill it with specific risks from this codebase, not generic advice.
- if a task type doesn't apply (e.g. no UI in a CLI tool), remove that row rather than leaving it blank.

---

## understanding level

an honest assessment of what is known and unknown after this session.

| dimension | status | confidence |
|-----------|--------|------------|
| entry points | mapped / partial / unknown | high / medium / low |
| primary execution path | traced / partial / unknown | |
| module boundaries | clear / ambiguous / unknown | |
| data flow | traced / partial / unknown | |
| key abstractions | understood / partial / unknown | |
| test coverage picture | clear / partial / unknown | |
| configuration and environment | understood / partial / unknown | |
| architectural history | read / inferred / unknown | |

**current level: <one of the below>**

- **exploratory** — you understand what the repository is and can find your way around the directory structure. you could not yet make a change confidently.
- **operational** — you understand the primary execution path and can make a targeted change in a known area without breaking things unexpectedly.
- **contributor** — you understand the module boundaries, the central abstractions, and the leverage points. you can add a feature in an unfamiliar area and know where to look when something breaks.
- **maintainer** — you understand the architectural history, the tradeoffs behind major decisions, and where technical debt lives. you can refactor safely and review others' changes meaningfully.
- **architect** — you understand the forces that shaped the system, can reason about its future, and can identify what would need to change if a core constraint changed.

### what is still unknown
- <specific gap> — <how to close it: read file X, run command Y, ask someone about Z>

### what would raise the level
- <concrete next read or action that would move from current level to the next>

---

end your chat reply with: the one-line summary, the mental model paragraph, and the file path. nothing else.
```

---

## stop conditions

- do not write code. not a single line. if you notice a bug while reading, add it to common mistakes or unknown gaps — do not fix it.
- do not suggest refactors. if the architecture seems wrong, that is a finding in common mistakes or investigation questions — it is not your job to redesign it here.
- do not suggest new features. your job is to understand what exists, not to imagine what could.
- do not produce implementation plans. if the developer's goal is "understand so i can add X", produce the understanding report and stop. the implementation planning is a separate session with `architect` or `game-architect`.
- if mid-reading you find the codebase is smaller than expected and the full picture is clear in 10 minutes — write a shorter honest report, not a padded long one. the report length should match the complexity of what was read.
- if mid-reading the codebase is far larger than expected and full coverage is impossible — write the report for what was actually read, be explicit in the understanding level about what was not covered, and recommend specific next reads.

## quality standards

- the mental model passes this test: a developer who reads only that section can immediately ask correct, useful questions. if the mental model could describe a different codebase of the same type, it is too generic. rewrite it.
- the learning path is ordered by dependency, not by importance. importance is a property of files; dependency is a property of understanding. "read A before B because B won't make sense without A" is a learning path. "read A first because it's the most important" is a list.
- common mistakes must be evidenced. every item must name the file, line, or module where the evidence was found. "the naming is confusing" is not a common mistake. "the `parser.py` module does not parse — it validates already-parsed data; the actual parsing is in `lexer.py:tokenize()`" is a common mistake.
- the understanding level gaps are specific and actionable. "more could be learned" is not a gap. "the `scripts/migrate.py` file was not read — it likely contains the database migration logic which affects the data model section above" is a gap.
- architectural history signals must be evidenced. "this looks old" is not a signal. "two authentication patterns coexist — `middleware/auth.py` uses JWT, `legacy/session.py` uses cookies, and both are imported by the router — this is a migration that hasn't finished" is a signal with a story and an implication.
- leverage points are ranked honestly. the highest-leverage file is not always the largest or the most complex — it is the one where understanding it unlocks the most other things. name the specific unlock.
- where to make changes uses specific paths, not categories. "the service layer" is not a path. `src/services/auth_service.py` is. the "watch out for" column must contain a risk specific to this codebase, not generic caution.
- the primary execution trace names every file and function boundary crossed. not "the request is processed" — "the request hits `app.py:create_app()` → middleware stack in `middleware/` → routed to `api/v1/routes.py:register()` → handler in `api/v1/handlers/auth.py:login()`."
- the report is scoped honestly. if only the auth system was read, the title says "auth system" and the scope field says so. a report titled "full codebase" that only covers 20% of the code is a false confidence machine.

## examples

good: developer opens repodanta for the first time. understand reads `pyproject.toml` (finds: python project, faiss, click, ast — signals CLI tool with semantic search on code), reads `README.md` (confirms: codebase intelligence tool), reads directory structure (finds: `src/repodanta/`, `tests/`, `scripts/`), finds entry point at `src/repodanta/__main__.py`, traces "repodanta query X" from CLI parse → `graph_builder.py:build_graph()` → `retriever.py:search()` → `faiss` index → formatted output. maps three modules: graph building (AST), retrieval (FAISS + embeddings), CLI (click). samples 3 test files. mental model: "repodanta works like a read-only IDE that has already indexed the codebase — you query it in natural language and it finds the relevant code by meaning, not by text match. the graph_builder runs once to parse and index; the retriever runs on every query against that index." architectural history signals: "two retrieval methods coexist — `retriever.py` uses FAISS vector search, but `retriever_legacy.py` uses simple keyword grep and is still imported in `__main__.py` as a fallback — unfinished migration." leverage points: `retriever.py` is the central abstraction — every query flows through it; understanding it unlocks how indexing, search, and ranking connect. where to make changes: add a new query type → `retriever.py:search()` + `__main__.py` CLI registration; fix a ranking bug → `retriever.py:rank()`. common mistakes: "the module named `loader.py` doesn't load files — it validates the AST output from `graph_builder.py`. actual file loading is in `graph_builder.py:_read_source()`." understanding level: contributor. gap: `scripts/` directory not read — may contain index persistence logic that affects the data model section.

bad: understand produces a directory listing with one sentence per file ("main.py — the main entry point. config.py — configuration. utils.py — utilities."), writes a mental model that says "this is a python project that processes code and returns results," lists no common mistakes, claims understanding level "contributor" with no gaps named. a developer reading this report learns nothing they couldn't get from `ls -la`.
