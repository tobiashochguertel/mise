# kapa.ai — Detailed Analysis

**Rating: ⭐⭐⭐ (Excellent product, paid commercial, OSS program may qualify)**

## 🏢 What Is kapa.ai?

kapa.ai is a **commercial AI platform** that builds accurate AI assistants from technical documentation. It ingests your docs from 50+ source types (VitePress sites, Markdown, GitHub, Notion, Confluence, etc.) and creates a RAG (Retrieval-Augmented Generation) pipeline that:

- Answers developer questions with citations
- Embeds into doc sites as a chat widget
- Integrates with Slack/Discord for community support
- Provides a hosted MCP server endpoint (new feature)

The tagline is: *"AI Assistants Built from Technical Documentation"*. It powers documentation AI for OpenAI, Monday.com, Logitech, and 200+ other companies.

## 🔌 kapa.ai MCP Server Feature

kapa.ai recently launched **one-click hosted MCP servers**. This is exactly what Astro uses at `https://mcp.docs.astro.build/mcp`.

How it works:
1. You connect your documentation sources to kapa.ai (the Kapa knowledge base)
2. In your kapa dashboard, click "Deploy MCP Server"
3. kapa generates a subdomain URL like `https://mcp.docs.yourproject.com/mcp`
4. Users add this URL to their AI tool's MCP config — no local install needed
5. When an AI assistant calls the MCP, kapa runs RAG over your docs and returns cited answers

### MCP Endpoint Details (from Astro's implementation)

```json
{
  "mcpServers": {
    "mise-docs": {
      "type": "http",
      "url": "https://mcp.docs.mise.jdx.dev/mcp"
    }
  }
}
```

Transport: **Streamable HTTP** (the new MCP 2025-03-26 transport).

## 💰 Pricing

kapa.ai is a **paid commercial service** with:
- **14-day free trial** (no credit card required)
- **Paid plans**: pricing not publicly listed; requires contacting sales
- **Open Source Program**: qualifying OSS projects may get free or discounted access

### Open Source Program

> "At kapa.ai, we believe in the power of open source... qualifying open source projects [get] access to our AI-powered support and onboarding bots at a discounted rate or entirely free of charge."

— [kapa.ai pricing page](https://www.kapa.ai/pricing) (Accessed: 2026-03-03)

**mise likely qualifies** for this program as a well-known open-source developer tool with a large community.

## ✅ Pros

- **Best-in-class search quality**: RAG with citation, semantic understanding, hallucination prevention
- **Zero infrastructure**: Hosted by kapa, mise doesn't run any server
- **Auto-updates**: kapa re-indexes docs on schedule
- **One-click MCP deployment**: No coding required
- **Analytics**: Dashboard shows what questions devs ask, reveals doc gaps
- **Model-agnostic**: Can use GPT-4, Claude, or custom LLMs
- **Used by Astro**: Proven model for a developer tool of comparable size

## ❌ Cons

- **Paid commercial service**: Not free by default (though OSS program may qualify)
- **Vendor lock-in**: If kapa.ai changes pricing or shuts down, the MCP endpoint breaks
- **Approval process**: OSS program requires applying and waiting for approval
- **Privacy**: Documentation is processed by kapa's servers (not a concern for public docs, but worth noting)
- **No self-hosting** (the commercial product; not open source)

## 🔄 How Astro Uses It

From Astro's build-with-AI guide:
> "The Astro Docs MCP server uses the kapa.ai API to maintain an up-to-date index of the Astro documentation."

The server is described as:
- **Free** for end users
- **Open-source** (the MCP endpoint itself)
- **Runs remotely** (nothing to install locally)

## 📋 What mise Would Need to Do

1. Sign up at kapa.ai and apply for the OSS program
2. Connect `mise.jdx.dev` as a documentation source
3. Deploy the MCP server (one click in dashboard)
4. Publish the MCP URL in mise docs
5. Add `mcp.docs.mise.jdx.dev` CNAME if desired for custom domain

**Estimated timeline**: 1–3 days (including OSS program approval wait)

## 🆚 vs. Other Options

Compared to building a custom solution:
- kapa.ai provides far better search quality (semantic RAG vs. keyword search)
- But has vendor dependency and potential cost
- The llms.txt + Context7 approach achieves 80% of the benefit for 0% of the cost

## 📊 Summary

| Criterion | Score |
|---|---|
| Search quality | ★★★★★ |
| Ease of setup | ★★★★★ (for maintainers) |
| Ease of use (end users) | ★★★★★ |
| Cost | ★★☆☆☆ (requires OSS approval for free) |
| Vendor independence | ★★☆☆☆ |
| Maintenance burden | ★★★★★ |

---

- **Research compiled by:** GitHub Copilot
- **For project:** mise (jdx/mise)
- **Date:** March 03, 2026
