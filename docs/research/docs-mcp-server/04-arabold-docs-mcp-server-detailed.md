# arabold/docs-mcp-server — Detailed Analysis

**Rating: ⭐⭐⭐⭐ (Best self-hosted open-source option; runs on user's machine)**

## 🏢 What Is arabold/docs-mcp-server?

`arabold/docs-mcp-server` (also called "Grounded Docs MCP Server") is an **open-source, self-hosted MCP server** for indexing and searching documentation. It is explicitly positioned as a free alternative to Context7, Nia, and Ref.Tools.

**Repository:** https://github.com/arabold/docs-mcp-server  
**License:** MIT  
**Runtime:** Node.js 22+ (or Docker)  
**Stars:** Growing (actively maintained as of 2026-03)

## 🔌 How It Works

1. User runs the server locally (or in Docker)
2. User adds documentation sources via a web UI or CLI (websites, GitHub repos, npm packages, local files)
3. The server scrapes and indexes the content (with optional vector embeddings for semantic search)
4. An MCP endpoint is exposed for AI clients to query

### Quick Start

```bash
# Start the server
npx @arabold/docs-mcp-server@latest

# Open web UI at http://localhost:6280
# Add mise.jdx.dev as a documentation source

# Connect your AI client
```

```json
{
  "mcpServers": {
    "docs-mcp-server": {
      "type": "sse",
      "url": "http://localhost:6280/sse"
    }
  }
}
```

### Docker Deployment

```bash
docker run --rm \
  -v docs-mcp-data:/data \
  -v docs-mcp-config:/config \
  -p 6280:6280 \
  ghcr.io/arabold/docs-mcp-server:latest \
  --protocol http --host 0.0.0.0 --port 6280
```

## 🧠 Embedding Models

The server supports optional vector embeddings for semantic search:

```bash
# OpenAI
OPENAI_API_KEY="sk-proj-..." npx @arabold/docs-mcp-server@latest

# Ollama (fully local, free)
OLLAMA_BASE_URL="http://localhost:11434" npx @arabold/docs-mcp-server@latest

# Azure, Gemini, and others also supported
```

Without an embedding model, it falls back to keyword search. With embeddings, it provides true semantic search.

## 📚 Supported Source Types

- Websites (crawled, including `mise.jdx.dev`)
- GitHub repositories
- npm packages
- PyPI packages
- Local folders and zip archives
- HTML, Markdown, PDF, Word (.docx), Excel, PowerPoint, source code

## ✅ Pros

- **100% free**: MIT-licensed, no API keys required for basic use
- **Fully private**: Your code and queries never leave your machine
- **Self-hosted**: Complete control, no vendor dependency
- **Semantic search**: When paired with OpenAI or Ollama embeddings
- **Web UI**: Easy source management at `http://localhost:6280`
- **Docker support**: Easy deployment for teams
- **Multiple source types**: Can index GitHub, websites, local files
- **Version-specific**: Can target a specific URL or branch

## ❌ Cons

- **Requires user setup**: Each user must run the server and add `mise.jdx.dev` as a source
- **Not a hosted solution**: mise maintainers cannot publish "here's our MCP URL, just add it"
- **Local resources**: Indexing large docs sites requires disk space and CPU
- **Maintenance per user**: Each user must re-index when mise docs update (though scheduling is possible)
- **Not zero-install**: Requires Node.js 22+ or Docker

## 🎯 Use Case for mise

This is the recommended self-hosted option for **users who want full control**. For example:

1. A user working offline or in an air-gapped environment
2. A team that wants a shared local docs server
3. Users who don't want any cloud service

### Recommended User Workflow

```bash
# 1. Start the server
npx @arabold/docs-mcp-server@latest

# 2. Add mise docs (one-time setup via web UI)
open http://localhost:6280
# → Add source → Website → https://mise.jdx.dev

# 3. Configure AI client
# Add to claude_desktop_config.json / .cursor/mcp.json
```

## 📊 Summary

| Criterion | Score |
|---|---|
| Search quality | ★★★★★ (with embeddings) |
| Ease of setup (maintainers) | N/A — runs on user side |
| Ease of use (end users) | ★★★☆☆ (requires local install) |
| Cost | ★★★★★ (free) |
| Vendor independence | ★★★★★ (fully local) |
| Maintenance burden | ★★★☆☆ (per-user, manual re-index) |

**Finding:** Ideal for power users who want full control. Not suitable as the primary "official" mise docs MCP server recommendation because it cannot be hosted by mise maintainers for all users. Should be documented as an option for self-hosting in the mise MCP docs.

---

- **Research compiled by:** GitHub Copilot
- **For project:** mise (jdx/mise)
- **Date:** March 03, 2026
