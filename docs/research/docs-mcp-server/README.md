# Docs MCP Server Research

Research into adding a documentation MCP tool/server for the mise project, enabling AI assistants to query up-to-date mise documentation in real-time.

## 📁 Research Documents

### Overview & Summary

- **[00-overview.md](00-overview.md)** - Executive summary and key findings

### Detailed Analysis

1. **[01-kapa-ai-detailed.md](01-kapa-ai-detailed.md)** - kapa.ai — Commercial hosted docs-AI platform with MCP server ⭐⭐⭐ (paid, open-source program available)
2. **[02-context7-detailed.md](02-context7-detailed.md)** - Context7 (Upstash) — Freemium docs MCP server for code libraries ⭐⭐⭐⭐ (free tier, easy submission)
3. **[03-inkeep-detailed.md](03-inkeep-detailed.md)** - Inkeep — Commercial platform with free OSS tier and self-hosting ⭐⭐⭐ (freemium)
4. **[04-arabold-docs-mcp-server-detailed.md](04-arabold-docs-mcp-server-detailed.md)** - arabold/docs-mcp-server — Open-source self-hosted docs MCP ⭐⭐⭐⭐ (free, self-hosted)
5. **[05-llms-txt-vitepress-plugin-detailed.md](05-llms-txt-vitepress-plugin-detailed.md)** - vitepress-plugin-llms — Static llms.txt generation ⭐⭐⭐⭐⭐ (free, zero-dependency)
6. **[06-mise-mcp-builtin-tool-detailed.md](06-mise-mcp-builtin-tool-detailed.md)** - Built-in mise MCP docs tool — Extend existing mise MCP server ⭐⭐⭐⭐ (fully custom, zero infra)

### Comprehensive Comparison

- **[99-comparison-table.md](99-comparison-table.md)** - Side-by-side comparison of all approaches

## 🎯 Quick Findings

### TL;DR: Two-Phase Approach ✅

**Phase 1 (Immediate, zero-cost):** Add `vitepress-plugin-llms` to generate `llms.txt` + `llms-full.txt` from the VitePress docs site, and submit mise to Context7's free registry.

**Phase 2 (Enhanced):** Add a `search_docs` tool to the existing `mise mcp` command that fetches from `mise.jdx.dev/llms.txt`, giving users a native `mise mcp` docs tool without any external dependencies.

### Key Metrics Comparison

| Approach | Cost | Infra Required | Quality | Effort |
|---|---|---|---|---|
| vitepress-plugin-llms | Free | None | ★★★★☆ | 1–2 hours |
| Context7 submission | Free | None | ★★★★☆ | 30 min |
| arabold/docs-mcp-server | Free (self-host) | User's machine | ★★★★★ | Hours per user |
| kapa.ai (OSS program) | Free (if approved) | Hosted by kapa | ★★★★★ | Days (approval) |
| Inkeep OSS | Free | Self-host or cloud | ★★★★★ | Days |
| Built-in mise MCP tool | Free | None | ★★★☆☆ | 2–4 hours (Rust) |

## 🔍 Research Methodology

### Criteria Evaluated

1. **Cost** ⭐⭐⭐⭐⭐
   - Pricing for mise as an open-source project
   - Free tiers and open-source programs

2. **Infrastructure Overhead** ⭐⭐⭐⭐⭐
   - Whether mise maintainers need to host or operate a server
   - Deployment complexity

3. **Search Quality** ⭐⭐⭐⭐⭐
   - RAG vs. static file vs. keyword search
   - Citation accuracy

4. **Integration Effort** ⭐⭐⭐⭐⭐
   - How hard it is to implement for mise maintainers
   - How hard it is for users to configure

5. **Maintenance Burden** ⭐⭐⭐⭐⭐
   - Whether content updates automatically with new docs
   - Vendor lock-in risk

### Data Sources

