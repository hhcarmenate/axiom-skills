# axiom-skills

A curated library of AI agent skills for modern software development. Each skill teaches an AI agent the best practices, critical patterns, and conventions for a specific technology — so it writes production-quality code from day one.

Compatible with Claude Code, Cursor, Windsurf, JetBrains AI, and any agent that reads Markdown context files.

---

## What is a skill?

A skill is a `SKILL.md` file that injects domain knowledge into an AI agent's context. It contains:

- **When to Use** — exact scenarios where the skill applies
- **Critical Patterns** — 4–6 battle-tested patterns with working code examples
- **Folder Structure** — recommended directory layout for projects using the tech
- **Key Principles** — core rules and invariants the agent must follow
- **Commands & Setup** — copy-paste installation and initialization commands

Skills are technology-specific, version-pinned, and opinionated. They encode decisions so the agent doesn't have to guess.

---

## Skill library

### Framework skills (8)

| Skill | Technology | Version |
|-------|-----------|---------|
| [React](skills/react/SKILL.md) | Component-based UIs with hooks and React Compiler | 19+ |
| [Next.js](skills/nextjs/SKILL.md) | Full-stack apps with App Router and server actions | 15 |
| [Vue](skills/vue/SKILL.md) | Reactive UIs with Composition API and script setup | 3 |
| [Laravel](skills/laravel/SKILL.md) | Server-side apps with Eloquent ORM and service layer | 11 |
| [Python](skills/python/SKILL.md) | Modern Python with type hints, async/await, and Pydantic | 3.12+ |
| [TypeScript](skills/typescript/SKILL.md) | Strict-mode TypeScript with discriminated unions and generics | Latest |
| [PostgreSQL](skills/postgresql/SKILL.md) | Schema design, indexes, and full-text search | 12+ |
| [MySQL](skills/mysql/SKILL.md) | InnoDB, transactions, and query optimization | 8.0+ |

### Design system skills (6)

| Skill | Technology |
|-------|-----------|
| [UX Design](skills/ux-design/SKILL.md) | User-centered design, accessibility (WCAG 2.1), responsive layouts |
| [Tailwind CSS](skills/tailwindcss/SKILL.md) | Utility-first CSS with dark mode and custom configuration | v4 |
| [Bootstrap](skills/bootstrap/SKILL.md) | 12-column grid, pre-built components, Sass theming | 5 |
| [Material UI](skills/material-ui/SKILL.md) | Material Design components in React with sx prop and theming | v6 |
| [shadcn/ui](skills/shadcn-ui/SKILL.md) | Headless copy-paste components on Radix UI and Tailwind | Latest |
| [daisyUI](skills/daisyui/SKILL.md) | Tailwind component library with built-in themes | Latest |

### Quality skills (1)

| Skill | Technology |
|-------|-----------|
| [Test Specialist](skills/test-specialist/SKILL.md) | Unit, integration, and E2E tests with Jest, Vitest, pytest, Playwright, Cypress |

---

## Agent system

This repository also ships an orchestrator and seven specialized sub-agents built on a 6-phase **SDD workflow** (Spec → Design → Build → Test → Ship).

### Orchestrator

[`AGENT.md`](AGENT.md) is the main orchestrator. Load it as the agent's system prompt or context file. It defines:

- Core engineering principles (no SQL interpolation, no secrets, English-only)
- A persistent memory system (decision, bug, pattern, context, preference)
- The 6-phase SDD workflow with mandatory confirmation gates
- Sub-agent delegation rules

### Sub-agents

Stored in [`.axiom/agents/`](.axiom/agents/):

| Agent | Role |
|-------|------|
| `architect` | Folder structure, module boundaries, interfaces — no implementation |
| `builder` | Feature implementation following an approved design |
| `tester` | Unit, integration, and E2E test suites |
| `reviewer` | Code review, security audit, quality check |
| `debugger` | Root cause analysis and bug fixing |
| `db-agent` | Schema design, migrations, query optimization |
| `doc-writer` | Docstrings, README, API documentation |

### IDE / editor integration

