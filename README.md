# Skill_repo

This repository is a **curated collection of "agent skills" and platform‑design guidelines** that I use when "vibe coding" with AI assistants. Many of the skills are copied or updated from various sources, particularly those found on https://skills.sh/. Each skill encapsulates knowledge about a specific platform or framework (e.g. Android, macOS, Astro, web, visionOS) and is written in a structured markdown format that can be loaded by AI agents to make them immediately productive.

## Purpose

- Serve as a single source of truth for platform design rules, examples, and best practices.
- Provide context‑aware triggers so that AI agents know which skill to activate for a given task or query.
- Organize rules into high‑level categories and optional subsections for on‑demand loading.

## Repository structure

```
/skills
  /<platformOrFramework>
    metadata.json        # version, references, abstract
    SKILL.md             # full skill definition with frontmatter and rules
    AGENTS.md            # summary for agents: when/why to load this skill
    rules/               # optional per‑category rule files or sections
```

### Key files

* **SKILL.md** – Contains YAML frontmatter (`name`, `description`, `license`, `metadata` and trigger keywords) followed by the guideline text.  Examples, code snippets, and numbered rules live here.  The frontmatter is used by tooling that ingests the skills.
* **AGENTS.md** – Short document aimed at clarifying the purpose of the skill, usage scenarios, priority levels, and conventions.  AI agents load this when deciding which skills to activate.
* **metadata.json** – Lightweight metadata about the skill version, author, references, and an abstract. Useful for tracking and automation.
* **rules/** – Some platforms split the long SKILL into smaller rule files (often `_sections.md`) so agents can load only the relevant subset.

## Platforms included

- Android (Material Design 3)
- iOS, iPadOS, macOS, tvOS, watchOS, visionOS (Apple Human Interface Guidelines variants)
- Web (WCAG, responsive design)
- Astro framework (islands, content collections, SSR/SSG)

You can explore any of the directories under `skills/` to see examples of the format.

## Contributor workflow

1. **Add or update a skill**: create a new folder under `skills/` and follow the pattern above.  Copy an existing skill as a template.
2. **Frontmatter**: ensure `name` is unique and the `description` includes trigger keywords (e.g. `triggers on tasks involving Android UI`).
3. **Metadata**: bump the version in `metadata.json` when making substantive changes.
4. **Rules**: if a skill grows large, break it into `rules/*.rule.md` and update `AGENTS.md` accordingly.
5. **Preview**: open files in a markdown viewer or use the AI agent to verify the triggers and load behavior.
6. **Commit & push**: follow the usual GitHub PR process; there is no build step.

> 🔧 There are no compiled artifacts, tests, or build commands in this repo. Content is purely written documentation used directly by tooling or agents.

## Conventions & style

* Use **imperative voice** in rules (`Always use semantic HTML …`).
* Include numbered rules (e.g. `R1.1:`) to allow agents to reference or list them.
* Provide code examples in the language(s) relevant to the platform; wrap them in triple backticks with an appropriate language hint.
* Reference external standards (WCAG, Material, HIG) using short URLs or SC numbers.
* Tag priorities `[CRITICAL]`, `[HIGH]`, `[MEDIUM]` for quick scanning.

## Searching the repository

Use simple `grep`/`rg` over `skills/**` to find keywords or rules.  The frontmatter keywords are particularly helpful when you know what platform or technology is involved.

---

Feel free to open an issue or pull request if something is missing or could be improved.  The goal is to keep the skills relevant and easy for AI helpers to consume.
