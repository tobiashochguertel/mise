# langchain-ai/mcpdoc — Detailed Analysis

**Rating: ⭐⭐⭐⭐⭐ (Best fit for llms.txt-native MCP; zero infrastructure)**

## 🏢 What Is mcpdoc?

`mcpdoc` is an **open-source MCP server by LangChain** that exposes `llms.txt` files as MCP tools. It was built because Cursor/Windsurf/Claude have inconsistent built-in handling of `llms.txt`, and mcpdoc gives full auditable control over tool calls.

**Repository:** https://github.com/langchain-ai/mcpdoc  
**License:** MIT  
**Language:** Python (installable via `uvx`, no local install needed)  
**Maintained by:** LangChain team (Anthropic ecosystem)

## 🔌 How It Works

1. You configure mcpdoc with one or more `llms.txt` URLs
2. mcpdoc exposes two MCP tools:
   - `list_doc_sources` — returns the llms.txt index (table of contents)
   - `fetch_docs` — fetches any URL listed in the llms.txt
3. AI assistant calls these tools to navigate and read docs

### Security Model

- Only fetches URLs from the **same domain** as the `llms.txt` file
- For `https://mise.jdx.dev/llms.txt`, only `mise.jdx.dev` URLs are allowed
- Prevents prompt injection via external links

## 💻 Configuration for mise

### Once the PR is merged (canonical URL):

```json
"mise-docs": {
  "tools": ["*"],
  "type": "stdio",
  "command": "uvx",
  "args": [
    "--from", "mcpdoc",
    "mcpdoc",
    "--urls", "mise:https://mise.jdx.dev/llms.txt",
    "--transport", "stdio"
  ]
}
```

### Why raw GitHub DOESN'T work with mcpdoc

mcpdoc has the same limitation as Context7: relative links in `llms.txt` resolve against the host domain. With a raw GitHub URL, links resolve to `raw.githubusercontent.com/cli/where.md` — which doesn't exist.

mcpdoc also enforces domain security — it only fetches from the `llms.txt` domain. So even if links resolved correctly, it could only fetch from `raw.githubusercontent.com`, not `mise.jdx.dev`.

### Workaround: point mcpdoc at the live site (before PR merge)

The existing `https://mise.jdx.dev` site doesn't have `llms.txt` yet, but once the PR is merged, the canonical URL works perfectly.

**Temporary workaround using `llms-full.txt` as a single file:**

mcpdoc supports `--urls` pointing at any URL, including a flat file. You can point it at `llms-full.txt` as a "single-page source":

```json
"mise-docs-temp": {
  "type": "stdio",
  "command": "uvx",
  "args": [
    "--from", "mcpdoc",
    "mcpdoc",
    "--urls", "mise-full:https://raw.githubusercontent.com/tobiashochguertel/mise/feat/docs-llms-txt-intermediate/docs/public/llms-full.txt",
    "--allowed-domains", "raw.githubusercontent.com",
    "--transport", "stdio"
  ]
}
```

This works because `llms-full.txt` contains the full docs in one file — no link-following needed.

## ⚡ Quick Test (local)

```bash
# Test it immediately
uvx --from mcpdoc mcpdoc \
  --urls "mise:https://raw.githubusercontent.com/tobiashochguertel/mise/feat/docs-llms-txt-intermediate/docs/public/llms-full.txt" \
  --allowed-domains "raw.githubusercontent.com" \
  --transport sse \
  --port 8099 \
  --host localhost
```

## 📊 Assessment

| Criterion | Score | Notes |
|-----------|-------|-------|
| **Cost** | ✅ Free | MIT, no API key, uvx only |
| **Self-hosted** | ✅ Yes | Runs locally via uvx |
| **llms.txt native** | ✅ Yes | Built specifically for this format |
| **Setup effort** | Minimal | One config block, uvx handles install |
| **Works offline** | ✅ Yes | After first fetch, can cache |
| **Semantic search** | ❌ No | Fetch-based, not vector search |
| **Works with raw GitHub** | ⚠️ Partial | Works with `llms-full.txt`, not `llms.txt` |
| **Works with `mise.jdx.dev`** | ✅ Yes | Once PR merged |

## 🏆 Recommendation

**Best zero-cost, zero-infrastructure option once the PR is merged.** Add it to `~/.copilot/mcp-config.json` after `mise.jdx.dev/llms.txt` is live. For the intermediate period, use `llms-full.txt` with `--allowed-domains`.
