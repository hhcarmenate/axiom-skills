# Axiom Skills Library

Complete skill guide for AI agents building with axiom-skills. Each skill teaches best practices for a specific technology or domain.

---

## Framework Skills (8)

| Skill | When to Use |
|-------|------------|
| [React](/skills/react/SKILL.md) | Building component-based UIs with React 19, hooks, and React Compiler patterns |
| [Next.js](/skills/nextjs/SKILL.md) | Full-stack applications with App Router, server components, and server actions |
| [Vue](/skills/vue/SKILL.md) | Reactive UIs with Vue 3 Composition API, script setup, and composables |
| [Laravel](/skills/laravel/SKILL.md) | Server-side web apps with Eloquent ORM, migrations, and service layer |
| [Python](/skills/python/SKILL.md) | Modern Python 3.12+ with type hints, async/await, and Pydantic validation |
| [TypeScript](/skills/typescript/SKILL.md) | Type-safe JavaScript with strict mode, discriminated unions, and generics |
| [PostgreSQL](/skills/postgresql/SKILL.md) | Advanced relational databases with schema design, indexes, and full-text search |
| [MySQL](/skills/mysql/SKILL.md) | MySQL 8.0+ databases with InnoDB, transactions, and query optimization |

---

## Design System Skills (6)

| Skill | When to Use |
|-------|------------|
| [UX Design](/skills/ux-design/SKILL.md) | User-centered design with accessibility (WCAG 2.1), responsive layouts, and design systems |
| [Tailwind CSS](/skills/tailwindcss/SKILL.md) | Utility-first CSS styling with dark mode, responsive design, and custom configuration |
| [Bootstrap](/skills/bootstrap/SKILL.md) | 12-column grid, pre-built components, and Sass theming for rapid prototyping |
| [Material UI](/skills/material-ui/SKILL.md) | Material Design components in React with theming, sx prop, and dark mode |
| [shadcn/ui](/skills/shadcn-ui/SKILL.md) | Headless, copy-paste components built on Radix UI and Tailwind CSS |
| [daisyUI](/skills/daisyui/SKILL.md) | Tailwind CSS component library with built-in themes and dark mode support |

---

## Quality Skills (1)

| Skill | When to Use |
|-------|------------|
| [Test Specialist](/skills/test-specialist/SKILL.md) | Writing unit, integration, and E2E tests with Jest, Vitest, pytest, Playwright, and Cypress |

---

## How to Use These Skills

1. **Find the right skill**: Look at the technology or pattern you're working with.
2. **Read the patterns**: Each skill contains 4-6 critical patterns with code examples.
3. **Understand the principles**: Learn the key principles that make the technology work well.
4. **Copy the examples**: Use the code examples as a starting point for your own implementation.
5. **Follow best practices**: Apply the patterns and principles to your codebase.

---

## Skill Structure

Each SKILL.md file contains:

- **When to Use**: Specific scenarios for using this skill
- **Critical Patterns**: 4-6 best practice patterns with language-specific code examples
- **Folder Structure**: Recommended directory layout for projects using this tech
- **Key Principles**: Core concepts and rules for working with this technology
- **Commands & Setup**: Copy-paste commands for installation and initialization

---

## Technology Grouping by Domain

### Web Frameworks
- [React](/skills/react/SKILL.md) — Client-side UI framework
- [Next.js](/skills/nextjs/SKILL.md) — Full-stack React framework
- [Vue](/skills/vue/SKILL.md) — Progressive JavaScript framework
- [Laravel](/skills/laravel/SKILL.md) — PHP web framework

### Languages
- [Python](/skills/python/SKILL.md) — Backend language with type hints and async
- [TypeScript](/skills/typescript/SKILL.md) — Type-safe JavaScript
- [JavaScript](/skills/react/SKILL.md) — Embedded in React, Next.js, Vue

### Databases
- [PostgreSQL](/skills/postgresql/SKILL.md) — Advanced relational database
- [MySQL](/skills/mysql/SKILL.md) — Popular relational database