- [mise MCP documentation](https://mise.jdx.dev/mcp.html) (Accessed: 2026-03-03)
- [kapa.ai pricing page](https://www.kapa.ai/pricing) (Accessed: 2026-03-03)
- [kapa.ai MCP server blog post](https://www.kapa.ai/blog/build-an-mcp-server-with-kapa-ai) (Accessed: 2026-03-03)
- [Astro Docs build-with-ai guide](https://docs.astro.build/en/guides/build-with-ai/) (Accessed: 2026-03-03)
- [Context7 GitHub (upstash)](https://github.com/upstash/context7) (Accessed: 2026-03-03)
- [arabold/docs-mcp-server GitHub](https://github.com/arabold/docs-mcp-server) (Accessed: 2026-03-03)
- [vitepress-plugin-llms npm](https://www.npmjs.com/package/vitepress-plugin-llms) (Accessed: 2026-03-03)
- [Inkeep pricing](https://docs.inkeep.com/pricing) (Accessed: 2026-03-03)
- [Context7 plans](https://context7.com/plans) (Accessed: 2026-03-03)
- Brave Search MCP server queries (Accessed: 2026-03-03)

## 📊 Key Results

### Current State of mise Docs

The mise documentation site (`mise.jdx.dev`) uses **VitePress** with a rich set of Markdown pages. It currently does **not** expose:
- `llms.txt` / `llms-full.txt` (returns 404)
- A dedicated docs search MCP server

The existing `mise mcp` command (experimental) provides resources about the user's **project** (tools, tasks, env, config) but has no tool for querying mise's own documentation.

**Finding:** There is a clear gap — AI assistants using `mise mcp` can manage a user's environment but cannot look up how mise itself works.

### The Astro Model (via kapa.ai)

Astro hosts a live MCP server at `https://mcp.docs.astro.build/mcp` powered by kapa.ai. This provides:
- Semantic search over all Astro docs
- Real-time updates as docs change
- Zero-install experience (users just add the URL to their MCP config)

**Finding:** This is the gold standard UX — but it requires either kapa.ai's paid service or equivalent infrastructure.

## 🎓 Recommendations by Use Case

### For mise (Recommended phased approach)

✅ **Phase 1: vitepress-plugin-llms + Context7 (zero cost, immediate)**

**Reasons:**
- Adding `vitepress-plugin-llms` to the VitePress build takes ~1 hour and generates both `llms.txt` and `llms-full.txt` automatically on every docs build
- Context7 is free for code libraries and mise is a developer tool — submit once and users can query mise docs via any Context7-compatible MCP client
- Zero ongoing infrastructure for mise maintainers

✅ **Phase 2: Built-in `search_docs` tool in `mise mcp`**

**Reasons:**
- Extending the existing Rust MCP server with a `search_docs` tool that fetches from `mise.jdx.dev/llms.txt` would give users a first-class experience directly in `mise mcp`
- No separate process, no external service, fully offline-capable if bundled

✅ **Phase 3 (Optional): Apply for kapa.ai OSS program or deploy arabold/docs-mcp-server**

**Reasons:**
- For a hosted MCP URL (`https://mcp.docs.mise.jdx.dev/mcp`), kapa.ai's open-source program may qualify
- arabold/docs-mcp-server can be user-self-hosted for private/offline use cases

### For Users Today (Without Waiting for mise Changes)

| Use Case | Recommendation |
|---|---|
| Local self-hosted docs search | arabold/docs-mcp-server pointed at mise.jdx.dev |
| Quick no-install access | Use Context7 if/when mise is added |
| Full semantic search | Wait for kapa.ai integration or use Inkeep |

## 💡 Key Insights

### 1. The llms.txt Standard Is the Lowest-Effort First Step

The `vitepress-plugin-llms` plugin is a single npm install and a one-line config change. It would generate `https://mise.jdx.dev/llms.txt` and `https://mise.jdx.dev/llms-full.txt` on every build. Many AI tools (Cursor, Claude, etc.) auto-discover these files when you provide a docs URL.

### 2. Context7 Is the Best Free Hosted Option for Code Libraries

Context7 by Upstash already indexes hundreds of developer tools. Once mise is submitted to their registry, users can type `use context7` and get mise docs in any Context7-compatible AI tool. No infrastructure required from mise maintainers.

### 3. The Existing mise MCP Server Is the Right Home for a Docs Tool

Since mise already ships `mise mcp`, adding a `search_docs` tool that fetches from the published `llms.txt` would be the most natural DX. Users who already have `mise mcp` configured get docs search for free. This is a Rust implementation (~100 lines) with no new dependencies beyond what's already in the project.

### 4. kapa.ai Requires No Self-Hosting but Has Vendor Lock-In Risk

kapa.ai is the approach Astro uses. The OSS program may make it free for mise, but it's a proprietary platform. If kapa.ai changes pricing or shuts down, the MCP endpoint breaks. The llms.txt + built-in tool approach avoids this entirely.

### 5. arabold/docs-mcp-server Is the Best Self-Hosted Alternative

For users who want full control, `arabold/docs-mcp-server` can index mise.jdx.dev and expose a local MCP server with semantic search. It's MIT-licensed, Docker-deployable, and supports optional vector embeddings via OpenAI, Ollama, etc.

## 🔗 External Resources

### Official Documentation

- [mise MCP docs](https://mise.jdx.dev/mcp.html)
- [MCP specification (modelcontextprotocol.io)](https://modelcontextprotocol.io/)
- [llms.txt standard (llmstxt.org)](https://llmstxt.org/)

### Tools & Projects

- [vitepress-plugin-llms (npm)](https://www.npmjs.com/package/vitepress-plugin-llms)
- [vitepress-plugin-llms (GitHub)](https://github.com/okineadev/vitepress-plugin-llms)
- [Context7 (GitHub)](https://github.com/upstash/context7)
- [Context7 add library](https://context7.com/add-library)
- [arabold/docs-mcp-server (GitHub)](https://github.com/arabold/docs-mcp-server)
- [kapa.ai](https://www.kapa.ai/)
- [kapa.ai OSS program](https://www.kapa.ai/pricing)
- [Inkeep](https://inkeep.com/)
- [Inkeep OSS (free)](https://docs.inkeep.com/pricing)

### Reference Implementations

- [Astro Docs MCP server](https://mcp.docs.astro.build/mcp) — Powered by kapa.ai
- [Astro build-with-AI guide](https://docs.astro.build/en/guides/build-with-ai/)
- [mise src/cli/mcp.rs](https://github.com/jdx/mise/blob/main/src/cli/mcp.rs)

## 📝 Research Date

**Conducted:** March 03, 2026

**Next Review:** Recommended when:
- kapa.ai changes its open-source program terms
- Context7 adds mise to its registry
- VitePress releases native llms.txt support (tracked in [issue #4590](https://github.com/vuejs/vitepress/issues/4590))
- The mise MCP feature moves out of experimental status

## ✅ Conclusion

**Final Recommendation: Two-Phase Approach (llms.txt + Built-in MCP tool)** ⭐⭐⭐⭐⭐

The most pragmatic path for mise is:
1. **Add `vitepress-plugin-llms`** to the docs build — zero cost, zero maintenance, static files, auto-updated on every docs deploy.
2. **Submit mise to Context7** — free, immediate benefit for users of Cursor/Claude/etc.
3. **Add a `search_docs` tool** to the existing `mise mcp` Rust server that reads from `mise.jdx.dev/llms.txt` — native, offline-capable, no external dependencies.

kapa.ai and Inkeep are excellent if the mise project wants a polished hosted MCP endpoint (like Astro), but they require either payment or approval, and add vendor dependency. Start with the free approaches first.

### Why This Approach Wins

1. ✅ Zero cost for the mise project
2. ✅ Zero additional infrastructure to maintain
3. ✅ Automatic updates whenever docs are rebuilt/deployed
4. ✅ Works with any MCP client (Context7 path) and any AI tool that understands llms.txt
5. ✅ Gives users a first-class `mise mcp` experience with no external setup

---

- **Research compiled by:** GitHub Copilot
- **For project:** mise (jdx/mise)
- **Date:** March 03, 2026
