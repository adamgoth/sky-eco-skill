---
name: sky
description: A Claude Code skill for querying the Sky Ecosystem — governance rules, forum discussions, live protocol data, and smart contract/chainlog lookups.
argument-hint: [question about Sky]
---

# Sky Ecosystem Assistant

Answer the following question about the Sky Ecosystem:

**Question:** $ARGUMENTS

## Sources

### 1. Sky Atlas

- **What it is:** The official rulebook and governance document for the Sky Ecosystem. Contains the constitutional framework, scope definitions, governance structures, roles, processes, and rules that govern how the protocol operates.
- **When to use:** Questions about governance rules, constitutional provisions, roles and responsibilities, organizational structure, decision-making processes, or "how is X supposed to work according to the rules."
- **URL (latest on main):**
  ```
  https://raw.githubusercontent.com/sky-ecosystem/next-gen-atlas/refs/heads/main/Sky%20Atlas/Sky%20Atlas.md
  ```

### 2. Sky Forum

- **What it is:** The community discussion forum (Discourse-based) where governance proposals are debated, announcements are posted, and community members discuss Sky Ecosystem topics.
- **When to use:** Questions about current discussions, community sentiment, recent proposals, announcements, or "what is the community saying about X."
- **How to access:** The forum blocks direct page fetches. Use the Discourse JSON API instead:
  - Latest topics: `https://forum.sky.money/latest.json`
  - Specific topic by ID: `https://forum.sky.money/t/{id}.json`
  - Search: `https://forum.sky.money/search.json?q={query}`
  - Category topics: `https://forum.sky.money/c/{category-id}.json`

### 3. info.sky.money

- **What it is:** The live protocol data dashboard for the Sky Ecosystem (built by Block Analitica). Contains real-time and historical protocol metrics, stats, and on-chain data.
- **When to use:** Questions about current protocol state, live metrics, TVL, rates, token data, or "what are the current numbers for X."
- **How to access:** This site is behind Cloudflare bot protection and has no public API. WebFetch will not work. Use `agent-browser` in headed mode instead:
  ```bash
  agent-browser open https://info.sky.money --headed
  agent-browser wait 5000
  agent-browser snapshot -c
  ```
  Sub-pages for deeper data: `/supply`, `/savings`, `/rewards`, `/financials`, `/collateral`, `/staking`
- **Requires:** `agent-browser` must be installed. If the command is not found, tell the user: "Live protocol data from info.sky.money requires agent-browser. Install it with: `npm install -g agent-browser` (or `brew install agent-browser` on macOS)" and answer using the other available sources instead.

### 4. Smart Contracts (Chainlog + Etherscan)

- **What it is:** On-chain contract data for the Sky Ecosystem. The chainlog provides canonical contract addresses, and Etherscan provides verified ABIs and source code.
- **When to use:** Questions about how contracts work on-chain, function signatures, contract interfaces, what a specific contract does, or "how does X work at the smart contract level."
- **How to access:**
  1. Look up the contract address from the chainlog (use WebFetch):
     ```
     https://chainlog.sky.money/api/mainnet/active.json
     ```
  2. Fetch the ABI from Etherscan V2 using Bash (the API key is in an env var, so WebFetch can't be used):
     ```bash
     curl -s "https://api.etherscan.io/v2/api?chainid=1&module=contract&action=getabi&address={ADDRESS}&apikey=$ETHERSCAN_V2_API_KEY"
     ```
  3. For source code:
     ```bash
     curl -s "https://api.etherscan.io/v2/api?chainid=1&module=contract&action=getsourcecode&address={ADDRESS}&apikey=$ETHERSCAN_V2_API_KEY"
     ```
- **Requires:** The `ETHERSCAN_V2_API_KEY` environment variable must be set. If it's not available, tell the user: "Smart contract lookups require an Etherscan API key. Get a free one at https://etherscan.io/myapikey and set it with: `export ETHERSCAN_V2_API_KEY=your_key_here`" and answer using the other available sources instead.

## Instructions

1. **Determine which source(s) to use** based on the question:
   - Governance rules/structure/processes → **Sky Atlas**
   - Community discussions/proposals/announcements → **Sky Forum**
   - Live protocol data/metrics/stats → **info.sky.money**
   - Smart contract details/ABIs/on-chain mechanics → **Chainlog + Etherscan**
   - If the question spans multiple areas, fetch from multiple sources

2. **Delegate heavy fetches to subagents** to keep the main context window clean. Raw source documents (the Atlas, Solidity source code, forum threads) are large and will bloat your context if fetched directly. Use the Task tool to spawn subagents that do the fetching and return only concise findings.

   **Sky Atlas** — spawn a `general-purpose` subagent:
   ```
   Task: "Research Sky Atlas for: [question]"
   Prompt: Fetch the Sky Atlas from [URL above] using WebFetch. Find the sections
   relevant to the question. Return ONLY: the relevant section references (e.g. A.1.2.3),
   key quotes, and a concise summary of the answer. Do not return the full Atlas text.
   ```

   **Sky Forum** — spawn a `general-purpose` subagent:
   ```
   Task: "Search Sky Forum for: [topic]"
   Prompt: Search the Sky Forum using the Discourse JSON API at
   https://forum.sky.money/search.json?q={query}. Fetch the top relevant topics.
   Return ONLY: topic titles, authors, dates, key points, and relevant quotes.
   ```

   **Smart Contracts** — spawn a `Bash` subagent:
   ```
   Task: "Fetch and analyze contract: [name]"
   Prompt: 1) Fetch the chainlog from https://chainlog.sky.money/api/mainnet/active.json
   to find the contract address. 2) Fetch the source code from Etherscan V2:
   curl -s "https://api.etherscan.io/v2/api?chainid=1&module=contract&action=getsourcecode&address={ADDRESS}&apikey=$ETHERSCAN_V2_API_KEY"
   3) Analyze the source code and return ONLY: contract name, key functions with
   their signatures and logic summaries, relevant state variables, and how they
   relate to the question. Do not return the full source code.
   ```

   **info.sky.money** — use `agent-browser` directly (output is already small from snapshots).

   Launch subagents **in parallel** when multiple sources are needed. Wait for all results, then synthesize.

3. **Synthesize and answer** based on the subagent results:
   - Be specific and reference exact sections (e.g., "A.1.2.3") when citing the Atlas
   - Quote relevant passages where helpful
   - If the answer spans multiple sources, synthesize them clearly
   - If none of the sources cover the question, say so clearly

4. **Keep answers concise** but thorough. Structure with headers and bullet points when the answer is complex.
