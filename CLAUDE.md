# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Fuwari is a static blog template built with Astro 5, Tailwind CSS, and Svelte. It features smooth animations, light/dark mode, i18n support, and extended Markdown capabilities with custom rehype/remark plugins.

## Development Commands

**Package Manager**: This project uses `pnpm` exclusively (enforced via preinstall script).

```bash
pnpm install              # Install dependencies
pnpm dev                  # Start dev server at localhost:4321
pnpm build                # Build for production (includes Pagefind search index)
pnpm preview              # Preview production build locally
pnpm check                # Run Astro checks for errors
pnpm type-check           # TypeScript type checking with isolated declarations
pnpm lint                 # Lint and auto-fix code using Biome
pnpm format               # Format code using Biome
pnpm new-post <filename>  # Create new blog post with frontmatter template
```

## Architecture & Key Concepts

### Content Collections

Posts are managed via Astro Content Collections in `src/content/`:
- `posts/` - Blog posts with strict schema validation (title, published date, tags, category, draft status, etc.)
- `spec/` - Special content pages with flexible schema

The schema is defined in `src/content/config.ts` and includes internal navigation fields (`prevTitle`, `prevSlug`, `nextTitle`, `nextSlug`) for post pagination.

### Configuration System

Site configuration is centralized in `src/config.ts`:
- `siteConfig` - Site title, language, theme color (hue-based), banner settings, TOC settings, favicon
- `navBarConfig` - Navigation links (uses `LinkPreset` for common pages)
- `profileConfig` - Author profile, avatar, bio, social links
- `licenseConfig` - Content license settings
- `expressiveCodeConfig` - Code block theme configuration

**Important**: The site URL and base path are configured in `astro.config.mjs` (currently set to `https://nagame309.github.io/`).

### Markdown Pipeline

The markdown processing pipeline uses custom remark and rehype plugins:

**Remark plugins** (process Markdown AST):
1. `remarkMath` - LaTeX math support
2. `remarkReadingTime` - Calculate reading time
3. `remarkExcerpt` - Extract post excerpts
4. `remarkGithubAdmonitionsToDirectives` - Convert GitHub-style admonitions
5. `remarkDirective` - Handle custom directive syntax
6. `remarkSectionize` - Wrap sections for styling
7. `parseDirectiveNode` (custom) - Parse directive nodes into rehype components

**Rehype plugins** (process HTML AST):
1. `rehypeKatex` - Render math with KaTeX
2. `rehypeSlug` - Add IDs to headings
3. `rehypeComponents` - Render custom components:
   - `github` - GitHub repository cards
   - `note`, `tip`, `important`, `caution`, `warning` - Admonition blocks
4. `rehypeAutolinkHeadings` - Add anchor links to headings

Custom plugins are in `src/plugins/`.

### Expressive Code Configuration

Code blocks use `astro-expressive-code` with custom plugins:
- `pluginCollapsibleSections` - Collapsible code sections
- `pluginLineNumbers` - Line numbers
- `pluginLanguageBadge` (custom) - Language badges
- `pluginCustomCopyButton` (custom) - Custom copy button styling

Custom plugins are in `src/plugins/expressive-code/`. Style overrides use CSS variables (`--codeblock-bg`, `--codeblock-topbar-bg`, `--primary`) for theming.

### Layout System

Two main layouts in `src/layouts/`:
- `Layout.astro` - Base layout with head, SEO, Swup transitions, global styles
- `MainGridLayout.astro` - Grid layout with sidebar for blog pages

Components are organized by purpose:
- `components/widget/` - Sidebar widgets (Profile, Tags, Categories, TOC, DisplaySettings)
- `components/control/` - Interactive controls (BackToTop, Pagination, ButtonLink, ButtonTag)
- `components/misc/` - Utilities (ImageWrapper, License, Markdown renderer)

### Page Transitions & SPA Behavior

The site uses Swup for smooth page transitions:
- Custom animation class: `transition-swup-` (not `transition-` to avoid conflicts with Tailwind)
- Containers: `main` and `#toc`
- Features: smooth scrolling, caching, preloading, head updates

Ensure JavaScript that needs to reinitialize on page change listens for Swup events.

### Internationalization (i18n)

Language files in `src/i18n/languages/` support 10 languages (en, zh_CN, zh_TW, ja, ko, es, id, vi, th, tr). The current site language is set in `src/config.ts` (`siteConfig.lang`). Individual posts can override with `lang` frontmatter.

## Code Style & Linting

Uses Biome for formatting and linting:
- Tabs for indentation
- Double quotes for strings
- Special rules disabled for `.svelte`, `.astro`, `.vue` files
- Run `pnpm lint` before committing (as noted in CONTRIBUTING.md)
- Follow Conventional Commits format for commit messages

## Deployment

Build command: `pnpm build` (includes Pagefind search index generation)

The build outputs to `dist/`. Pagefind runs as a post-build step to create the search index.

Before deployment, update `site` and `base` in `astro.config.mjs` to match your deployment URL.

## Creating New Posts

Use `pnpm new-post <filename>` which creates a file in `src/content/posts/` with this frontmatter template:

```yaml
---
title: <filename>
published: YYYY-MM-DD
description: ''
image: ''
tags: []
category: ''
draft: false
lang: ''
---
```

Images can be relative to the post file or use paths starting with `/` for `public/` directory.
