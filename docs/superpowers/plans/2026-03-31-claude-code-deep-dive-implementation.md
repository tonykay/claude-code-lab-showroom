# Claude Code Deep Dive Course — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a 10-module + capstone AsciiDoc course teaching Claude Code architecture through interleaved theory, introspection, and a progressive build project (Personal Learning Path Manager).

**Architecture:** Each module is an AsciiDoc page in `content/modules/ROOT/pages/` following a three-part structure (How It Works / See It In Action / Build With It). Modules are numbered for ordering. Navigation is defined in `nav.adoc`. The cached architecture site at `assets/site/` is the primary source for architectural content.

**Tech Stack:** Antora 3.1, AsciiDoc, Mermaid diagrams, Showroom template

**Spec:** `docs/superpowers/specs/2026-03-31-claude-code-deep-dive-course-design.md`

---

## File Map

### Files to Create
- `content/modules/ROOT/pages/index.adoc` — Course landing page (replaces existing)
- `content/modules/ROOT/pages/00-prerequisites.adoc` — Prerequisites and setup verification
- `content/modules/ROOT/pages/01-agentic-loop.adoc` — Module 1: The Agentic Loop
- `content/modules/ROOT/pages/02-context-memory.adoc` — Module 2: Context & Memory
- `content/modules/ROOT/pages/03-tools-dispatch.adoc` — Module 3: Tools & Tool Dispatch
- `content/modules/ROOT/pages/04-permissions-trust.adoc` — Module 4: Permissions & Trust
- `content/modules/ROOT/pages/05-hooks-lifecycle.adoc` — Module 5: Hooks & Lifecycle
- `content/modules/ROOT/pages/06-skills-workflows.adoc` — Module 6: Skills & Workflows
- `content/modules/ROOT/pages/07-extending-claude-code.adoc` — Module 7: Extending Claude Code
- `content/modules/ROOT/pages/08-mcp-servers.adoc` — Module 8: MCP Servers
- `content/modules/ROOT/pages/09-multi-agent.adoc` — Module 9: Multi-Agent Patterns
- `content/modules/ROOT/pages/10-sdk-integration.adoc` — Module 10: SDK & Integration
- `content/modules/ROOT/pages/11-capstone.adoc` — Capstone: Putting It All Together

### Files to Modify
- `content/modules/ROOT/nav.adoc` — Replace with course navigation
- `content/antora.yml` — Update title, remove Showroom-specific attributes, add course attributes
- `ui-config.yml` — Update tabs for course context
- `CLAUDE.md` — Update to reflect course content (already created, may need updates)

### Files to Delete
- All existing template pages in `content/modules/ROOT/pages/` except `index.adoc` (which gets overwritten):
  `agnosticv-config.adoc`, `architecture.adoc`, `attribute-example.adoc`, `content-repo.adoc`, `contributing.adoc`, `deployer.adoc`, `images.adoc`, `nookbag.adoc`, `ocp-integration.adoc`, `ocp4-role-reference.adoc`, `quick-start.adoc`, `ui-config.adoc`, `user-data.adoc`, `vm-role-reference.adoc`

### Source Files (read-only reference)
- `assets/site/concepts/how-it-works.html` → Module 1
- `assets/site/concepts/memory-context.html` → Module 2
- `assets/site/configuration/claudemd.html` → Module 2
- `assets/site/configuration/settings.html` → Modules 2, 4
- `assets/site/concepts/tools.html` → Module 3
- `assets/site/reference/tools/*.html` → Module 3
- `assets/site/concepts/permissions.html` → Module 4
- `assets/site/guides/hooks.html` → Module 5
- `assets/site/reference/sdk/hooks-reference.html` → Module 5
- `assets/site/guides/skills.html` → Module 6
- `assets/site/guides/mcp-servers.html` → Module 8
- `assets/site/guides/multi-agent.html` → Module 9
- `assets/site/reference/tools/agent.html` → Module 9
- `assets/site/reference/sdk/overview.html` → Module 10
- `assets/site/reference/sdk/permissions-api.html` → Module 4

---

## Task 1: Project Setup — Clean Template and Configure

**Files:**
- Delete: all 14 template `.adoc` pages listed above
- Modify: `content/modules/ROOT/nav.adoc`
- Modify: `content/antora.yml`
- Modify: `ui-config.yml`

- [ ] **Step 1: Delete template content pages**

Remove all existing Showroom template pages. Keep `index.adoc` (will be overwritten in Task 2).

```bash
cd content/modules/ROOT/pages
rm agnosticv-config.adoc architecture.adoc attribute-example.adoc content-repo.adoc contributing.adoc deployer.adoc images.adoc nookbag.adoc ocp-integration.adoc ocp4-role-reference.adoc quick-start.adoc ui-config.adoc user-data.adoc vm-role-reference.adoc
```

- [ ] **Step 2: Update `content/antora.yml`**

Replace the Showroom-specific configuration with course configuration:

