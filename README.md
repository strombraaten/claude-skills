# claude-skills

A small collection of custom **skills** for [Claude Code](https://claude.com/claude-code).

## What's a skill?

A skill is a folder with a `SKILL.md` file that teaches Claude Code how to do one thing well — generate UI mockups, run an analysis, follow a checklist. Once installed, you trigger it by typing `/skill-name`, or you just describe what you want and Claude loads the right skill on its own.

Think of it like adding a new tool to Claude's toolbox. Skills only load when they're actually used, so having a lot of them installed costs you nothing until you reach for one.

> New to skills? The [official docs](https://code.claude.com/docs/en/skills) are the best reference. This repo is just a handful of ready-made ones you can drop in.

## What's inside

| Skill | What it does |
|-------|--------------|
| [design-explorer](skills/design-explorer/) | Generates 6–8 distinct UI mockups and lets you browse them in an interactive gallery with voting and feedback synthesis |
| [obsidian-plugin-dev](skills/obsidian-plugin-dev/) | Helps you build Obsidian plugins that pass the official automated review on the first try — ESLint setup, API conventions, manifest rules, submission checklist |
| [pareto-analysis](skills/pareto-analysis/) | Runs an 80-20 analysis to find the vital few inputs, tasks, or problems that drive most of the results |
| [figma-write-to-figma](skills/figma-write-to-figma/) | Writes editable design layers into Figma files using the official Figma MCP server's `generate_figma_design` tool |

## Install a skill

A skill is just a folder. Installing one means copying that folder into your personal skills directory at `~/.claude/skills/`. Pick whichever method below feels easiest — they all end up in the same place.

### Option 1 — Copy one skill (simplest)

If you just want one skill, grab the repo and copy that folder over:

```sh
git clone https://github.com/strombraaten/claude-skills.git
mkdir -p ~/.claude/skills
cp -r claude-skills/skills/design-explorer ~/.claude/skills/
```

Swap `design-explorer` for any skill name from the table above. To install **all** of them at once:

```sh
cp -r claude-skills/skills/* ~/.claude/skills/
```

### Option 2 — Symlink (stay up to date)

A symlink points your skills directory at the folder in this repo, so a `git pull` here updates the installed skill automatically — handy if you're tweaking it:

```sh
git clone https://github.com/strombraaten/claude-skills.git
cd claude-skills
mkdir -p ~/.claude/skills
ln -s "$PWD/skills/design-explorer" ~/.claude/skills/design-explorer
```

### Option 3 — Download without git

No git? On the repo's GitHub page, click **Code ▸ Download ZIP**, unzip it, and move any folder from inside `skills/` into `~/.claude/skills/`. Same result, no command line required.

> **Where do skills live?** `~/.claude/skills/` makes a skill available in **every** project on your machine. If you'd rather keep a skill inside one specific project (and commit it for teammates), put it in that project's `.claude/skills/` folder instead.

## Use a skill

Two ways, both work for every skill here:

1. **Type the command.** In Claude Code, type `/` and you'll see the skill in the menu — e.g. `/design-explorer`.
2. **Just ask.** Describe what you want ("show me some design options for this screen") and Claude picks the matching skill on its own.

## Check that it worked

Start (or restart) Claude Code and run:

```
/skills
```

You should see the skill listed. You can also just ask Claude *"What skills are available?"*

> **Don't see it?** If the `~/.claude/skills/` folder didn't exist before you started Claude Code, restart Claude Code once so it picks up the new folder. After that, adding more skills shows up without restarting.

## Contributing

Pull requests welcome. Each skill folder should have:

- **`SKILL.md`** *(required)* — YAML frontmatter (`name`, `description`) telling Claude what the skill does and when to use it, followed by the instructions Claude follows.
- **`README.md`** *(nice to have)* — a plain-language explanation of what it does, how to trigger it, and a screenshot or two.

Keep the `description` specific and write it with the words a user would naturally say — that's what Claude matches against when deciding whether to load the skill.

## License

[MIT](LICENSE)
