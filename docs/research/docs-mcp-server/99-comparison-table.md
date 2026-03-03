# Docs MCP Server — Comprehensive Comparison Table

## 📊 Feature Comparison

| Feature | kapa.ai | Context7 | Inkeep | arabold/docs-mcp | vitepress-plugin-llms | Built-in mise MCP tool |
|---|---|---|---|---|---|---|
| **Cost for mise** | Free (OSS program, if approved) | Free | Free (OSS tier) | Free (self-hosted) | Free | Free |
| **Infrastructure owned by mise** | None | None | None (or self-host) | N/A (user runs it) | None (static files) | None |
| **Hosted URL for users** | ✅ Yes (e.g., `mcp.docs.mise.jdx.dev`) | ✅ Yes (context7.com) | ✅ Yes (or self-host) | ❌ No (each user runs locally) | ❌ No (static file discovery) | ❌ No (part of `mise mcp`) |
| **Search type** | Semantic RAG | Semantic + version-specific | Semantic RAG | Semantic (with embeddings) | Keyword (static index) | Keyword |
| **Search quality** | ★★★★★ | ★★★★☆ | ★★★★★ | ★★★★★ (with embeddings) | ★★★☆☆ | ★★★☆☆ |
| **Auto-updates from docs** | ✅ Yes | ✅ Yes | ✅ Yes | ❌ Manual re-index per user | ✅ Every docs build | ✅ Live fetch |
| **User setup effort** | Zero install (URL) | `npx` install | URL or `npx` | `npx` or Docker | Auto-discovered | Zero (part of `mise mcp`) |
| **Open source** | ❌ No (proprietary) | ✅ Yes (MIT) | 🔶 Partially | ✅ Yes (MIT) | ✅ Yes (MIT) | ✅ Yes (already in mise) |
| **Self-hostable** | ❌ No | ❌ No | ✅ Yes (Enterprise) | ✅ Yes | N/A (static files) | ✅ Yes (with mise) |
| **Vendor dependency** | High | Medium | Medium | None | None | None |
| **Analytics** | ✅ Full dashboard | Limited | ✅ Yes | ❌ No | ❌ No | ❌ No |
| **MCP transport** | Streamable HTTP | stdio (npx) | HTTP | SSE / HTTP | N/A (file) | stdio (part of `mise mcp`) |
| **Works offline** | ❌ No | ❌ No | ❌ No (cloud) | ✅ Yes | ✅ Partially (llms-full.txt) | ✅ Yes (bundled option) |
| **Approval/wait time** | Days (OSS program) | Hours (auto-index) | Days (OSS signup) | None | None | Depends on PR review |
| **Implementation effort** | Low (dashboard + form) | Minimal (web form) | Low-Medium | N/A (user setup) | Very low (npm + 1 line) | Medium (Rust, ~100-200 lines) |

## 💰 Cost Comparison

| Provider | mise Cost | User Cost | Notes |
|---|---|---|---|
| kapa.ai | Free (if OSS approved) | Free | Requires OSS program approval |
| Context7 | Free | Free up to 50 queries/day | Usage limits on free tier |
| Inkeep | Free (OSS tier) | Free | Free OSS tier for qualifying projects |
| arabold/docs-mcp-server | Free | Free (self-hosted) | Optional: LLM API costs for embeddings |
| vitepress-plugin-llms | Free | Free | Static files, no runtime cost |
| Built-in mise MCP tool | Free | Free | Requires `mise mcp` (free) |

## 🏆 Rating Summary

| Approach | Overall Rating | Best For |
|---|---|---|
| **vitepress-plugin-llms** | ⭐⭐⭐⭐⭐ | First step — foundation for everything else |
| **Context7** | ⭐⭐⭐⭐ | Free hosted MCP for users of Cursor/Claude |
| **Built-in mise MCP tool** | ⭐⭐⭐⭐ | Native integration for `mise mcp` users |
| **arabold/docs-mcp-server** | ⭐⭐⭐⭐ | Power users wanting full local control |
| **kapa.ai** | ⭐⭐⭐ | Premium hosted MCP endpoint (if OSS approved) |
| **Inkeep** | ⭐⭐⭐ | Alternative to kapa.ai with self-hosting option |

## 🗺️ Decision Matrix

Use this matrix to pick the right approach based on your goal:

| Goal | Recommended Approach |
|---|---|
| Quickest path to any improvement | vitepress-plugin-llms (1–2 hours) |
| Free hosted MCP URL for users | Context7 (submit mise to registry) |
| Best search quality, no infrastructure | kapa.ai OSS program |
| Full control, self-hosted | arabold/docs-mcp-server |
| Native `mise mcp` experience | Built-in `search_docs` tool |
| Zero vendor lock-in | vitepress-plugin-llms + built-in tool |

## 📐 Architecture Comparison

### kapa.ai / Inkeep (Hosted RAG)
```
User AI Tool → kapa MCP endpoint → kapa RAG pipeline → mise docs (indexed)
                (mcp.docs.mise.jdx.dev)
```

### Context7 (Hosted Index)
```
User AI Tool → Context7 MCP (npx) → Context7 index → mise.jdx.dev (fetched)
```

### arabold/docs-mcp-server (Self-hosted)
```
User AI Tool → Local MCP (localhost:6280) → Local index → mise.jdx.dev (scraped)
```

### vitepress-plugin-llms (Static Files)
```
AI Tool → mise.jdx.dev/llms.txt    (discovered)
       → mise.jdx.dev/llms-full.txt (full content)
       → mise.jdx.dev/mcp.md        (per-page)
```

### Built-in mise MCP Tool
```
User AI Tool → mise mcp (stdio) → search_docs tool → mise.jdx.dev/llms.txt (fetch)
```

## 🔗 Recommended Implementation Sequence

```
Phase 1 (Hours): Add vitepress-plugin-llms → llms.txt / llms-full.txt published
     ↓
Phase 2 (30 min): Submit mise to Context7 → free hosted MCP for users  
     ↓
Phase 3 (Days): Implement search_docs in mise mcp → native docs tool
     ↓
Phase 4 (Optional): Apply for kapa.ai OSS program → polished hosted MCP URL
```

---

- **Research compiled by:** GitHub Copilot
- **For project:** mise (jdx/mise)
- **Date:** March 03, 2026