```yaml
name: modules
title: "Claude Code Deep Dive: Zero to Hero"
version: ~
nav:
  - modules/ROOT/nav.adoc

asciidoc:
  attributes:
    experimental: true
    page-pagination: true
    icons: font
    source-highlighter: rouge
    # Course metadata
    course-version: v0.1.0
    claude-code-docs-url: https://code.claude.com/docs
    claude-code-transcripts-url: https://github.com/simonw/claude-code-transcripts
    claude-howto-url: https://github.com/luongnv89/claude-howto
    claude-code-tips-url: https://github.com/ykdojo/claude-code-tips
    # Build project defaults
    project-name: learnpath
    python-version: "3.13"
```

- [ ] **Step 3: Update `content/modules/ROOT/nav.adoc`**

Replace with the course navigation structure:

```asciidoc
* xref:index.adoc[Course Overview]
* xref:00-prerequisites.adoc[Prerequisites]

.Foundations
* xref:01-agentic-loop.adoc[Module 1: The Agentic Loop]
* xref:02-context-memory.adoc[Module 2: Context & Memory]
* xref:03-tools-dispatch.adoc[Module 3: Tools & Tool Dispatch]
* xref:04-permissions-trust.adoc[Module 4: Permissions & Trust]

.Automation & Extension
* xref:05-hooks-lifecycle.adoc[Module 5: Hooks & Lifecycle]
* xref:06-skills-workflows.adoc[Module 6: Skills & Workflows]
* xref:07-extending-claude-code.adoc[Module 7: Extending Claude Code]

.Advanced Patterns
* xref:08-mcp-servers.adoc[Module 8: MCP Servers]
* xref:09-multi-agent.adoc[Module 9: Multi-Agent Patterns]
* xref:10-sdk-integration.adoc[Module 10: SDK & Integration]

.Wrap-Up
* xref:11-capstone.adoc[Capstone: Putting It All Together]
```

- [ ] **Step 4: Update `ui-config.yml`**

```yaml
type: showroom
default_width: 30
persist_url_state: true
view_switcher:
  enabled: true
  default_mode: split

tabs:
  - name: Claude Code Docs
    url: 'https://code.claude.com/docs'
  - name: Claude Code Transcripts
    url: 'https://github.com/simonw/claude-code-transcripts'
```

- [ ] **Step 5: Build and verify**

```bash
podman run --rm --name antora -v $PWD:/antora -p 8080:8080 -i -t ghcr.io/juliaaano/antora-viewer
```

Expected: Site builds. Navigation shows course structure. Only `index.adoc` has content (the old Showroom content — will be replaced next task). All other nav links lead to missing-page errors (expected at this stage).

- [ ] **Step 6: Commit**

```bash
git add -A content/modules/ROOT/pages/ content/antora.yml content/modules/ROOT/nav.adoc ui-config.yml
git commit -m "refactor: replace showroom template with claude code deep dive course structure"
```

---

## Task 2: Course Landing Page and Prerequisites

**Files:**
- Create: `content/modules/ROOT/pages/index.adoc` (overwrite existing)
- Create: `content/modules/ROOT/pages/00-prerequisites.adoc`

- [ ] **Step 1: Write `index.adoc` — Course Landing Page**

Read `assets/site/index.html` for tone and scope context. Write the landing page with:

```asciidoc
= Claude Code Deep Dive: Zero to Hero
:navtitle: Course Overview
:source-highlighter: rouge
:icons: font

== What This Course Is

[intro paragraph: architecture-driven deep dive for experienced developers. Not a tutorial — a course that teaches how Claude Code works and why, so you can use it effectively.]

== What You Will Build

[describe the Personal Learning Path Manager — full-stack app (Python 3.13/uv, FastAPI, Pydantic V2, PostgreSQL, React) built progressively across 10 modules]

== How Each Module Works

Each module follows a three-part rhythm:

. *How It Works* — architectural deep dive with diagrams
. *See It In Action* — introspection exercises using `claude-code-transcripts` and `~/.claude/` inspection
. *Build With It* — apply the concept to the learning asset manager

== Module Map

[table listing all 10 modules + capstone with architecture concept and build milestone columns — draw from the spec's module map table]

== Prerequisites

Before starting, make sure you have everything listed on the xref:00-prerequisites.adoc[Prerequisites] page.

== Source Material

This course draws from:

* The {claude-code-docs-url}[official Claude Code documentation]
* An architectural reference site (included in this course's `assets/site/` directory)
* {claude-code-transcripts-url}[claude-code-transcripts] by Simon Willison — for session replay and introspection
* {claude-howto-url}[claude-howto] and {claude-code-tips-url}[claude-code-tips] — community-curated tips and patterns
```

- [ ] **Step 2: Write `00-prerequisites.adoc`**

