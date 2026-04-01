# Claude Code Deep Dive: Zero to Hero — Course Design Spec

## Overview

A self-paced, architecture-driven deep dive course teaching experienced developers how Claude Code works and how to use it effectively. Delivered as a Showroom AsciiDoc workshop built with Antora. The course assumes Claude Code is installed and the learner has basic familiarity, but no deep understanding of its architecture.

The course pairs architectural understanding with a progressive build project — a **Personal Learning Path Manager** — so every concept is immediately applied in practice.

## Design Decisions

### Approach: Interleaved — Teach Then Apply

Each module pairs an architectural concept with a build milestone. The learner gains understanding and working code in every module. This was chosen over "architecture first" (delays gratification) and "spiral" (creates repetition) approaches.

### Delivery Format

- **Self-paced lab** on the learner's local machine
- **Warm start** — assumes Claude Code installed, API key configured, basic usage experience. Module 1 links to official install docs (https://code.claude.com/docs) for those who need them.
- **Target: 10 modules + capstone**, expandable if content demands it

### Progressive Build Project: Personal Learning Path Manager

A full-stack application for managing learning resources (videos, blogs, tutorials, repos, tweets) organized by topic areas. Useful for any domain, but framed for an AI/ML engineer tracking topics like inference, agentic coding, RAG, and prompt engineering.

**Tech Stack:**
- Python 3.13 with uv (project/dependency management)
- Pydantic V2 (all models and structured outputs)
- FastAPI (API layer)
- PostgreSQL (persistence, learner runs locally or via Docker)
- React (frontend, introduced in module 9)
- pytest (testing, integrated via hooks in module 5)

**Core Data Model:**
```
Topic (id, name, description, icon)
  └── Asset (id, topic_id, title, url, type[video|blog|tweet|repo|howto], notes, rating, created_at)

LearningPath (id, name, description, goal)
  └── LearningPathStep (id, path_id, asset_id, order, status[pending|in_progress|completed])
```

## Module Map

### Module 1: The Agentic Loop

**Architecture:** How Claude Code thinks — the six-step cycle: user input → context assembly → model reasoning → permission evaluation → tool execution → loop continuation. Context loading strategy (system context + user context prepended to every API call).

**Introspection:** Install `claude-code-transcripts`. Run a simple Claude Code task, export the transcript, and walk through it identifying each step of the agentic loop.

**Build:** Scaffold the learning asset manager project using Claude Code — `pyproject.toml` with uv, initial FastAPI app skeleton, directory structure. Observe how Claude Code selects tools (Write, Bash) and loops through the scaffolding task.

**Source:** `concepts/how-it-works.html`

### Module 2: Context & Memory

**Architecture:** The 4-level CLAUDE.md hierarchy (system → user → project → local), priority resolution (closer to CWD = higher priority), @include directives, granular rules with `.claude/rules/*.md`, file discovery walk, 40k char limit.

**Introspection:** Examine `~/.claude/` directory structure. Create CLAUDE.md files at different levels and observe which instructions take precedence. Use `/memory` command.

**Build:** Write the project's CLAUDE.md with build commands, stack conventions (Pydantic V2, uv), and architecture notes. Configure `.claude/settings.json`. Set up memory structure for the project.

**Sources:** `concepts/memory-context.html`, `configuration/claudemd.html`, `configuration/settings.html`

### Module 3: Tools & Tool Dispatch

**Architecture:** Built-in tool inventory (Read, Edit, Write, Glob, Grep, Bash, WebFetch, WebSearch, Agent, TodoWrite, NotebookEdit). How Claude selects tools — each tool's `checkPermissions()` returning allow/ask/deny. MCP tool naming (`mcp__<server>__<tool>`).

**Introspection:** Review transcripts from Module 1-2 build sessions. Identify tool selection patterns — when does Claude choose Edit vs Write? When does it use Bash vs dedicated tools? Count tool calls per type.

**Build:** Build the Postgres schema, Pydantic V2 models, and CRUD endpoints for Topics and Assets. Observe tool selection as Claude works across SQL, Python, and shell commands.

**Source:** `concepts/tools.html`, `reference/tools/*`

### Module 4: Permissions & Trust

**Architecture:** Six permission modes (default, acceptEdits, plan, bypassPermissions, dontAsk, auto). Permission rules system — allow/deny lists with tool name and pattern matching. Bash-specific rules with wildcards. Compound command checking. Five configuration sources.

**Introspection:** Switch between permission modes and observe behavior changes. Create permission rules that allow specific database commands but deny destructive operations. Watch the permission prompt appear and disappear.

**Build:** Configure project permissions — safe patterns for database operations, restricted patterns for shell commands. Set up `.claude/settings.json` permission rules tailored to the learning asset manager's workflow.

**Source:** `concepts/permissions.html`

### Module 5: Hooks & Lifecycle

**Architecture:** Six event types (PreToolUse, PostToolUse, Stop, SessionStart, UserPromptSubmit, FileChanged). Four hook types (shell command, HTTP request, LLM prompt, agent hook). Exit codes (0=success, 2=block/inject). Configuration in settings files.

**Introspection:** Create a simple logging hook on PreToolUse to see every tool call Claude makes. Examine the hook payload structure. Create a Stop hook that prevents Claude from finishing without running tests.

**Build:** Add PostToolUse hooks for ruff auto-formatting on Python file edits, pytest execution on file changes, and pre-commit linting. Configure via `/hooks` command.

**Source:** `guides/hooks.html`

### Module 6: Skills & Workflows

**Architecture:** Skills structure (SKILL.md with frontmatter), storage locations (project `.claude/skills/`, user `~/.claude/skills/`), invocation via `/skill-name`, argument passing ($ARGUMENTS, named vars), inline shell commands, namespacing with colons, conditional activation via `paths` frontmatter.

**Introspection:** Examine existing skills (built-in onboarding, code review). Read SKILL.md files to understand frontmatter options. Compare skills vs hooks — when to use which.

**Build:** Create custom skills for the project: `/db:migrate` (run Alembic migrations), `/api:scaffold-endpoint` (generate a new FastAPI endpoint from a description), `/test:coverage` (run pytest with coverage report).

**Source:** `guides/skills.html`

### Module 7: Extending Claude Code

**Architecture:** The superpowers framework — how it extends Claude Code with brainstorming, planning, TDD, debugging, and code review workflows. The RHDP skills marketplace model. Third-party ecosystem tools and integrations.

**Introspection:** Install superpowers. Walk through the brainstorming and planning skills. Review transcripts from modules 1-6 using `claude-code-transcripts` — now with architectural understanding, the learner can see patterns they missed before.

**Build:** Use superpowers to plan and implement a new feature for the learning asset manager (e.g., asset tagging or search). Experience the full brainstorm → plan → implement → review cycle.

**Ecosystem survey:** claude-code-transcripts, claude-howto, claude-code-tips, skills marketplaces, community MCP servers.

**Sources:** Superpowers skills, external repos, community resources

### Module 8: MCP Servers

**Architecture:** MCP protocol basics — stdio/HTTP/SSE transports. Configuration scopes (project `.mcp.json`, user `~/.claude.json`, local settings). Server lifecycle management. Tool naming convention. Safety model (Claude prompts before calling MCP tools).

**Introspection:** Add a public MCP server (e.g., filesystem or fetch). Examine `.mcp.json` configuration. Use `/mcp` panel to manage servers. Watch MCP tool calls in transcripts.

**Build:** Build a custom MCP server (Python with FastMCP) that ingests learning resources from an external source — RSS feed, YouTube API, or similar. Register it with the project and use it from Claude Code to populate the learning asset manager.

**Source:** `guides/mcp-servers.html`

### Module 9: Multi-Agent Patterns

**Architecture:** Sub-agent model — independent Claude instances with isolated contexts. Zero inherited context (prompts must be self-contained). Worktree isolation for safe exploration. Foreground vs background execution. Agent types and specialization.

**Introspection:** Spawn a sub-agent and examine its behavior. Compare the parent transcript with the sub-agent's transcript. Observe the zero-context isolation — what does the sub-agent know vs. what it doesn't?

**Build:** Use multi-agent patterns to build the React frontend. One agent works on API endpoint refinements while another scaffolds the React UI. Experience parallel development orchestrated by Claude Code.

**Source:** `guides/multi-agent.html`, `reference/tools/agent.html`

### Module 10: SDK & Integration

**Architecture:** Subprocess model — host spawns Claude with JSON message exchange. Output formats (text, json, stream-json). Initialization handshake. Message types (system, assistant, tool_progress, result, stream_event). Session management (transcripts, forking).

**Introspection:** Run Claude Code in `--output json` mode and examine the output structure. Explore session transcripts in `~/.claude/`. Fork a previous session and observe context resumption.

**Build:** Build a headless CI script using the SDK that runs automated tests, generates coverage reports, and creates a summary — a lightweight automation pipeline for the learning asset manager.

**Source:** `reference/sdk/overview.html`

### Capstone: Putting It All Together

**Full review:** The learner has a working full-stack application with:
- Configured CLAUDE.md and project memory
- Permission rules tailored to their workflow
- Hooks for auto-formatting, testing, and linting
- Custom skills for common operations
- A custom MCP server for external data ingestion
- Multi-agent patterns for parallel development
- SDK integration for CI automation

**Architecture retrospective:** Walk through the complete system architecture of Claude Code one more time, now with deep practical experience. The learner can trace any Claude Code behavior back to its architectural foundation.

**Transcript deep dive:** Review select transcripts from across the course, identifying how their understanding has evolved from Module 1 to Module 10.

## Module Internal Structure

Every module follows a consistent three-part rhythm:

### Part 1: "How It Works" (Architecture)
- Concept overview drawing from the cached architecture site (`assets/site/`)
- Mermaid diagrams showing relevant system internals
- Key architectural decisions explained — the *why* behind the design
- Cross-references to official docs and source material

### Part 2: "See It In Action" (Introspection)
- Guided exercises observing Claude Code's behavior
- Transcript review using `claude-code-transcripts`
- "Try this and observe" experiments
- Inspection of `~/.claude/` artifacts

### Part 3: "Build With It" (Progressive Project)
- Apply the module's concept to the learning asset manager
- Each module advances the project with a clear milestone
- Ends with a verifiable checkpoint
- Suggested transcript review connecting architecture to practice

### Module Bookends
- **Prerequisites box** at top: what should be working from prior modules
- **Checkpoint** at bottom: what the learner should have running, how to verify
- **References** section: official docs, cached architecture pages, external resources

## Source Material

### Primary: Cached Architecture Site (`assets/site/`)
26 HTML files covering the full Claude Code architecture. Each module maps directly to one or more pages. This is the architectural backbone of the course.

### Secondary: External Sources
- **Official docs** (https://code.claude.com/docs) — canonical reference, linked from every module
- **claude-code-transcripts** (https://github.com/simonw/claude-code-transcripts) — session replay tool, introduced Module 1, used throughout
- **claude-howto** (https://github.com/luongnv89/claude-howto) — supplementary tips and patterns
- **claude-code-tips** (https://github.com/ykdojo/claude-code-tips) — practical tips, linked as further reading
- **Superpowers framework** — Module 7 primary content

### Content Strategy
- Cached site = architectural backbone (each module's "How It Works" draws from it)
- Official docs = canonical reference (always linked, never duplicated)
- External repos = supplementary (curated tips where they reinforce a lesson)
- Transcript tool = introspection mechanism (ties architecture to practice)

## Showroom-Specific Notes

- Content lives in `content/modules/ROOT/pages/` as AsciiDoc files
- Navigation defined in `content/modules/ROOT/nav.adoc`
- File naming: numbered prefixes for ordering (e.g., `01-agentic-loop.adoc`)
- Attributes defined in `content/antora.yml` — use for any runtime-injectable values
- Mermaid diagrams supported via `[mermaid]` blocks
- Executable commands use `[source,role="execute"]`
- Variable substitution in code blocks via `[source,bash,subs="attributes"]`
- Build preview: `podman run --rm --name antora -v $PWD:/antora -p 8080:8080 -i -t ghcr.io/juliaaano/antora-viewer`
