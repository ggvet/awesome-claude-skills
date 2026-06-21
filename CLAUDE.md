# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

A curated collection of Claude Skills — modular instruction packages that extend Claude's capabilities across Claude.ai, Claude Code, and the Claude API. Each skill lives in its own subdirectory and is self-contained.

The repository is published as a Claude Code marketplace plugin via `.claude-plugin/marketplace.json`, which maps skill folder names to their categories and descriptions for discovery.

## Skill Structure

Every skill follows this layout:

```
skill-name/
├── SKILL.md          # Required: YAML frontmatter + markdown instructions
├── scripts/          # Executable helpers (Python/Bash) — run without loading into context
├── references/       # Reference docs loaded into context as needed
└── assets/           # Files used in output (fonts, templates, boilerplate)
```

`SKILL.md` must start with YAML frontmatter containing exactly two required fields:

```yaml
---
name: skill-name       # hyphen-case: lowercase letters, digits, hyphens only; max 40 chars
description: ...       # One sentence; third-person ("This skill should be used when...")
---
```

The `description` field determines when Claude auto-activates the skill — it must be specific and must not contain angle brackets (`<` or `>`).

## Creating a New Skill

1. **Initialize** from the skill-creator template:
   ```bash
   python skill-creator/scripts/init_skill.py <skill-name> --path .
   ```
   This creates the directory with a SKILL.md template and example `scripts/`, `references/`, and `assets/` subdirectories.

2. **Edit** `SKILL.md`: write instructions in imperative/infinitive form (verb-first, not "you should"). Information should live in either `SKILL.md` or reference files, not both — keep `SKILL.md` under ~5k words.

3. **Validate** the skill structure:
   ```bash
   python skill-creator/scripts/quick_validate.py <skill-name>
   ```

4. **Package** for distribution (runs validation automatically):
   ```bash
   python skill-creator/scripts/package_skill.py <path/to/skill-folder>
   # Optional: specify output directory
   python skill-creator/scripts/package_skill.py <path/to/skill-folder> ./dist
   ```

5. **Register** in `.claude-plugin/marketplace.json` by adding an entry with `name`, `description`, `source`, and `category`.

6. **List** in `README.md` under the appropriate category, in alphabetical order, with no emojis and consistent punctuation:
   ```markdown
   - [Skill Name](./skill-name/) - One-sentence description.
   ```

## Key Conventions

- **Skill naming**: directory name, `name` frontmatter, and marketplace `name` must all match exactly. Use `hyphen-case`.
- **Writing style**: write skill instructions for Claude, not for end users. Use imperative/infinitive form throughout.
- **Resource separation**: `scripts/` = deterministic executable code; `references/` = documentation Claude reads; `assets/` = files Claude uses in output but doesn't read into context.
- **Progressive disclosure**: metadata (~100 words) is always in context, `SKILL.md` body loads when the skill triggers (<5k words), bundled resources load only as needed.
- **Pull requests**: title format is `"Add [Skill Name] skill"`. PR description must explain the real-world use case, who uses it, and include an attribution line if applicable.

## Document Skills (special case)

`document-skills/` contains four sub-skills (`docx`, `pdf`, `pptx`, `xlsx`) each registered separately in the marketplace. They each bundle Python helper scripts for format manipulation (e.g., `fill_fillable_fields.py`, `convert_pdf_to_images.py`) and OOXML reference documentation.

## Artifacts Builder (special case)

`artifacts-builder/` provides shell scripts for building self-contained React/Tailwind/shadcn HTML artifacts:

```bash
bash artifacts-builder/scripts/init-artifact.sh <project-name>  # scaffold project
bash artifacts-builder/scripts/bundle-artifact.sh               # produce bundle.html
```

The output `bundle.html` is a single file with all JS/CSS inlined, suitable for sharing as a Claude.ai artifact.