```asciidoc
= Prerequisites
:navtitle: Prerequisites
:source-highlighter: rouge
:icons: font

== What We Assume

You are an experienced developer comfortable with:

* A Unix-like terminal (macOS or Linux)
* Git and GitHub
* Python basics (we use Python 3.13 with uv)
* Basic familiarity with REST APIs
* You have *used* Claude Code casually — run it a few times, had it edit files. You don't yet understand *how* it works under the hood.

== Required Software

[NOTE]
====
If Claude Code is not yet installed, follow the {claude-code-docs-url}[official installation guide] before continuing.
====

Verify your setup by running each command below:

[source,bash]
----
claude --version          # Claude Code CLI
python3 --version         # Python 3.13+
uv --version              # uv package manager
node --version             # Node.js 20+ (for React in Module 9)
psql --version             # PostgreSQL client
docker --version || podman --version  # Container runtime for Postgres
----

== Recommended: Install claude-code-transcripts

We use this throughout the course to replay and analyze Claude Code sessions:

[source,bash]
----
pipx install claude-code-transcripts
----

See {claude-code-transcripts-url}[the project page] for details.

== Optional: Postgres via Container

If you don't have PostgreSQL installed locally:

[source,bash]
----
docker run --name learnpath-db -e POSTGRES_USER=learnpath -e POSTGRES_PASSWORD=learnpath -e POSTGRES_DB=learnpath -p 5432:5432 -d postgres:16
----

== Ready?

Proceed to xref:01-agentic-loop.adoc[Module 1: The Agentic Loop].
```

- [ ] **Step 3: Build and verify**

```bash
podman run --rm --name antora -v $PWD:/antora -p 8080:8080 -i -t ghcr.io/juliaaano/antora-viewer
```

Expected: Landing page renders with course overview, module map table, and links. Prerequisites page renders with all verification commands. Attribute substitution works for URLs.

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/index.adoc content/modules/ROOT/pages/00-prerequisites.adoc
git commit -m "feat: add course landing page and prerequisites"
```

---

## Task 3: Module 1 — The Agentic Loop

**Files:**
- Create: `content/modules/ROOT/pages/01-agentic-loop.adoc`
- Source: `assets/site/concepts/how-it-works.html`

- [ ] **Step 1: Read source material**

Read `assets/site/concepts/how-it-works.html` completely. Extract:
- The six-step agentic loop cycle
- Context loading strategy (system context + user context)
- Operating modes (interactive REPL vs non-interactive)
- Storage and session resumption

- [ ] **Step 2: Write the module**

Structure:

```asciidoc
= Module 1: The Agentic Loop
:navtitle: The Agentic Loop
:source-highlighter: rouge
:icons: font

[prerequisites]
====
* Claude Code installed and working (`claude --version`)
* `claude-code-transcripts` installed (`pipx install claude-code-transcripts`)
====

== How It Works

=== The Six-Step Cycle

[explain the agentic loop with a mermaid diagram]

[mermaid]
----
graph TD
    A[1. User Input] --> B[2. Context Assembly]
    B --> C[3. Model Reasoning]
    C --> D[4. Permission Evaluation]
    D --> E[5. Tool Execution]
    E --> F{6. Done?}
    F -->|No| C
    F -->|Yes| G[Response]
----

[detailed explanation of each step, drawn from how-it-works.html]

=== Context Assembly Pipeline

[explain the two context blocks: system context (git status, date, tools) and user context (CLAUDE.md hierarchy). Mermaid diagram showing the assembly.]

=== Why It Loops

[explain why the agentic loop is not a single request/response — Claude Code makes multiple API calls, each time reassessing. This is what makes it an *agent*, not a chatbot.]

== See It In Action

=== Exercise 1: Watch the Loop

. Open a terminal in any project directory
. Run a simple task:
+
[source,bash,role="execute"]
----
claude "create a file called hello.py that prints hello world"
----
+
. Watch the output. Notice: Claude Code doesn't just write the file — it reasons, selects a tool (Write), executes it, then checks the result before responding.

=== Exercise 2: Replay with Transcripts

. After the task completes, export the transcript:
+
[source,bash,role="execute"]
----
claude-code-transcripts
----
+
. Open the transcript. Identify:
** Where context assembly happened (system prompt, CLAUDE.md loading)
** Each tool call (what tool, what arguments)
** The loop continuation decision (why did Claude make another API call, or why did it stop?)

=== Exercise 3: Observe Multi-Tool Loops

. Ask Claude Code to do something that requires multiple tools:
+
[source,bash,role="execute"]
----
claude "read the file hello.py, change the message to 'hello from claude code', and verify the change"
----
+
. Review the transcript. Count the tool calls. Map each one to a step in the six-step cycle.

== Build With It

=== Scaffold the Learning Asset Manager

Use Claude Code to scaffold the project. This is your first real interaction — watch the agentic loop as it works.

