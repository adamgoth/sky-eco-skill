# Sky Ecosystem Skill for Claude Code

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that answers questions about the Sky Ecosystem by pulling from three live sources:

- **Sky Atlas** - The official governance rulebook ([GitHub](https://github.com/sky-ecosystem/next-gen-atlas))
- **Sky Forum** - Community discussions, proposals, and announcements ([forum.sky.money](https://forum.sky.money))
- **info.sky.money** - Live protocol data, metrics, and stats ([info.sky.money](https://info.sky.money))

The skill automatically picks the right source(s) based on your question.

## Install

### Option 1: Clone (recommended)

```bash
git clone https://github.com/adamgoth/sky-eco-skill ~/.claude/skills/sky
```

This is the recommended approach — you can update later with `git pull`:

```bash
cd ~/.claude/skills/sky && git pull
```

### Option 2: One-liner download

```bash
mkdir -p ~/.claude/skills/sky && curl -sL https://raw.githubusercontent.com/adamgoth/sky-eco-skill/main/SKILL.md -o ~/.claude/skills/sky/SKILL.md
```

### Option 3: Manual

1. Create the directory `~/.claude/skills/sky/`
2. Copy the contents of [`SKILL.md`](./SKILL.md) into `~/.claude/skills/sky/SKILL.md`

## Usage

Once installed, use the `/sky` command in Claude Code:

```
/sky What is the Sky Atlas?
/sky What are the latest discussions on the forum?
/sky What's the current savings APY?
```

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- For info.sky.money queries: [`agent-browser`](https://agent-browser.dev/) (the site is behind Cloudflare, so the skill uses a headed browser to fetch data)
  ```bash
  npm install -g agent-browser      # all platforms
  brew install agent-browser        # macOS
  ```

## Sources

| Source | What it contains | How the skill accesses it |
|---|---|---|
| Sky Atlas | Governance rules, roles, processes | Raw markdown from GitHub |
| Sky Forum | Community discussions, proposals | Discourse JSON API |
| info.sky.money | Live protocol metrics, TVL, rates | Browser automation (Cloudflare-protected) |
