# Inkeep — Detailed Analysis

**Rating: ⭐⭐⭐ (Commercial with free OSS tier, self-hostable, more complex than needed)**

## 🏢 What Is Inkeep?

Inkeep is an **AI agent platform for customer experience and developer support**. It builds AI assistants from documentation and knowledge bases, similar to kapa.ai, but with a stronger focus on agentic workflows, multi-agent orchestration, and self-hosting options.

**Website:** https://inkeep.com  
**Pricing page:** https://docs.inkeep.com/pricing  
**MCP docs:** https://docs.inkeep.com/tutorials/mcp-servers/overview

## 💰 Pricing Tiers

Inkeep offers three tiers:

1. **Open Source (Free forever)**: For qualifying open-source projects
2. **Cloud**: Managed deployment (pricing on request)
3. **Enterprise**: Full platform with dedicated support (pricing on request)

The **Open Source free tier** is notable — it explicitly offers free-forever access for OSS projects. mise would very likely qualify given its GitHub presence and community size.

Source: [Inkeep pricing](https://docs.inkeep.com/pricing) (Accessed: 2026-03-03)

## 🔌 MCP Integration

Inkeep has MCP support in two directions:

### 1. Inkeep as a Documentation Search MCP Server

Inkeep exposes a RAG search MCP tool that allows AI coding assistants to query your ingested documentation. This is listed in the official MCP registry (`modelcontextprotocol/servers`).

From the official MCP server list:
> "Inkeep — RAG Search over your content powered by Inkeep"

### 2. Inkeep Agents Using External MCP Servers

Inkeep's agent platform can *consume* external MCP servers for actions (the Enterprise tier). This is the reverse direction and not relevant to the mise docs use case.

## ✅ Pros

- **Free OSS tier**: Potentially free forever for mise
- **Self-hosting option**: Unlike kapa.ai, Inkeep supports full self-hosting (Enterprise tier)
- **MCP server built-in**: Listed in the official MCP registry
- **Multi-agent architecture**: More sophisticated than simple RAG if needed later
- **A2A support**: Supports Agent-to-Agent communication (more future-proof)

## ❌ Cons

- **More complex than needed**: Inkeep is a full agent platform; mise just needs a docs search tool
- **Pricing opacity**: Cloud and Enterprise pricing requires contacting sales
- **Self-hosting complexity**: The self-hosted option requires infrastructure setup
- **Less focused on developer tools**: kapa.ai and Context7 are more specifically designed for developer documentation
- **Less proven for this use case**: Astro uses kapa.ai, not Inkeep; fewer public developer tool examples

## 📋 What mise Would Need to Do

1. Sign up at Inkeep and apply for the OSS free tier
2. Connect `mise.jdx.dev` as a knowledge source
3. Deploy the Inkeep MCP server endpoint
4. Publish the MCP configuration in mise docs

## 🆚 vs. kapa.ai

| Feature | Inkeep | kapa.ai |
|---|---|---|
| OSS free tier | ✅ Free forever | Discounted (approval) |
| Self-hosting | ✅ Yes | ❌ No |
| Search quality | ★★★★★ | ★★★★★ |
| User setup | Medium | Easy (URL only) |
| Proven for dev tools | Limited | Many examples (Astro, Expo, etc.) |
| MCP server | ✅ Yes (in MCP registry) | ✅ Yes (hosted) |

## 📊 Summary

| Criterion | Score |
|---|---|
| Search quality | ★★★★★ |
| Ease of setup (maintainers) | ★★★☆☆ |
| Ease of use (end users) | ★★★★☆ |
| Cost | ★★★★☆ (free OSS tier) |
| Vendor independence | ★★★★☆ (self-hosting available) |
| Maintenance burden | ★★★★☆ |

**Finding:** Inkeep is a credible alternative to kapa.ai for mise, especially because it offers a free OSS tier and self-hosting. However, it's more complex to set up and less proven for developer tool documentation MCP servers. Recommend as a **Phase 4** option after simpler approaches are exhausted.

---

- **Research compiled by:** GitHub Copilot
- **For project:** mise (jdx/mise)
- **Date:** March 03, 2026
