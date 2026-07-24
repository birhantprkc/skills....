# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

## What this repository is

A collection of agent skills for building great product interfaces (typography, colors, UI polish), published for installation via `npx skills add jakubkrehel/skills`. It is documentation-only; there is no build, lint, or test tooling.

## Structure

Each skill lives in `skills/<skill-name>/`:

- `SKILL.md` is the entry point. YAML frontmatter with `name` (matching the directory) and `description` (one-line summary, "Use when..." guidance, and a "Triggers on ..." keyword list that agents match against). The body: a short philosophy paragraph (one or two lines, with hand-off lines naming sibling skills that own adjacent topics), a **Quick Reference** table linking to reference files (only when the skill has them), numbered **Core Principles**, a **Common Mistakes** table, and a **Review Output Format** section. No review checklists and no trailing reference-file index; the Quick Reference is the only file listing.
- Supporting `.md` reference files are optional; single-file skills are fine. Add one only when it carries depth beyond the principle statements (recipes, code patterns, lookup tables), not to restate SKILL.md in longer form. Link via relative paths from the Quick Reference table.
- Each rule lives in exactly one skill; other skills point to it by skill name in backticks (e.g. `better-layout`), never via cross-skill relative links.

Current skills: `better-interface` (user-invoked cross-discipline review), `better-ui` (interface polish details), `better-typography` (web typography), `better-colors` (OKLCH color space and color usage), `better-accessibility` (accessibility engineering), `better-layout` (layout structure), `better-writing` (UX writing and interface copy).

## Authoring conventions

- Principles are prescriptive and specific: exact CSS properties, exact values (e.g. scale `0.25` → `1`, blur `4px` → `0px`), not vague advice.
- Skills instruct agents to match the target project's existing styling system (Tailwind vs. plain CSS vs. CSS-in-JS) rather than impose one.
- Frontmatter `description` is the discovery surface; when adding or changing a skill's scope, update its trigger keywords accordingly.
- Skill directory names use the `better-*` prefix; renaming a skill means renaming its directory and frontmatter `name` together.