### Styling & UI
- [UX Design](/skills/ux-design/SKILL.md) — Design fundamentals
- [Tailwind CSS](/skills/tailwindcss/SKILL.md) — Utility-first CSS
- [Bootstrap](/skills/bootstrap/SKILL.md) — Component framework
- [Material UI](/skills/material-ui/SKILL.md) — Material Design components
- [shadcn/ui](/skills/shadcn-ui/SKILL.md) — Headless components
- [daisyUI](/skills/daisyui/SKILL.md) — Tailwind component library

### Testing & Quality
- [Test Specialist](/skills/test-specialist/SKILL.md) — Testing strategies

---

## Quick Reference by Task

### "I'm building a new React app"
1. [React](/skills/react/SKILL.md) — Learn component patterns
2. [TypeScript](/skills/typescript/SKILL.md) — Add type safety
3. [Tailwind CSS](/skills/tailwindcss/SKILL.md) or [daisyUI](/skills/daisyui/SKILL.md) — Style components
4. [Test Specialist](/skills/test-specialist/SKILL.md) — Write tests

### "I'm building a full-stack app"
1. [Next.js](/skills/nextjs/SKILL.md) — App Router + server actions
2. [TypeScript](/skills/typescript/SKILL.md) — Type everything
3. [PostgreSQL](/skills/postgresql/SKILL.md) or [MySQL](/skills/mysql/SKILL.md) — Store data
4. [Tailwind CSS](/skills/tailwindcss/SKILL.md) or [shadcn/ui](/skills/shadcn-ui/SKILL.md) — Design UI
5. [Test Specialist](/skills/test-specialist/SKILL.md) — Test everything

### "I'm building a Laravel app"
1. [Laravel](/skills/laravel/SKILL.md) — MVC + Eloquent
2. [MySQL](/skills/mysql/SKILL.md) — Database design
3. [Bootstrap](/skills/bootstrap/SKILL.md) or [Tailwind CSS](/skills/tailwindcss/SKILL.md) — Front-end
4. [Test Specialist](/skills/test-specialist/SKILL.md) — Test suite

### "I'm designing a UI"
1. [UX Design](/skills/ux-design/SKILL.md) — Research and principles
2. [Tailwind CSS](/skills/tailwindcss/SKILL.md) or [Material UI](/skills/material-ui/SKILL.md) — Implement design
3. [daisyUI](/skills/daisyui/SKILL.md) — Reusable components

### "I'm building an API"
1. [Next.js](/skills/nextjs/SKILL.md) (Route Handlers) or [Python](/skills/python/SKILL.md) or [Laravel](/skills/laravel/SKILL.md)
2. [PostgreSQL](/skills/postgresql/SKILL.md) or [MySQL](/skills/mysql/SKILL.md)
3. [TypeScript](/skills/typescript/SKILL.md) — Type-safe endpoints
4. [Test Specialist](/skills/test-specialist/SKILL.md) — Integration tests

---

## Version Information

- **React**: 19+
- **Next.js**: 15 (App Router)
- **Vue**: 3 (Composition API)
- **Laravel**: 11
- **Python**: 3.12+
- **TypeScript**: Latest (strict mode)
- **PostgreSQL**: 12+
- **MySQL**: 8.0+
- **Tailwind CSS**: 4
- **Bootstrap**: 5
- **Material UI**: v6
- **shadcn/ui**: Latest
- **daisyUI**: Latest

---

## Contributing to Skills

To add or update a skill:

1. Create or update the SKILL.md file with frontmatter and sections
2. Ensure code examples are language-specific and follow best practices
3. Include 4-6 critical patterns with explanations
4. Add folder structure recommendations
5. List key principles and setup commands
6. Update this SKILLS.md index with the skill mapping

---

**Last Updated**: 2026-04-22  
**Author**: Henry Carmenate  
**License**: MIT