. Create a project directory:
+
[source,bash,role="execute"]
----
mkdir ~/learnpath && cd ~/learnpath
----
+
. Ask Claude Code to scaffold:
+
[source,bash,role="execute"]
----
claude "initialize a Python 3.13 project using uv with FastAPI and Pydantic V2. Create the project structure with a src/learnpath package, pyproject.toml, and a basic health check endpoint at /health. Use uv for dependency management."
----
+
. Verify:
+
[source,bash,role="execute"]
----
uv run uvicorn src.learnpath.main:app --reload
# Visit http://localhost:8000/health — should return {"status": "ok"}
----

=== Transcript Review

Export and review the transcript from the scaffolding task. Answer:

* How many agentic loop iterations did Claude Code make?
* Which tools did it select and in what order?
* At what point did it decide it was "done"?

[checkpoint]
====
*What you should have:*

* A working `~/learnpath` project with FastAPI health check endpoint
* At least two transcript exports you can reference
* An understanding of the six-step agentic loop from both theory and observation
====

== References

* {claude-code-docs-url}[Official Claude Code Documentation]
* link:../assets/site/concepts/how-it-works.html[Architecture Reference: How It Works] (local)
* {claude-code-transcripts-url}[claude-code-transcripts]

Next: xref:02-context-memory.adoc[Module 2: Context & Memory]
```

- [ ] **Step 3: Build and verify**

```bash
podman run --rm --name antora -v $PWD:/antora -p 8080:8080 -i -t ghcr.io/juliaaano/antora-viewer
```

Expected: Module 1 renders with mermaid diagrams, code blocks with execute role, prerequisites box, checkpoint box, and navigation links.

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/01-agentic-loop.adoc
git commit -m "feat: add module 1 — the agentic loop"
```

---

## Task 4: Module 2 — Context & Memory

**Files:**
- Create: `content/modules/ROOT/pages/02-context-memory.adoc`
- Source: `assets/site/concepts/memory-context.html`, `assets/site/configuration/claudemd.html`, `assets/site/configuration/settings.html`

- [ ] **Step 1: Read source material**

Read all three source files. Extract:
- 4-level CLAUDE.md hierarchy (system → user → project → local)
- Priority resolution (closer to CWD = higher priority)
- @include directive (relative/home/absolute paths, circular detection, max depth 5)
- Granular rules with `.claude/rules/*.md` and path-scoped frontmatter
- File discovery walk (CWD to filesystem root)
- Settings hierarchy (5 levels: plugin defaults → user → project → local → managed)
- 40k char limit per file

- [ ] **Step 2: Write the module**

Structure following the three-part rhythm:

**How It Works:**
- Mermaid diagram showing the 4-level memory hierarchy with priority arrows
- Mermaid diagram showing the file discovery walk from CWD upward
- Explanation of why later-loaded files get higher priority (model attention pattern)
- @include directive mechanics
- Granular rules with frontmatter globs
- Settings hierarchy (5-level) as separate mermaid diagram

**See It In Action:**
- Exercise 1: Examine `~/.claude/` directory — what's there? Identify settings.json, CLAUDE.md, projects/
- Exercise 2: Create CLAUDE.md at user level (`~/.claude/CLAUDE.md`) and project level, put conflicting instructions, observe which wins
- Exercise 3: Use `/memory` command to inspect loaded memory. Try `@include` directive
- Exercise 4: Create a `.claude/rules/python.md` with frontmatter `paths: ["**/*.py"]` and verify it only activates for Python files

**Build With It:**
- Write `~/learnpath/CLAUDE.md` with: build commands (`uv run uvicorn ...`), stack conventions (Pydantic V2, uv, Python 3.13), project architecture notes
- Create `.claude/settings.json` with initial configuration
- Create a `.claude/rules/api.md` rule scoped to `src/learnpath/api/**` for API conventions
- Transcript review: observe context assembly showing CLAUDE.md being loaded

**Checkpoint:** Working CLAUDE.md, settings.json, and at least one granular rule. Claude Code should follow the project conventions when asked to write code.

- [ ] **Step 3: Build and verify**

