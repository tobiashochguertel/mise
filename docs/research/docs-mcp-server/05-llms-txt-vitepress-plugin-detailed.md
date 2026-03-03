# vitepress-plugin-llms — Detailed Analysis

**Rating: ⭐⭐⭐⭐⭐ (Best first step — free, zero-dependency, automatic)**

## 📦 What Is vitepress-plugin-llms?

`vitepress-plugin-llms` is an npm plugin for VitePress that automatically generates **LLM-optimized documentation files** during the build:

- `llms.txt` — an index of all documentation pages with titles and links (the standard per [llmstxt.org](https://llmstxt.org))
- `llms-full.txt` — a single file containing the entire documentation in Markdown (all pages concatenated)
- `*.md` files — individual Markdown versions of each HTML page at the same URL path

**Repository:** https://github.com/okineadev/vitepress-plugin-llms  
**npm:** https://www.npmjs.com/package/vitepress-plugin-llms  
**License:** MIT  
**VitePress feature request:** [vuejs/vitepress#4590](https://github.com/vuejs/vitepress/issues/4590) (tracking native support)

## 📂 What It Generates

After building the docs, the `.vitepress/dist` folder will contain:

```
.vitepress/dist/
├── llms.txt           ← Index of all pages (llms.txt standard)
├── llms-full.txt      ← All pages concatenated (single large file)
├── index.html         ← Normal HTML page
├── index.md           ← LLM-friendly Markdown version of each page
├── mcp.html
├── mcp.md             ← e.g., mise MCP docs in clean Markdown
└── ...
```

Users of tools like Cursor can then specify `https://mise.jdx.dev` as a docs source, and Cursor will auto-discover and use `llms.txt`.

## ⚙️ How to Add to mise

### Step 1: Install the plugin

```bash
# In the mise repo root
bun add -D vitepress-plugin-llms
# or
npm install --save-dev vitepress-plugin-llms
```

### Step 2: Add to VitePress config

Edit `docs/.vitepress/config.ts`:

```typescript
import { defineConfig } from 'vitepress'
import llmstxt from 'vitepress-plugin-llms'

export default defineConfig({
  // ... existing config ...
  vite: {
    plugins: [llmstxt()],
  },
})
```

### Step 3: Build and verify

```bash
bun run docs:build
# or: mise run docs:build

# Verify the output
ls docs/.vitepress/dist/llms.txt
ls docs/.vitepress/dist/llms-full.txt
```

That's it. On every `docs:build`, both files are automatically regenerated.

## 🌐 What the llms.txt File Looks Like

```
# mise-en-place

> mise is a polyglot tool version manager. It replaces tools like asdf, nvm, pyenv, rbenv, etc.

## Documentation

- [Getting Started](https://mise.jdx.dev/getting-started.md): How to install and activate mise
- [Configuration](https://mise.jdx.dev/configuration.md): Configuration files and options
- [MCP Server](https://mise.jdx.dev/mcp.md): MCP server documentation
- [Dev Tools](https://mise.jdx.dev/dev-tools/index.md): Managing dev tools with mise
...
```

## 🤖 How AI Tools Use It

### Cursor
Cursor auto-discovers `llms.txt` when you add a docs URL to your project:
```json
{
  "docs": ["https://mise.jdx.dev"]
}
```

### Claude Code
```bash
# Claude can fetch and use llms-full.txt for context
claude --context https://mise.jdx.dev/llms-full.txt "How do I configure mise for Python?"
```

### Any AI Tool Supporting the Standard
Any tool that implements the [llmstxt.org](https://llmstxt.org) standard will auto-discover and use these files.

## 🔧 Additional Features (Optional)

### Copy/Download as Markdown Buttons

The plugin can add "Copy as Markdown" and "Download as Markdown" buttons to each documentation page:

```typescript
// In docs/.vitepress/config.ts
import { copyOrDownloadAsMarkdownButtons } from 'vitepress-plugin-llms'

export default defineConfig({
  markdown: {
    config(md) {
      md.use(copyOrDownloadAsMarkdownButtons)
    },
  },
})
```

This adds a convenient UX for users who want to copy a page into an AI chat.

### Custom Configuration

```typescript
llmstxt({
  // Custom title and description for llms.txt header
  title: 'mise-en-place Documentation',
  description: 'mise is a polyglot tool version manager...',
  
  // Exclude specific pages
  ignoreFiles: ['contributing.md', 'changelog.md'],
  
  // Generate individual .md files per page
  generateIndexMd: true,
})
```

## 📈 Ecosystem Adoption

Projects using `vitepress-plugin-llms` or similar:
- Vite (PR submitted: [vitejs/vite#19675](https://github.com/vitejs/vite/pull/19675))
- Astro also generates `llms.txt` and `llms-full.txt` alongside their kapa.ai MCP server
- Mintlify auto-generates these files for all hosted docs sites

## 🔄 Alternative: Manual llms.txt

If the plugin doesn't meet all needs, a custom VitePress build hook can generate `llms.txt` manually:

```typescript
// In docs/.vitepress/config.ts
import { createWriteStream } from 'fs'
import { resolve } from 'path'

export default defineConfig({
  buildEnd: async (siteConfig) => {
    // Custom llms.txt generation logic
  }
})
```

## 🔗 Related: llms.txt Standard

The `llms.txt` standard was proposed by [Jeremy Howard](https://www.answer.ai/posts/2024-09-03-llmstxt.html) (Answer.AI co-founder) in September 2024. It specifies:

```
# Project Name

> Short description

Optional: Additional context

## Section

- [Page Title](url): Optional description
```

The `llms-full.txt` variant concatenates all content for tools that benefit from full context.

## 📊 Summary

| Criterion | Score |
|---|---|
| Search quality | ★★★★☆ (keyword, not semantic) |
| Ease of setup (maintainers) | ★★★★★ (1 npm install + 1 config line) |
| Ease of use (end users) | ★★★★★ (auto-discovered by tools) |
| Cost | ★★★★★ (free, MIT) |
| Vendor independence | ★★★★★ (static files, no external service) |
| Maintenance burden | ★★★★★ (auto-regenerated on every build) |

**Finding:** This is the highest-ROI, lowest-risk first step for mise. It should be implemented immediately and is the foundation for everything else (Context7 indexes llms.txt, arabold/docs-mcp-server can scrape the site, kapa.ai can ingest the output).

---

- **Research compiled by:** GitHub Copilot
- **For project:** mise (jdx/mise)
- **Date:** March 03, 2026
