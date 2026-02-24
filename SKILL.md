---
name: sky
description: Answer questions about the Sky Ecosystem using the Sky Atlas, Sky Forum, and info.sky.money. Chooses the right source(s) based on the query.
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

## Instructions

1. **Determine which source(s) to use** based on the question:
   - Governance rules/structure/processes → **Sky Atlas**
   - Community discussions/proposals/announcements → **Sky Forum**
   - Live protocol data/metrics/stats → **info.sky.money**
   - If the question spans multiple areas, fetch from multiple sources

2. **Fetch the relevant source(s)** using WebFetch from the URLs above.

3. **Answer the question** based on the fetched content:
   - Be specific and reference exact sections (e.g., "A.1.2.3") when citing the Atlas
   - Quote relevant passages where helpful
   - If the answer spans multiple sources, synthesize them clearly
   - If none of the sources cover the question, say so clearly

4. **Keep answers concise** but thorough. Structure with headers and bullet points when the answer is complex.
