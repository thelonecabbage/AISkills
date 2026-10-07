# AISkills

A collection of reusable AI agent skills. Each skill is a folder under `skills/` with a `SKILL.md` that tells an agent (GitHub Copilot, Claude Code and similar) when and how to do a specific kind of task.

## Skills

| Skill | Description |
|---|---|
| [zepp-os-developer](skills/zepp-os-developer/SKILL.md) | Writing, debugging, building and previewing Zepp OS (Zeus CLI) smartwatch apps: pages, widgets, layouts, `app.json`, gestures, animations, simulator and `zeus` errors |

## Using a skill

Copy or symlink the skill folder into the skills directory your agent reads:

```bash
# GitHub Copilot, per project
cp -r skills/zepp-os-developer <your-project>/.github/skills/

# GitHub Copilot, for every project
cp -r skills/zepp-os-developer ~/.copilot/skills/

# Claude Code
cp -r skills/zepp-os-developer ~/.claude/skills/
```

The agent loads a skill when your request matches the trigger phrases in its `description`.

## Adding a skill

1. Create `skills/<skill-name>/SKILL.md`. The folder name must match the `name` in the frontmatter.
2. Start the file with frontmatter:

   ```markdown
   ---
   name: <skill-name>
   description: "Use when ... Triggers: 'phrase one', 'phrase two'."
   ---
   ```

3. Write the instructions below the frontmatter, and add a row to the table above.

See [AGENTS.md](AGENTS.md) for the conventions to follow.

## License

[MIT](LICENSE)
