# Context7 Failure Analysis — Why It Can't Index Raw GitHub llms.txt URLs

**Date:** 2026-03-03  
**Status:** Confirmed failure, root cause identified

## 🔴 The Problem

Attempting to submit the mise `llms.txt` file hosted at a raw GitHub URL to Context7 fails completely. Every page link in the index returns "Failed to process any resolved URL".

**Submitted URL:**
```
https://raw.githubusercontent.com/tobiashochguertel/mise/feat/docs-llms-txt-intermediate/docs/public/llms.txt
```

## 📋 Failure Logs (Observed)

```
Processing URL: https://raw.githubusercontent.com/.../docs/public/llms.txt
Processing URL: https://raw.githubusercontent.com/.../docs/public/cli/where.md
Failed to process any resolved URL for: /cli/where.md

Processing URL: https://raw.githubusercontent.com/.../docs/public/plugins.md
Processing URL: https://raw.githubusercontent.com/plugins.md
Failed to process any resolved URL for: /plugins.md
```

The pattern is clear:
1. Context7 fetches the `llms.txt` file ✅
2. It parses the relative links inside it (e.g., `- [CLI: where](/cli/where.md)`)
3. It correctly constructs the full absolute URL: `https://raw.githubusercontent.com/.../docs/public/cli/where.md` ✅
4. It then strips down to just the path: `https://raw.githubusercontent.com/cli/where.md` ❌
5. That second URL fails — it's clearly a raw.githubusercontent.com bug/limitation

## 🔍 Root Cause

The `llms.txt` format uses **site-relative links** (e.g., `/cli/where.md`, `/dev-tools/backends/asdf`). These are relative to the **documentation root** (`https://mise.jdx.dev/`), not to the directory containing `llms.txt`.

When `llms.txt` is hosted at `raw.githubusercontent.com`:
- The "base domain" is `raw.githubusercontent.com`
- Context7 resolves `/cli/where.md` against that domain → `https://raw.githubusercontent.com/cli/where.md`
- This is wrong — there's no such file at raw.githubusercontent.com
- The correct resolution would be `https://mise.jdx.dev/cli/where.md`

Context7 cannot know the **original documentation domain** from a raw GitHub URL. The relative links are semantically tied to `mise.jdx.dev`.

## ✅ What Would Work With Context7

Context7 **will work correctly once the PR is merged** and `https://mise.jdx.dev/llms.txt` is live, because:
- `llms.txt` is at `https://mise.jdx.dev/llms.txt`
- Relative link `/cli/where.md` resolves to `https://mise.jdx.dev/cli/where.md` ✅
- Context7 can then fetch each page from the real docs site ✅

## 🔑 Lesson Learned

> **Context7 and any llms.txt crawler requires the file to be hosted at the canonical documentation domain**, not at a proxy URL (like raw.githubusercontent.com, CDN, or intermediary host).

This is a fundamental constraint of the llms.txt format: links inside it are relative to the site root, so the host serving `llms.txt` must be the documentation site itself.

## 🔀 Alternatives for the Intermediate Period

Before the PR is merged, use tools that do **not** follow links inside `llms.txt` but instead treat the whole file as a blob:

| Tool | Approach | Works with Raw GitHub? |
|------|---------|----------------------|
| `langchain-ai/mcpdoc` | Reads llms.txt links + fetches each URL | ❌ Same domain-resolution issue |
| `fetch` MCP | Fetches `llms-full.txt` as one blob | ✅ Yes — no link following |
| `markitdown` MCP | Same as fetch, converts to markdown | ✅ Yes |
| GitMCP | Reads repo files directly via GitHub API | ✅ Yes (reads from repo) |
| `arabold/docs-mcp-server` | Can crawl `mise.jdx.dev` today | ✅ Yes (uses live site) |

**Recommended immediate workaround:** Use `arabold/docs-mcp-server` pointed at `https://mise.jdx.dev` (the existing site), which works today even before the PR is merged.
