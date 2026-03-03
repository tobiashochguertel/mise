# Built-in mise MCP Docs Tool — Detailed Analysis

**Rating: ⭐⭐⭐⭐ (Best long-term native DX; requires Rust implementation)**

## 🎯 Concept

Extend the existing `mise mcp` Rust server (`src/cli/mcp.rs`) with a new `search_docs` tool that allows AI assistants to query mise's own documentation. This would give users who already have `mise mcp` configured automatic access to docs search without any additional setup.

## 🔌 How It Would Work

### Proposed Tool: `search_docs`

```rust
/// Search mise documentation for a given query
#[tool(description = "Search mise documentation for information about commands, configuration, and usage")]
async fn search_docs(
    &self,
    Parameters(SearchDocsParams { query, max_results }): Parameters<SearchDocsParams>,
) -> std::result::Result<CallToolResult, ErrorData> {
    // 1. Fetch llms.txt from mise.jdx.dev
    // 2. Parse the index to find relevant sections
    // 3. Optionally fetch individual .md pages for context
    // 4. Return relevant content
}
```

### Parameters

```rust
struct SearchDocsParams {
    /// The search query (e.g., "how to configure tasks", "install python")
    query: String,
    /// Maximum number of results to return (default: 5)
    max_results: Option<usize>,
}
```

### Proposed Resource: `mise://docs`

In addition to the tool, a new resource could be added:

```
mise://docs                  → Returns the llms.txt index
mise://docs/mcp              → Returns the MCP page as Markdown
mise://docs/configuration    → Returns the configuration page as Markdown
```

This mirrors the pattern already used for `mise://tools`, `mise://tasks`, etc.

## 🛠️ Implementation Design

### Option A: Fetch llms.txt + Individual Pages (Recommended)

```rust
async fn search_docs(&self, ...) -> ... {
    // 1. Fetch https://mise.jdx.dev/llms.txt
    let llms_index = fetch_url("https://mise.jdx.dev/llms.txt").await?;
    
    // 2. Parse index entries
    let entries = parse_llms_txt(&llms_index);
    
    // 3. Score entries by query relevance (simple keyword matching)
    let scored = score_entries(&entries, &params.query);
    
    // 4. Fetch top N pages as Markdown
    let top_results = &scored[..params.max_results.unwrap_or(5)];
    let content = fetch_pages_markdown(top_results).await?;
    
    // 5. Return combined content
    Ok(CallToolResult::success(vec![Content::text(content)]))
}
```

### Option B: Bundle llms-full.txt at Build Time

Alternatively, `llms-full.txt` could be downloaded and bundled into the mise binary at build time (using `build.rs`):

```rust
// build.rs
fn main() {
    // Download llms-full.txt during build
    let response = reqwest::blocking::get("https://mise.jdx.dev/llms-full.txt")?;
    let content = response.text()?;
    
    // Write to OUT_DIR for inclusion
    let out = env::var("OUT_DIR")?;
    fs::write(format!("{}/mise_docs.txt", out), content)?;
}
```

```rust
// In mcp.rs
static MISE_DOCS: &str = include_str!(concat!(env!("OUT_DIR"), "/mise_docs.txt"));
```

**Pros of Option B:** Works offline; no HTTP call at runtime.  
**Cons:** Docs become stale between releases; larger binary size; requires llms.txt to be published first.

**Recommendation:** Start with Option A (live fetch), add Option B as a fallback.

### Option C: Scrape and Search at Runtime

A more sophisticated approach would use a simple BM25/TF-IDF algorithm over the docs content. Since this is Rust, crates like `tantivy` could be used, but this adds significant complexity. Not recommended for the initial implementation.

## 📋 Implementation Checklist

- [ ] Prerequisite: `vitepress-plugin-llms` must be added to docs build (see [05-llms-txt-vitepress-plugin-detailed.md](05-llms-txt-vitepress-plugin-detailed.md))
- [ ] Add `search_docs` tool to `MiseServer` in `src/cli/mcp.rs`
- [ ] Add `SearchDocsParams` struct with `#[derive(JsonSchema)]`
- [ ] Implement HTTP fetch for `llms.txt` and doc pages (reuse existing `crate::http` client)
- [ ] Parse `llms.txt` format (simple Markdown parsing)
- [ ] Add keyword scoring (simple, no external crates needed)
- [ ] Add `mise://docs` resource (optional, for full index)
- [ ] Update `AFTER_LONG_HELP` string
- [ ] Update `src/cli/mcp.rs` doc comment
- [ ] Add/update docs in `docs/mcp.md`
- [ ] Add e2e test in `e2e/` directory

## 🔄 HTTP Client Considerations

The mise codebase already uses `reqwest` for HTTP requests (for tool downloads, registry access, etc.). The `search_docs` tool can reuse the same HTTP client with:

```rust
// Respect mise's existing HTTP settings (timeout, proxy, etc.)
use crate::http::HTTP;

let content = HTTP.get("https://mise.jdx.dev/llms.txt")
    .send()
    .await?
    .text()
    .await?;
```

## 🔒 Privacy Considerations

The `search_docs` tool makes outbound HTTP requests to `mise.jdx.dev`. This is consistent with other mise behavior (checking for updates, fetching tool versions). However, the tool should:
- Only make requests when explicitly called by an AI assistant
- Not make requests during normal `mise` usage
- Respect any proxy settings configured by the user

## 📝 Docs Update

Once implemented, the `docs/mcp.md` page should be updated with:

```markdown
### `search_docs`

Search the mise documentation for information about commands, configuration, and usage.

**Parameters:**
- `query` (required, string): Search query (e.g., "configure tasks", "install python 3.12")
- `max_results` (optional, integer): Maximum results (default: 5)

**Example:**
```json
{
  "query": "how to configure task dependencies",
  "max_results": 3
}
```

When the AI assistant calls this tool, it will fetch relevant pages from `mise.jdx.dev` and return their content as Markdown.
```

## ✅ Pros

- **Zero external service**: No kapa.ai, no Context7, no Inkeep dependency
- **Native DX**: Users who already have `mise mcp` configured get docs search automatically
- **No new process**: Reuses the existing `mise mcp` server
- **Offline option**: Can bundle docs at build time as a fallback
- **Always accurate**: Fetches from the live documentation site

## ❌ Cons

- **Requires Rust implementation**: ~100-200 lines of Rust, moderate complexity
- **Depends on llms.txt being generated**: Prerequisite is the VitePress plugin
- **Simple keyword search**: Not as sophisticated as RAG (though adequate for most queries)
- **Requires internet access**: For live fetch (mitigated by bundled fallback)
- **No analytics**: Cannot see what docs questions users ask (unlike kapa.ai)

## 📊 Summary

| Criterion | Score |
|---|---|
| Search quality | ★★★☆☆ (keyword, improves with llms-full.txt) |
| Ease of setup (maintainers) | ★★★★☆ (Rust implementation needed) |
| Ease of use (end users) | ★★★★★ (zero additional config) |
| Cost | ★★★★★ (free) |
| Vendor independence | ★★★★★ (no external services) |
| Maintenance burden | ★★★★★ (auto-updates from llms.txt) |

**Finding:** This is the best long-term solution for mise's native MCP users. It should be implemented after `vitepress-plugin-llms` is added (which provides the content source). The implementation is straightforward given mise's existing HTTP infrastructure.

---

- **Research compiled by:** GitHub Copilot
- **For project:** mise (jdx/mise)
- **Date:** March 03, 2026
