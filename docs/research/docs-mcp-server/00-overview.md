# Docs MCP Server — Executive Summary

## 🎯 Problem Statement

The `mise mcp` server (experimental) currently exposes project-level resources: tools, tasks, env vars, and config. But AI assistants cannot query **mise's own documentation** through it — they rely on training data that may be outdated.

This research evaluates how to give AI assistants real-time, accurate access to mise documentation, either by:
- Adding a tool to the existing `mise mcp` command, or
- Creating/integrating a separate hosted docs MCP server

## 🔍 Context: The Astro Model

Astro solved this with a hosted MCP server at `https://mcp.docs.astro.build/mcp` powered by **kapa.ai**. Users add one URL to their AI config and get semantic search over all Astro docs. This is the gold standard UX.

The question is: can mise achieve a similar (or better) result without paid infrastructure?

## ✅ Answer: Yes — Two Zero-Cost Paths

### Path 1: Static llms.txt (Immediate, zero-dependency)

The `vitepress-plugin-llms` npm package generates `llms.txt` and `llms-full.txt` from the mise VitePress docs on every build. These files are auto-discovered by tools like Cursor and Claude Desktop when a user provides `https://mise.jdx.dev` as a docs source.

- **Effort:** 1–2 hours (npm install + one-line VitePress config)
- **Cost:** Free forever
- **Maintenance:** Zero — auto-updates with every docs deploy
- **Current status:** `mise.jdx.dev/llms.txt` returns 404 (not yet implemented)

### Path 2: Context7 Registry Submission (Free hosted MCP)

Context7 by Upstash is a freemium MCP service that indexes developer tool documentation. Submitting mise to their registry is free, takes ~30 minutes, and gives users a `use context7` workflow in Cursor/Claude/etc.

- **Effort:** 30 minutes (web form at context7.com/add-library)
- **Cost:** Free (developer tools qualify)
- **Maintenance:** Zero — Context7 re-indexes automatically
- **Current status:** mise not yet in Context7 (not verified)

### Path 3: Built-in `search_docs` Tool in `mise mcp` (Best long-term DX)

Once `llms.txt` is available at `mise.jdx.dev/llms.txt`, the existing Rust MCP server can be extended with a `search_docs` tool that:
1. Fetches the `llms.txt` index
2. Optionally fetches specific doc pages as Markdown
3. Returns relevant content to the AI assistant

This gives users a single `mise mcp` config that covers both project context AND documentation lookup.

- **Effort:** 2–4 hours (Rust, ~100 lines, reuses existing HTTP client)
- **Cost:** Free forever
- **Maintenance:** Minimal — follows whatever llms.txt format the docs produce

## 🚫 What We Evaluated and Why We Ranked It Lower

| Option | Why Lower Priority |
|---|---|
| **kapa.ai** | Paid commercial service; open-source program requires approval; vendor lock-in |
| **Inkeep** | Also commercial (free OSS tier available but requires setup); more complex than needed |
| **arabold/docs-mcp-server** | Excellent self-hosted option, but users must run it themselves; not a hosted solution for the mise project |
| **Dedicated separate MCP server** | Unnecessary complexity when extending `mise mcp` achieves the same result |

## 📊 Implementation Priority

| Priority | Action | Effort | Owner |
|---|---|---|---|
| 🥇 P1 | Add `vitepress-plugin-llms` to docs build | 1–2 hours | mise contributor |
| 🥈 P2 | Submit mise to Context7 registry | 30 min | mise maintainer |
| 🥉 P3 | Add `search_docs` tool to `mise mcp` | 2–4 hours Rust | mise contributor |
| 🔵 P4 (optional) | Apply for kapa.ai OSS program | 1–3 days (approval) | mise maintainer |

## 🔗 Key Links

- [Full comparison table](99-comparison-table.md)
- [kapa.ai analysis](01-kapa-ai-detailed.md)
- [Context7 analysis](02-context7-detailed.md)
- [arabold/docs-mcp-server analysis](04-arabold-docs-mcp-server-detailed.md)
- [vitepress-plugin-llms analysis](05-llms-txt-vitepress-plugin-detailed.md)
- [Built-in mise MCP tool design](06-mise-mcp-builtin-tool-detailed.md)

---

- **Research compiled by:** GitHub Copilot
- **For project:** mise (jdx/mise)
- **Date:** March 03, 2026
