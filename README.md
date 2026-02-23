# Cursor Agent Skills

A collection of specialized AI agent skills and personas for [Cursor IDE](https://cursor.com), designed to augment software development workflows with role-specific guidance, Azure DevOps integration, and security scanning.

## Overview

Each skill defines a focused AI persona with scoped responsibilities, tool access, communication style, and structured workflows. Skills follow a consistent `SKILL.md` format and integrate with MCP (Model Context Protocol) servers for live data access.

## Skills

| Skill | Role | Focus | Trigger Keywords |
|---|---|---|---|
| `adm-persona` | Application Development Manager | Team capacity, technical debt, pipeline health, build success rates, quality gates | ADM responsibilities, quality gates, engineering metrics, PR throughput, resource allocation |
| `ba-persona` | Business Analyst | Requirements analysis, acceptance criteria, story readiness, dependency tracing | BA tasks, requirements analysis, story refinement, user stories, dependencies |
| `lead-designer-persona` | Lead Designer | UI/UX design, design systems, marketing sites, SaaS platforms, accessibility (WCAG 2.1 AA) | Design tasks, component design, landing pages, screen layouts, design system, design reviews |
| `lead-dev-persona` | Lead Developer | Code reviews, PR feedback, architecture decisions, build failure analysis, implementation guidance | Technical review, code quality, development support, build failures, technical blockers |
| `pm-persona` | Project Manager | Sprint planning, velocity tracking, risk management, blocker identification, stakeholder reporting | PM tasks, sprint status, blockers, project health, delivery tracking, velocity metrics |
| `owasp-security-scan` | OWASP Security Expert | Vulnerability scanning against OWASP Top 10 (2021) and API Security Top 10 (2023) | Security audit, vulnerability scan, security review, OWASP check, XSS, injection, CSRF, SSRF |

## Project Structure

```
skills/
├── README.md
├── adm-persona/
│   └── SKILL.md
├── ba-persona/
│   └── SKILL.md
├── lead-designer-persona/
│   ├── SKILL.md
│   └── design-patterns.md          # Marketing & SaaS UI patterns
├── lead-dev-persona/
│   └── SKILL.md
├── pm-persona/
│   └── SKILL.md
└── owasp-security-scan/
    ├── SKILL.md
    ├── report-template.md           # Security audit report template
    ├── nextjs-react-guidance.md     # Framework-specific security checks
    ├── owasp-top10-2021.md          # OWASP Top 10 reference (A01–A10)
    └── owasp-api-top10-2023.md      # OWASP API Top 10 reference (API1–API10)
```

## How Skills Work

1. **Activation** -- Skills activate when a user query matches the skill's trigger criteria (e.g., asking about sprint velocity activates the PM persona).
2. **Context loading** -- Cursor reads the `SKILL.md` and any supporting files to prime the agent with role-specific knowledge.
3. **Tool access** -- Each skill declares which MCP tools it uses (Azure DevOps, Pencil, etc.) and operates read-only by default.
4. **Structured output** -- Skills follow defined workflows and reporting formats for consistency.

## MCP Integrations

| MCP Server | Used By | Purpose |
|---|---|---|
| `user-azureDevOps` | ADM, BA, Lead Designer, Lead Dev, PM | Query and manage work items, pipelines, PRs, sprints |
| `user-pencil` | Lead Designer | Read/write `.pen` design files |

## Conventions

- **Read-only by default** -- All personas require explicit user approval before modifying Azure DevOps items or other external systems.
- **Hybrid Agile** -- Scrum sprints with Kanban flow elements. Work item types: Feature, User Story, Task, Bug, Issue.
- **Data-driven** -- Personas rely on metrics, queries, and evidence rather than assumptions.
- **Challenge culture** -- Skills push back on vague requirements, skipped tests, and architectural shortcuts.

## SKILL.md Format

Each skill follows a consistent structure:

| Section | Purpose |
|---|---|
| Frontmatter | `name` and `description` metadata |
| Role Definition | Scope, work item types, workflow context |
| Access Level | Read/write permissions and escalation rules |
| Primary Responsibilities | Key duties for the role |
| Tool Usage | MCP tool references with usage examples |
| Workflow Patterns | Step-by-step processes for common tasks |
| Anti-patterns | Behaviors to avoid |
| Escalation Protocol | When and how to escalate |
| Example Interactions | Sample queries and response approaches |

## Getting Started

### Prerequisites

- [Cursor IDE](https://cursor.com) installed
- MCP servers configured (Azure DevOps, Pencil) as needed

### Setup

#### Windows

```powershell
git clone <repository-url> %USERPROFILE%\.cursor\skills
```

#### macOS

```bash
git clone <repository-url> ~/.cursor/skills
```

#### Linux

```bash
git clone <repository-url> ~/.cursor/skills
```

### Usage

Skills activate automatically when relevant queries are detected. You can also reference a skill directly in Cursor by mentioning it (e.g., *"Run an OWASP security scan on this project"*).

## Adding a New Skill

1. Create a new folder under `skills/` (e.g., `my-skill/`).
2. Add a `SKILL.md` following the format described above.
3. Include supporting reference files alongside `SKILL.md` as needed.
4. Register the skill in Cursor's agent skill configuration so it appears in the available skills list.

## License

Internal use. See repository-level license for details.
