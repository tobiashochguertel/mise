# Context7 (Upstash) — Detailed Analysis

**Rating: ⭐⭐⭐⭐ (Free tier, designed for code libraries, easy submission)**

## 🏢 What Is Context7?

Context7 is an MCP server by **Upstash** (a Redis/serverless infrastructure company) that provides up-to-date, version-specific code documentation for AI coding assistants.

It addresses a core problem: AI models are trained on static data and generate broken, deprecated code. Context7 pulls fresh documentation directly from official sources and injects it into the AI's context at query time.

**Repository:** https://github.com/upstash/context7  
**Status:** Open-source (source available), hosted service  
**Website:** https://context7.com

## 🔌 How It Works

1. Context7 maintains an index of 850+ programming libraries and developer tools
2. When an AI assistant is asked about a library, the user adds "use context7" to their prompt
3. Context7's MCP resolves the library, fetches relevant docs, and injects them into the prompt
4. The AI responds using fresh, version-specific documentation

### User Workflow

```
User: "Create a Redis client in Python. use context7"
AI:   [calls context7 MCP → gets latest redis-py docs → answers accurately]
```

### Adding mise to Context7

```bash
# Visit the web form:
# https://context7.com/add-library
# Or use the API to submit
```

Context7 supports adding any library via:
1. Web form at `context7.com/add-library`
2. Their legacy API

## 💰 Pricing

Context7 uses a **freemium** model:

- **Free tier**: Up to **50 queries/day** for personal use
- **Paid tiers**: Higher limits, team features
- **Library submission**: Free for any public library

The free tier is sufficient for individual developers exploring mise docs. Heavy usage (e.g., a team actively building with mise all day) may hit limits.

Source: [Context7 plans](https://context7.com/plans) (Accessed: 2026-03-03)

## ✅ Pros

- **Free to submit** mise to their registry
- **No infrastructure required** from mise maintainers
- **Widely used** — integrated into Cursor, Claude Code, VS Code GitHub Copilot
- **Auto re-indexing** — Context7 keeps its index fresh from source docs
- **Version-specific** — great for a tool like mise that manages version-specific behavior
- **Simple user experience**: Users just say "use context7" in any prompt
- **Open-source client**: The MCP code is MIT-licensed on GitHub

## ❌ Cons

- **Free tier has query limits** (50/day for individual users)
- **Hosted by Upstash**: dependency on their infrastructure and business decisions
- **General-purpose**: Context7 is for any code library, not specifically tailored to mise's use case
- **mise is not currently indexed**: Requires a submission step
- **Search is keyword+semantic**: Not as sophisticated as kapa.ai's RAG pipeline

## 📦 Integration for Users

Once mise is in Context7, users add it to their AI tool:

```bash
# Claude Code
claude mcp add --scope user context7 -- npx -y @upstash/context7-mcp --api-key YOUR_KEY

# Or without API key (uses anonymous free tier)
claude mcp add --scope user context7 -- npx -y @upstash/context7-mcp
```

Then in any prompt:
```
"How do I set up a mise task with dependencies? use context7"
```

## 🔧 What mise Would Need to Do

1. Go to https://context7.com/add-library
2. Submit `https://mise.jdx.dev` as the documentation URL
3. Submit the GitHub repo `https://github.com/jdx/mise`
4. Wait for Context7 to index (typically a few hours to days)
5. Optionally document this in `mise.jdx.dev/mcp.html` under a new "Documentation MCP" section

**Estimated effort:** 30 minutes

## 🆚 vs. kapa.ai

| Feature | Context7 | kapa.ai |
|---|---|---|
| Cost | Free (50/day limit) | Paid (OSS program possible) |
| Infra for mise | None | None (hosted by kapa) |
| Search quality | ★★★★☆ | ★★★★★ |
| User setup | Simple (npx install) | Zero install (URL only) |
| Analytics | Limited | Full dashboard |
| mise-specific | Generic library | Could be tailored |

## 📊 Summary

| Criterion | Score |
|---|---|
| Search quality | ★★★★☆ |
| Ease of setup (maintainers) | ★★★★★ |
| Ease of use (end users) | ★★★★☆ |
| Cost | ★★★★★ (free for libraries) |
| Vendor independence | ★★★☆☆ |
| Maintenance burden | ★★★★★ |

---

- **Research compiled by:** GitHub Copilot
- **For project:** mise (jdx/mise)
- **Date:** March 03, 2026
