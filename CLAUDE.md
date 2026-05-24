# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**MyStashMark** (formerly "Read Later Git") is a browser extension for saving articles to GitHub or GitLab repositories. Users click the extension icon to save articles as markdown files with YAML frontmatter, organized by date.

**Key Points:**
- Domain: `mystashmark.com` (public website)
- Repository: `mystashmark.github.io` (GitHub Pages for documentation and issue tracking)
- The actual extension source code is in a separate private/internal repository
- This repo serves as the public-facing documentation and issue tracker

## Repository Structure

This repository contains **documentation and marketing materials only**:

```
mystashmark.github.io/
├── code_readme.md          # Main technical documentation for the extension
├── PRO_FEATURES.md         # Pro tier pricing and feature details
├── assets/                 # Marketing screenshots and demo images
└── CLAUDE.md              # This file
```

## Core Architecture & How the Extension Works

### Article Save Flow
1. User clicks extension icon on any webpage
2. Content Script (`extractor.ts`) uses Mozilla's Readability library to extract article
3. Service Worker (`service-worker.ts`) handles the Git API calls
4. Article saved as markdown with YAML frontmatter to Git repository
5. Optional: Articles queued offline and synced when online

### Key Components

**Frontend (Popup & Settings UI)**
- `popup.html/ts/css` - Simple UI for saving articles
- `options.html/ts/css` - Settings configuration page for repository, token, and Pro license

**Backend (Service Worker)**
- `service-worker.ts` - Handles all Git API calls, request deduplication, queuing
- Supports GitHub and GitLab via separate API modules

**Content Extraction**
- `readability.ts` - Wraps Mozilla Readability library for article extraction
- `markdown.ts` - Converts extracted content to markdown with YAML frontmatter
- `extractor.ts` - Content script injected into pages to grab article content

**Git Integration**
- `github.ts` - GitHub REST API client
- `gitlab.ts` - GitLab API client
- Both implement request deduplication and retry logic with exponential backoff

**Storage**
- `storage.ts` - Browser storage helpers for settings and pending article queue
- Uses `chrome.storage.local` for configuration and sync queue

### Article Organization
- **Default**: `articles/YYYY/MM/article-title-TIMESTAMP.md`
- **Pro feature**: Custom save paths per repository
- Filenames include hash to prevent collisions from simultaneous saves

### Data Persistence & Sync
- Articles stored in user's own Git repository (no central server)
- Pending queue stored locally if offline, synced when online
- No data sent to MyStashMark servers (privacy-first design)

## Performance & Scale

**Optimizations:**
- **Request Deduplication** - Prevents accidental double-click saves
- **API Call Optimization** - GitLab saves reduced from 2 to 1 API call per file
- **Storage Caching** - In-memory cache for batch operations
- **Retry Logic** - Exponential backoff with jitter prevents thundering herd

**Scale Limits:**
- Articles up to 10MB supported
- Pending queue supports 1000+ articles (beyond that, consider IndexedDB migration)
- Multiple repository configurations in Pro tier
- Concurrent save operations handled with deduplication

## Browser Extension Permissions

Required permissions (defined in `manifest.json`):
- `storage` - Save configuration and pending articles
- `activeTab` - Access current tab for content extraction
- `scripting` - Inject content script for extraction
- `https://api.github.com/*` - GitHub API
- `https://github.com/*` - GitHub OAuth
- `https://gitlab.com/*` - GitLab API

## Pro Tier Features (Licensing)

When working on Pro features, note:
- License key validation and activation stored locally
- Pro features gated by license key check in service worker
- Free tier: Single repository, default folder structure
- Pro tier: Multiple repositories, custom paths, advanced file organization, labels

## Common Development Tasks

### Building & Testing
```bash
# Install dependencies
npm install

# Development build with watch mode
npm run dev

# Production build
npm run build

# Load in Chrome: Go to chrome://extensions → Enable Developer mode → Load unpacked → select dist/
# Load in Firefox: Go to about:debugging → Load Temporary Add-on → select any file in dist/
```

### Icon Assets
Extension requires icon files in `src/icons/`:
- `icon16.png` (16x16)
- `icon32.png` (32x32)
- `icon48.png` (48x48)
- `icon128.png` (128x128)

### Testing the Extension
- **Popup UI**: Click extension icon, verify settings form works
- **Saving articles**: Test on real websites (e.g., Medium, news sites)
- **Queue sync**: Test offline mode by disconnecting network, then reconnecting
- **Pro features**: Activate license key and verify multi-repo and custom path functionality

## Important Implementation Details

### YAML Frontmatter Format
All saved articles use this structure:
```yaml
---
title: "Article Title"
url: "https://source.url"
saved_at: "2025-01-10T15:30:00Z"
tags: ["tag1", "tag2"]
status: "unread"
source: "browser"
author: "Author Name"
site: "Site Name"
---
```

### Error Handling
- Token validation errors → Tell user to check settings
- Repository not found → Verify owner/repo combination
- Network errors → Queue locally and retry with exponential backoff
- File collision → Filename includes hash; collisions extremely rare

### Token Storage Security
- Personal Access Tokens stored only in `chrome.storage.local` (encrypted by browser)
- Never sent to MyStashMark servers
- Users responsible for token security (regenerate if compromised)

## Documentation Files in This Repo

- `code_readme.md` - Main technical documentation (covers installation, features, project structure)
- `PRO_FEATURES.md` - Pricing, licensing, and Pro feature details
- `assets/` - Marketing screenshots and demo materials

When updating documentation, keep these separate:
- **code_readme.md** - Technical setup, architecture, permissions
- **PRO_FEATURES.md** - Pricing, feature comparisons, licensing FAQs

## Related Resources

- Main website: `mystashmark.com`
- Extension source: Separate repository (not this one)
- Issue tracking: Use GitHub issues in this repository
- License: MIT
