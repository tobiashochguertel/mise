# GitMCP (idosal/git-mcp) — Detailed Analysis

**Rating: ⭐⭐⭐⭐⭐ (Zero setup, works TODAY with the fork branch)**

## 🏢 What Is GitMCP?

GitMCP is a **free, open-source, remote MCP server** that turns any public GitHub repository into a documentation hub. It reads `llms.txt`, `llms-full.txt`, and `README.md` directly from the repository via the GitHub API.

**Repository:** https://github.com/idosal/git-mcp  
**Website:** https://gitmcp.io  
**License:** MIT  
**Type:** Hosted remote MCP (HTTPS endpoint, no local install)  
**Maintained by:** Community, actively maintained

## 🔑 Key Insight: Works With Our Fork Branch TODAY

GitMCP reads files **directly from the GitHub repository via the API** — not from deployed docs. Since we have `llms.txt` and `llms-full.txt` committed to the `feat/docs-llms-txt-intermediate` branch of the fork, GitMCP can serve those files right now.

However, GitMCP reads from the **default branch** of a repo by default. Our files are on a non-default branch, so we need the generic `gitmcp.io/docs` endpoint, which accepts a repo + branch parameter.

## 🔌 How It Works

GitMCP exposes the following MCP tools:
- `fetch_generic_documentation` — fetches and returns the repo's documentation files
- `search_generic_documentation` — searches within the documentation

It automatically detects and reads (in priority order):
1. `llms.txt` in the repo root
2. `llms-full.txt` in the repo root  
3. `README.md` as fallback

## 💻 Configuration Options

### Option A: Point at jdx/mise (upstream, live today — no llms.txt yet)

```json
"mise-docs-gitmcp": {
  "type": "http",
  "url": "https://gitmcp.io/jdx/mise",
  "headers": {},
  "tools": ["*"]
}
```

Works today — reads the README.md as context. No llms.txt available yet upstream.

### Option B: Point at your fork's intermediate branch (has llms.txt)

GitMCP supports a `branch` parameter for the generic endpoint:

```
https://gitmcp.io/tobiashochguertel/mise
```

This reads from the **default branch** of your fork. Since the intermediate branch is not the default branch, it won't automatically find the llms.txt files.

**Workaround:** Temporarily set `feat/docs-llms-txt-intermediate` as the default branch on your fork in GitHub Settings, then GitMCP will use it. (Remember to revert after PR is merged.)

### Option C: After PR merge (upstream gets llms.txt)

Once `jdx/mise` merges the PR:
```json
"mise-docs-gitmcp": {
  "type": "http",
  "url": "https://gitmcp.io/jdx/mise",
  "headers": {},
  "tools": ["*"]
}
```

GitMCP will automatically find and serve `llms.txt` + `llms-full.txt`.

## 🚀 Why GitMCP Is Powerful

- **Zero install** — pure HTTP endpoint, no `npx`, no `uvx`, nothing
- **No API key** required
- **Auto-detects** llms.txt, llms-full.txt, README.md
- **Keeps in sync** with GitHub — always reads current repo state
- **Reads both** — uses llms.txt for navigation + llms-full.txt for content
- **Works for any public GitHub repo** — including upstream jdx/mise once merged

## ⚡ Add to ~/.copilot/mcp-config.json Right Now

Add this to the `mcpServers` section — works today with the upstream mise README, and will automatically use llms.txt once the PR is merged:

```json
"mise-docs-gitmcp": {
  "type": "http",
  "url": "https://gitmcp.io/jdx/mise",
  "headers": {},
  "tools": ["*"]
}
```

No restart needed after PR merge — GitMCP always fetches the latest from GitHub.

## 📊 Assessment

| Criterion | Score | Notes |
|-----------|-------|-------|
| **Cost** | ✅ Free | No API key, no account needed |
| **Self-hosted** | 🔶 Optional | Remote by default; OSS so self-host possible |
| **llms.txt native** | ✅ Yes | Reads llms.txt + llms-full.txt automatically |
| **Setup effort** | Minimal | One HTTP URL, no install |
| **Works today** | ✅ Yes | With README; with llms.txt after PR merge |
| **Works with fork branch** | ⚠️ Partial | Needs default branch or workaround |
| **Auto-updates** | ✅ Yes | Always reads current GitHub state |
| **Semantic search** | 🔶 Moderate | Fetches docs, LLM does the searching |

## 🏆 Recommendation

**Add `gitmcp.io/jdx/mise` to your MCP config right now.** It works today with the README and will automatically upgrade to full llms.txt support the moment the PR is merged upstream — zero reconfiguration needed.