| Tool | Config file |
|------|------------|
| Claude Code | `CLAUDE.md` → `AGENT.md` |
| Cursor | `.cursorrules` → `AGENT.md` |
| Windsurf | `.windsurfrules` → `AGENT.md` |
| JetBrains AI | `.junie/guidelines.md` |
| Codex / GPT | Load `AGENT.md` as system prompt |

---

## Quick start by task

**Building a React app**
1. [React](skills/react/SKILL.md) — component patterns
2. [TypeScript](skills/typescript/SKILL.md) — type safety
3. [Tailwind CSS](skills/tailwindcss/SKILL.md) or [daisyUI](skills/daisyui/SKILL.md) — styling
4. [Test Specialist](skills/test-specialist/SKILL.md) — tests

**Building a full-stack app**
1. [Next.js](skills/nextjs/SKILL.md) — App Router + server actions
2. [TypeScript](skills/typescript/SKILL.md) — type everything
3. [PostgreSQL](skills/postgresql/SKILL.md) or [MySQL](skills/mysql/SKILL.md) — data layer
4. [shadcn/ui](skills/shadcn-ui/SKILL.md) — UI components
5. [Test Specialist](skills/test-specialist/SKILL.md) — full test suite

**Building a Laravel app**
1. [Laravel](skills/laravel/SKILL.md) — MVC + Eloquent
2. [MySQL](skills/mysql/SKILL.md) — schema design
3. [Bootstrap](skills/bootstrap/SKILL.md) or [Tailwind CSS](skills/tailwindcss/SKILL.md) — front-end
4. [Test Specialist](skills/test-specialist/SKILL.md) — PHPUnit tests

**Designing a UI**
1. [UX Design](skills/ux-design/SKILL.md) — principles and research
2. [Tailwind CSS](skills/tailwindcss/SKILL.md) or [Material UI](skills/material-ui/SKILL.md) — implementation
3. [daisyUI](skills/daisyui/SKILL.md) — reusable components

---

## Repository structure

```
axiom-skills/
├── AGENT.md                    # Main orchestrator prompt
├── AGENTS.md                   # Alias (OpenAI Codex / AGENTS.md convention)
├── CLAUDE.md                   # Claude Code entry point → AGENT.md
├── .cursorrules                # Cursor entry point → AGENT.md
├── .windsurfrules              # Windsurf entry point → AGENT.md
├── .junie/guidelines.md        # JetBrains AI entry point
├── .axiom/agents/              # Specialized sub-agent prompts
│   ├── architect.md
│   ├── builder.md
│   ├── db-agent.md
│   ├── debugger.md
│   ├── doc-writer.md
│   ├── reviewer.md
│   └── tester.md
└── skills/
    ├── SKILLS.md               # Full skill index with cross-references
    ├── react/SKILL.md
    ├── nextjs/SKILL.md
    ├── vue/SKILL.md
    ├── laravel/SKILL.md
    ├── python/SKILL.md
    ├── typescript/SKILL.md
    ├── postgresql/SKILL.md
    ├── mysql/SKILL.md
    ├── tailwindcss/SKILL.md
    ├── bootstrap/SKILL.md
    ├── material-ui/SKILL.md
    ├── shadcn-ui/SKILL.md
    ├── daisyui/SKILL.md
    ├── ux-design/SKILL.md
    └── test-specialist/SKILL.md
```

---

## Contributing a skill

1. Create `skills/<technology>/SKILL.md` using the frontmatter format:

```yaml
---
name: Technology Name
description: One-line description of when and why to use this skill.
license: MIT
metadata:
  author: Your Name
  version: "1.0"
  applies-to:
    - claude-code
    - cursor
    - codex
---
```

2. Include these sections: **When to Use**, **Critical Patterns** (4–6 with code examples), **Folder Structure**, **Key Principles**, **Commands & Setup**.
3. Pin the version in the frontmatter and in `skills/SKILLS.md`.
4. Add a row to the skill table in `skills/SKILLS.md`.
5. Commit with `feat: add <technology> skill`.

---

## License

MIT — Henry Carmenate
