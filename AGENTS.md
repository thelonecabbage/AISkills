# AISkills

A repository of reusable AI agent skills. There is no application code or build step; the content is Markdown.

## Layout

- `skills/<skill-name>/SKILL.md`: one folder per skill. Put skills here, not under `.github/`.
- `README.md`: lists every skill. Keep its table in sync when adding, renaming or removing a skill.
- `LICENSE`: MIT.

## Skill conventions

- The folder name equals the `name` in the frontmatter, in lowercase with hyphens.
- Frontmatter has `name` and `description` only. The `description` says when to use the skill and ends with `Triggers:` followed by quoted phrases.
- Write instructions for an agent: imperative, specific, with exact commands and file paths. Prefer tables and short lists over prose.
- Record verified facts and known pitfalls. Do not guess APIs or flags; link to the official docs when unsure.
- Keep each skill self-contained. Extra files (scripts, templates) go in the same skill folder and are referenced by relative path.
- Do not include secrets, tokens, or personal paths.

## Changing a skill

1. Read the existing `SKILL.md` before editing.
2. Edit in place; keep the frontmatter valid YAML (quote a `description` that contains colons).
3. If the skill's purpose changes, update its row in the `README.md` table.

## Verification

There are no tests. Check that:

- every `skills/*/SKILL.md` starts with valid frontmatter whose `name` matches its folder;
- every skill is listed in the `README.md` table;
- relative links resolve.
