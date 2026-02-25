# Sky Ecosystem Skill for Claude Code

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that answers questions about the Sky Ecosystem by pulling from five live sources:

- **Sky Atlas** - The official governance rulebook ([GitHub](https://github.com/sky-ecosystem/next-gen-atlas))
- **Sky Forum** - Community discussions, proposals, and announcements ([forum.sky.money](https://forum.sky.money))
- **info.sky.money** - Live protocol data, metrics, and stats ([info.sky.money](https://info.sky.money))
- **Smart Contracts** - On-chain contract ABIs and source code via [Chainlog](https://chainlog.sky.money) + [Etherscan](https://etherscan.io)
- **Laniakea Docs** - Infrastructure architecture, capital flows, risk framework, settlement, agents, and roadmap ([GitHub](https://github.com/sky-ecosystem/laniakea-docs))

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
/sky How does the USDS contract work?
/sky What is the PAU pattern in Laniakea?
/sky Explain the Laniakea risk framework
```

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- For info.sky.money queries: [`agent-browser`](https://agent-browser.dev/) (the site is behind Cloudflare, so the skill uses a headed browser to fetch data)
  ```bash
  npm install -g agent-browser      # all platforms
  brew install agent-browser        # macOS
  ```
- For smart contract queries: an [Etherscan API key](https://etherscan.io/myapikey) (free). Add to your shell profile (`~/.zshrc` or `~/.bashrc`):
  ```bash
  export ETHERSCAN_V2_API_KEY=your_key_here
  ```
  Then restart your terminal or run `source ~/.zshrc`.

## Architecture

The skill delegates heavy data fetches to **subagents** to keep the main context window clean. Large documents like the Sky Atlas and Solidity source code are fetched and analyzed in isolated subagent contexts — only concise findings are returned to the main conversation. Multiple sources are fetched in parallel when needed.

## Sources

| Source | What it contains | How the skill accesses it |
|---|---|---|
| Sky Atlas | Governance rules, roles, processes | Subagent fetches raw markdown from GitHub |
| Sky Forum | Community discussions, proposals | Subagent queries Discourse JSON API |
| info.sky.money | Live protocol metrics, TVL, rates | Browser automation (Cloudflare-protected) |
| Smart Contracts | Contract ABIs, source code, interfaces | Subagent queries Chainlog + Etherscan API |
| Laniakea Docs | Infrastructure architecture, risk, settlement, agents, roadmap | Subagent fetches raw markdown from GitHub |