Expected: Module renders with all mermaid diagrams, exercise code blocks, and cross-references to Module 1.

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/02-context-memory.adoc
git commit -m "feat: add module 2 — context and memory"
```

---

## Task 5: Module 3 — Tools & Tool Dispatch

**Files:**
- Create: `content/modules/ROOT/pages/03-tools-dispatch.adoc`
- Source: `assets/site/concepts/tools.html`, `assets/site/reference/tools/*.html`

- [ ] **Step 1: Read source material**

Read `concepts/tools.html` and all files in `reference/tools/`. Extract:
- Complete tool inventory (File tools, Shell, Search, Web, Agent/Task, Notebook, MCP)
- Each tool's permission model (checkPermissions → allow/ask/deny)
- Tool selection heuristics — when Claude chooses Edit vs Write, Bash vs dedicated tools
- Bash tool specifics: persistent session, compound command checking, background execution
- Grep specifics: ripgrep-based, three output modes
- MCP tool naming convention

- [ ] **Step 2: Write the module**

**How It Works:**
- Tool taxonomy table (tool name, purpose, permission default, when auto-approved)
- Mermaid diagram: tool dispatch flow (user request → model selects tool → checkPermissions → allow/ask/deny → execute or prompt)
- Deep dive on Edit tool mechanics (requires prior Read, unique string match, replace_all)
- Bash tool: persistent session, compound command independence, output truncation
- How Claude decides between tools (system prompt instructions guide it)

**See It In Action:**
- Exercise 1: Review transcripts from Modules 1-2. Categorize every tool call. Build a frequency table.
- Exercise 2: Ask Claude Code to "find all Python files containing 'import'" — watch it choose Grep over Bash+grep
- Exercise 3: Ask Claude Code to make a small edit, then a large rewrite — observe Edit vs Write selection
- Exercise 4: Ask Claude Code to run a multi-step shell command — observe compound command checking

**Build With It:**
- Build the Postgres schema: Topics and Assets tables via SQL migration
- Create Pydantic V2 models for Topic and Asset (matching the spec data model)
- Create CRUD endpoints (GET/POST/PUT/DELETE for topics, GET/POST for assets)
- Watch Claude Code work across SQL files, Python models, and shell commands — identify which tools it uses for each

**Checkpoint:** Working CRUD API with Postgres. `uv run pytest` passes for basic endpoint tests. Learner can explain why Claude chose specific tools during the build.

- [ ] **Step 3: Build and verify**

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/03-tools-dispatch.adoc
git commit -m "feat: add module 3 — tools and tool dispatch"
```

---

## Task 6: Module 4 — Permissions & Trust

**Files:**
- Create: `content/modules/ROOT/pages/04-permissions-trust.adoc`
- Source: `assets/site/concepts/permissions.html`, `assets/site/reference/sdk/permissions-api.html`

- [ ] **Step 1: Read source material**

Read both source files. Extract:
- Six permission modes with behavioral differences
- Permission rules system (allow/deny, tool name + pattern, 5 configuration sources)
- Bash-specific pattern matching with wildcards
- Compound command evaluation (most restrictive result applies)
- Permission evaluation order (rules before mode)

- [ ] **Step 2: Write the module**

**How It Works:**
- Permission modes comparison table (mode, file ops, bash, MCP, use case)
- Mermaid diagram: permission evaluation flow (rules first → mode second → prompt or auto-approve)
- Bash pattern matching deep dive with examples
- Configuration sources hierarchy (user → project → local → session → CLI)

**See It In Action:**
- Exercise 1: Start Claude Code in `plan` mode (`claude --permission-mode plan`). Try to make it write a file — observe the block.
- Exercise 2: Switch to `acceptEdits`. Same task — observe auto-approval for file writes but prompt for bash.
- Exercise 3: Create a permission rule allowing `psql` commands but denying `rm`. Test both.
- Exercise 4: Create a compound command `psql -c "SELECT 1" && rm -rf /tmp/test` — observe independent checking.

**Build With It:**
- Configure `.claude/settings.json` with permission rules for the learnpath project:
  - Allow: `uv run`, `pytest`, `psql`, `docker`/`podman` commands
  - Deny: `rm -rf`, `git push --force`, destructive database commands (`DROP`, `TRUNCATE`)
- Test the rules by asking Claude Code to perform allowed and denied operations
- Transcript review: identify permission evaluation in the transcript

**Checkpoint:** Permission rules in `.claude/settings.json` that appropriately constrain Claude Code for the project. Learner can explain the evaluation order.

- [ ] **Step 3: Build and verify**

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/04-permissions-trust.adoc
git commit -m "feat: add module 4 — permissions and trust"
```

---

## Task 7: Module 5 — Hooks & Lifecycle

**Files:**
- Create: `content/modules/ROOT/pages/05-hooks-lifecycle.adoc`
- Source: `assets/site/guides/hooks.html`, `assets/site/reference/sdk/hooks-reference.html`

- [ ] **Step 1: Read source material**

Read both source files. Extract:
- Six event types with trigger conditions
- Four hook types (shell, HTTP, LLM, agent)
- Exit code semantics (0=success, 2=block/inject, other=error)
- Hook configuration in settings files
- Common patterns (auto-formatting, test running, logging, blocking)

- [ ] **Step 2: Write the module**

**How It Works:**
- Event lifecycle mermaid diagram (SessionStart → UserPromptSubmit → PreToolUse → tool executes → PostToolUse → ... → Stop)
- Event types table (event, when it fires, exit 2 behavior, common use)
- Hook types comparison (shell vs HTTP vs LLM vs agent — when to use which)
- Exit code flow diagram

**See It In Action:**
- Exercise 1: Create a shell hook on `PreToolUse` that logs tool name and arguments to a file. Run a task, then read the log.
- Exercise 2: Create a `Stop` hook that checks if tests pass before allowing Claude to finish (exit 2 to continue).
- Exercise 3: Create a `PostToolUse` hook that runs `ruff format` after any Python file is written/edited.
- Exercise 4: Examine the hook payload JSON structure — what data is available to hooks?

**Build With It:**
- Add hooks to the learnpath project via `/hooks` command:
  - `PostToolUse`: ruff auto-format when `Edit` or `Write` touches `*.py` files
  - `FileChanged`: run `uv run pytest` when files in `tests/` change
  - `SessionStart`: inject project-specific environment variables
- Verify hooks fire correctly by making changes and observing the output

**Checkpoint:** Three working hooks in the project. Learner can create hooks for any event type and understands the exit code semantics.

- [ ] **Step 3: Build and verify**

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/05-hooks-lifecycle.adoc
git commit -m "feat: add module 5 — hooks and lifecycle"
```

---

## Task 8: Module 6 — Skills & Workflows

**Files:**
- Create: `content/modules/ROOT/pages/06-skills-workflows.adoc`
- Source: `assets/site/guides/skills.html`

- [ ] **Step 1: Read source material**

Read `guides/skills.html`. Extract:
- SKILL.md structure and frontmatter options
- Storage locations (project vs user)
- Invocation and argument passing
- Namespacing with colons
- Inline shell commands with `!` prefix
- Conditional activation via `paths` frontmatter
- Skills vs hooks comparison

- [ ] **Step 2: Write the module**

**How It Works:**
- Skill anatomy diagram (directory → SKILL.md with frontmatter → instructions)
- Frontmatter options table (name, description, argument-hint, allowed-tools, when_to_use, model, paths, hooks)
- Namespacing: `skills/db/migrate/SKILL.md` → `/db:migrate`
- Skills vs hooks: decision matrix (explicit workflow = skill, automatic side effect = hook)
- Inline shell commands: how `!uv run pytest` in a skill runs against live project state

**See It In Action:**
- Exercise 1: List built-in skills. Read a SKILL.md file to understand the format.
- Exercise 2: Create a minimal skill at `.claude/skills/hello/SKILL.md` that greets the user. Invoke with `/hello`.
- Exercise 3: Add argument passing — `/hello <name>` uses `$ARGUMENTS`.
- Exercise 4: Create a namespaced skill at `.claude/skills/db/status/SKILL.md` → `/db:status`. Add `paths` frontmatter.

**Build With It:**
- Create three project skills for the learnpath project:
  - `/db:migrate` — runs Alembic migrations with proper uv invocation
  - `/api:scaffold-endpoint` — generates a new FastAPI endpoint from a description (takes entity name as argument)
  - `/test:coverage` — runs pytest with coverage and reports uncovered lines
- Each skill uses inline shell commands (`!`) for live project state
- Test each skill against the learnpath project

**Checkpoint:** Three working custom skills. Learner can create skills with arguments, namespacing, and path-scoped activation.

- [ ] **Step 3: Build and verify**

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/06-skills-workflows.adoc
git commit -m "feat: add module 6 — skills and workflows"
```

---

## Task 9: Module 7 — Extending Claude Code

**Files:**
- Create: `content/modules/ROOT/pages/07-extending-claude-code.adoc`
- Source: Superpowers framework documentation, external ecosystem repos

- [ ] **Step 1: Research current ecosystem**

Fetch current information about:
- Superpowers skills framework (installation, available skills)
- RHDP skills marketplace (https://github.com/rhpds/rhdp-skills-marketplace)
- claude-code-transcripts features and usage
- claude-howto and claude-code-tips content highlights
- Community MCP servers directory
- Other notable Claude Code extensions/tools

- [ ] **Step 2: Write the module**

**How It Works:**
- The extension model: Claude Code is designed to be extended via skills, hooks, MCP, and settings
- Superpowers framework: what it adds (brainstorming, planning, TDD, debugging, code review), how it works (skills that invoke other skills)
- Marketplace model: how organizations distribute skills packages
- Transcript analysis: how claude-code-transcripts extracts and renders session data
- Ecosystem map: mermaid diagram showing Claude Code core → extension points → community tools

**See It In Action:**
- Exercise 1: Install superpowers. Explore the available skills with tab completion.
- Exercise 2: Walk through the brainstorming skill — start a design session for a feature. Observe how it structures the conversation.
- Exercise 3: Walk through the planning skill — take the brainstorm output into a plan.
- Exercise 4: Review transcripts from Modules 1-6. With architectural understanding, identify: context assembly, tool dispatch decisions, permission checks, hook executions. What patterns do you see now that you couldn't see in Module 1?

**Build With It:**
- Use superpowers to plan and implement a new feature: **asset tagging** (Tags model, many-to-many with Assets, API endpoints, search by tag)
- Experience the full cycle: `/brainstorm` → `/plan` → implement → `/review`
- Compare: how does the superpowers-guided workflow differ from ad-hoc prompting?

**Ecosystem Survey:**
- Table of notable tools with links and descriptions
- How to evaluate and adopt third-party extensions safely

**Checkpoint:** Superpowers installed and used. Asset tagging feature implemented via the guided workflow. Learner understands the extension model.

- [ ] **Step 3: Build and verify**

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/07-extending-claude-code.adoc
git commit -m "feat: add module 7 — extending claude code"
```

---

## Task 10: Module 8 — MCP Servers

**Files:**
- Create: `content/modules/ROOT/pages/08-mcp-servers.adoc`
- Source: `assets/site/guides/mcp-servers.html`

- [ ] **Step 1: Read source material**

Read `guides/mcp-servers.html`. Extract:
- MCP protocol overview (stdio, HTTP, SSE transports)
- Adding servers (CLI, config file, interactive `/mcp`)
- Configuration scopes (project `.mcp.json`, user `~/.claude.json`, local settings)
- Server management (enable, disable, reconnect)
- Tool naming convention (`mcp__<server>__<tool>`)
- Safety model (Claude prompts before MCP tool calls)

- [ ] **Step 2: Write the module**

**How It Works:**
- MCP protocol diagram (Claude Code ↔ MCP Server ↔ External Service)
- Transport comparison table (stdio vs HTTP vs SSE — when to use each)
- Configuration scopes diagram matching the settings hierarchy from Module 2
- Tool naming and discovery: how Claude Code sees MCP tools
- Safety model: why Claude prompts before MCP calls

**See It In Action:**
- Exercise 1: Add a public MCP server (e.g., `claude mcp add fetch -- npx @anthropic-ai/mcp-fetch`). Use it to fetch a URL.
- Exercise 2: Examine `.mcp.json` — understand the configuration format.
- Exercise 3: Use `/mcp` panel to view server status, disable/enable.
- Exercise 4: Watch MCP tool calls in a transcript — identify `mcp__fetch__fetch` calls.

**Build With It:**
- Build a custom MCP server using Python and FastMCP:
  - Server that can search and ingest learning resources from an RSS feed
  - Tools: `search_feed(url, query)`, `ingest_asset(feed_url, item_index, topic_id)`
  - Register with the learnpath project via `.mcp.json`
- Use the MCP server from Claude Code to populate the learning asset manager with real content
- Transcript review: identify MCP tool calls and the permission prompts

**Checkpoint:** Custom MCP server running and registered. Claude Code can use it to ingest content into the learning asset manager.

- [ ] **Step 3: Build and verify**

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/08-mcp-servers.adoc
git commit -m "feat: add module 8 — MCP servers"
```

---

## Task 11: Module 9 — Multi-Agent Patterns

**Files:**
- Create: `content/modules/ROOT/pages/09-multi-agent.adoc`
- Source: `assets/site/guides/multi-agent.html`, `assets/site/reference/tools/agent.html`

- [ ] **Step 1: Read source material**

Read both source files. Extract:
- Sub-agent model (independent instances, isolated contexts)
- Zero inherited context — prompts must be self-contained
- Agent tool parameters (prompt, description, subagent_type, model, run_in_background, isolation)
- Worktree isolation
- Foreground vs background execution
- When to use vs not use agents
- Effective prompt design for agents

- [ ] **Step 2: Write the module**

**How It Works:**
- Multi-agent architecture diagram (parent agent → spawns sub-agents → isolated contexts → results returned)
- Zero-context isolation: why sub-agents don't inherit parent context, and why this is a feature not a bug
- Worktree isolation: mermaid diagram showing git worktree creation for safe experimentation
- Agent type specialization (Explore, Plan, code-reviewer, general-purpose)
- Foreground vs background: decision matrix

**See It In Action:**
- Exercise 1: From Claude Code, ask it to spawn a sub-agent for research. Observe the Agent tool call in the transcript.
- Exercise 2: Compare parent and sub-agent transcripts. What context does the sub-agent have? What's missing?
- Exercise 3: Use `isolation: "worktree"` — observe the worktree creation and cleanup.
- Exercise 4: Run a background agent — observe the notification when it completes.

**Build With It:**
- Build the React frontend using multi-agent parallelism:
  - Ask Claude Code to "build the React frontend for the learnpath app while also adding pagination to the API endpoints"
  - Observe how it dispatches: one agent on React scaffolding, another on API refinements
  - Or: guide it to use agents explicitly with specific prompts
- Review the multi-agent transcripts — trace how work was divided and results merged

**Checkpoint:** React frontend scaffolded and connected to the FastAPI backend. Learner has used both foreground and background agents and understands context isolation.

- [ ] **Step 3: Build and verify**

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/09-multi-agent.adoc
git commit -m "feat: add module 9 — multi-agent patterns"
```

---

## Task 12: Module 10 — SDK & Integration

**Files:**
- Create: `content/modules/ROOT/pages/10-sdk-integration.adoc`
- Source: `assets/site/reference/sdk/overview.html`

- [ ] **Step 1: Read source material**

Read `reference/sdk/overview.html`. Extract:
- Subprocess model (host spawns Claude, JSON message exchange)
- Output formats (text, json, stream-json)
- Initialization handshake (`initialize` control request)
- Message types (system, assistant, tool_progress, result, stream_event)
- Session management (transcripts, forking, saved sessions)
- Headless/non-interactive mode

- [ ] **Step 2: Write the module**

**How It Works:**
- SDK architecture diagram (host process → spawns Claude subprocess → JSON messages → results)
- Message type taxonomy with examples
- Output format comparison (when to use text vs json vs stream-json)
- Session management: how transcripts are stored, session forking, resumption
- Integration patterns: IDE extensions, CI/CD, headless agents

**See It In Action:**
- Exercise 1: Run Claude Code with `--output json` and pipe to `jq`. Examine the JSON structure.
- Exercise 2: Run with `--output stream-json` — observe streaming message types in real time.
- Exercise 3: Explore `~/.claude/` session transcripts. Find a previous session and fork it with `claude --resume`.
- Exercise 4: Run Claude Code non-interactively: `echo "explain this project" | claude --output text -p`

**Build With It:**
- Build a CI automation script for the learnpath project:
  - Python script using `subprocess` to invoke Claude Code with `--output json`
  - Runs: `claude -p "run the test suite and report results" --output json`
  - Parses JSON output to extract test results
  - Generates a summary report
- Alternative: build a simple GitHub Action or shell script that uses Claude Code headlessly

**Checkpoint:** Working CI automation script. Learner understands the SDK message protocol and can build integrations.

- [ ] **Step 3: Build and verify**

- [ ] **Step 4: Commit**

```bash
git add content/modules/ROOT/pages/10-sdk-integration.adoc
git commit -m "feat: add module 10 — SDK and integration"
```

---

## Task 13: Capstone — Putting It All Together

**Files:**
- Create: `content/modules/ROOT/pages/11-capstone.adoc`

- [ ] **Step 1: Write the capstone module**

Structure:

**Architecture Retrospective:**
- Full system mermaid diagram showing all components: agentic loop, context assembly, tool dispatch, permissions, hooks, skills, MCP, multi-agent, SDK
- Walk through a single Claude Code session tracing the complete path from user input through every system
- "Now you know": summary table mapping each module to the architectural concept and the part of the build project it produced

**What You Built:**
- Inventory of everything in the learnpath project:
  - FastAPI backend with CRUD + pagination
  - Pydantic V2 models with Postgres persistence
  - CLAUDE.md and project memory configuration
  - Permission rules for safe development
  - Hooks for auto-formatting, testing, linting
  - Custom skills for project operations
  - MCP server for external content ingestion
  - React frontend
  - CI automation script
- Architecture diagram of the learnpath app itself

**Final Transcript Deep Dive:**
- Exercise: Select transcripts from Module 1 and Module 10. Compare your understanding.
- Guided questions: What do you see now that you couldn't see then? How has your mental model changed?

**Where to Go Next:**
- Contributing to Claude Code ecosystem (building skills, MCP servers)
- Advanced patterns not covered (custom agent types, enterprise managed settings, API-driven workflows)
- Community resources and staying current

**Checkpoint:** Learner can trace any Claude Code behavior back to its architectural component. The learning asset manager is a fully functional reference implementation.

- [ ] **Step 2: Build and verify**

- [ ] **Step 3: Commit**

```bash
git add content/modules/ROOT/pages/11-capstone.adoc
git commit -m "feat: add capstone module"
```

---

## Task 14: Final Review and Polish

**Files:**
- Verify: all 13 `.adoc` files in `content/modules/ROOT/pages/`
- Verify: `content/modules/ROOT/nav.adoc`
- Modify: `CLAUDE.md` (update if needed)

- [ ] **Step 1: Full site build and verification**

```bash
podman run --rm --name antora -v $PWD:/antora -p 8080:8080 -i -t ghcr.io/juliaaano/antora-viewer
```

Verify:
- All 13 pages render without errors
- Navigation sidebar shows correct structure with sections
- Mermaid diagrams render
- All cross-references (`xref:`) resolve
- Attribute substitution works (URLs, project name)
- Code blocks have correct syntax highlighting
- Prerequisites and checkpoint boxes render consistently

- [ ] **Step 2: Cross-reference audit**

Check every `xref:` link across all modules. Verify:
- Module N's "Next:" link points to Module N+1
- Prerequisites reference prior modules correctly
- No broken cross-references

- [ ] **Step 3: Attribute audit**

Verify all `{attribute}` references in code blocks use attributes defined in `content/antora.yml`.

- [ ] **Step 4: Update CLAUDE.md**

Update `CLAUDE.md` to reflect the final course content structure if anything changed during implementation.

- [ ] **Step 5: Final commit**

```bash
git add -A
git commit -m "chore: final review and polish for claude code deep dive course"
```
