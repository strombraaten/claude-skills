# claude-skills

A collection of custom skills for [Claude Code](https://claude.ai/code).

Skills extend Claude Code with reusable, trigger-aware behaviors — things like generating UI mockups, running structured workflows, or producing formatted output. Each skill lives in its own folder and can be installed independently.

## Installation

Clone this repo and symlink (or copy) any skill folder into `~/.claude/skills/`:

```sh
git clone https://github.com/strombraaten/claude-skills.git
cd claude-skills

# Copy a skill
cp -r skills/design-explorer ~/.claude/skills/

# Or symlink it (changes here reflect immediately)
ln -s "$PWD/skills/design-explorer" ~/.claude/skills/design-explorer
```

Then invoke it in Claude Code with `/design-explorer`.

## Skills

| Skill | Description |
|-------|-------------|
| [design-explorer](skills/design-explorer/) | Generate 6–8 distinct UI mockups and browse them in an interactive gallery with voting and feedback synthesis |
| [obsidian-plugin-dev](skills/obsidian-plugin-dev/) | Build Obsidian plugins that pass the official automated review on the first try — ESLint setup, API conventions, manifest rules, and submission checklist |
| [pareto-analysis](skills/pareto-analysis/) | Run an 80-20 analysis to identify the vital few inputs, tasks, or problems driving most results |
| [figma-write-to-figma](skills/figma-write-to-figma/) | Write editable design layers into Figma files using the official Figma MCP server's `generate_figma_design` tool |

## Contributing

Pull requests welcome. Each skill needs:
- `SKILL.md` — skill definition with YAML frontmatter (`name`, `description`) and instructions for Claude
- `README.md` — what it does, how to invoke it, and a screenshot or two

## License

MIT
