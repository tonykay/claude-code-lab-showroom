# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Showroom content repository building a "Claude Code Deep Dive" course — a zero-to-hero guide for experienced developers who are new to Claude Code. Content is written in AsciiDoc and built with Antora into a static site deployed via GitHub Pages.

The repo was forked from the `showroom_template_nookbag` template. The existing pages under `content/modules/ROOT/pages/` are Showroom platform documentation from the template — they will be replaced with Claude Code course content organized into progressive learning modules.

## Build & Preview

```bash
# Local preview with Podman (no live reload)
podman run --rm --name antora -v $PWD:/antora -p 8080:8080 -i -t ghcr.io/juliaaano/antora-viewer

# On SELinux systems, append :z to the volume mount

# CI builds use: antora --fetch site.yml (output to ./www/)
```

There is no `package.json`, `Makefile`, or test suite. The build is Antora 3.1 running in a container or via CI.

## Architecture

### Configuration (do not confuse these)

| File | Purpose | When to edit |
|------|---------|--------------|
| `content/antora.yml` | Component descriptor: title, nav, AsciiDoc attributes | Add/change content attributes, update nav references |
| `site.yml` | Antora playbook: site title, UI bundle, extensions | Rarely — controls build pipeline, not content |
| `ui-config.yml` | Showroom right-pane tabs (terminal, docs links) | When adding/removing UI tabs for the deployed lab |

### Content lives in `content/modules/ROOT/`

- `pages/` — AsciiDoc files (the course content)
- `nav.adoc` — sidebar navigation; pages not listed here are hidden unless dev-mode is enabled
- `partials/` — reusable include snippets (create as needed)
- `assets/images/` — images referenced via `image::filename.png[]`
- `examples/` — downloadable files (create as needed)

### Examples directory (`examples/`)

Contains reference templates for two content patterns:
- **Demo** (`examples/demo/`) — Know/Show structure: business context then step-by-step commands
- **Workshop** (`examples/workshop/`) — narrative progression with hands-on exercises

Each has `example/` (minimal) and `templates/` (full with README guide) subdirectories.

## AsciiDoc Conventions

- **Attribute substitution in code blocks**: use `[source,bash,subs="attributes"]` to enable `{attribute_name}` replacement
- **Executable commands**: use `[source,role="execute"]` for commands the learner should run
- **Cross-references**: `xref:other-page.adoc[Link Text]`
- **Mermaid diagrams**: supported via `[mermaid]` blocks (extension configured in site.yml)
- **Admonitions**: `[NOTE]`, `[IMPORTANT]`, `[TIP]`, `[WARNING]`
- **Variables** are defined in `content/antora.yml` under `asciidoc.attributes` and injected at runtime from `user_data` during deployment

## Content Guidelines for This Course

- Target audience: experienced developers without Claude Code background
- Progressive difficulty: foundations first, then architecture, then advanced usage
- Use numbered file prefixes for ordering (e.g., `01-getting-started.adoc`, `02-architecture.adoc`)
- Every new page must be added to `content/modules/ROOT/nav.adoc` to appear in the sidebar
- When referencing Claude Code features, cite current documentation — the tool evolves rapidly
