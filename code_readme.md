# My Stash Mark

A browser extension that saves articles directly to your GitHub or GitLab repository for reading later. No server required - your data stays in your own git repository.

## Features

- **One-click save** - Save any article from your browser with a single click
- **Full article extraction** - Extracts readable content using Mozilla's Readability library
- **Git-based storage** - Articles stored as markdown files in your own repository
- **Cross-browser** - Works on Chrome and Firefox (Manifest V3)
- **Offline support** - Queue articles when offline, sync when back online
- **Tags** - Organize articles with custom tags
- **Dark mode** - Automatic dark mode support

## Performance Optimizations

The extension includes several performance optimizations:

- **Request Deduplication**: Prevents duplicate saves from accidental double-clicks
- **API Call Optimization**: Reduces GitLab API calls from 2 to 1 per file save
- **Storage Caching**: In-memory cache reduces redundant storage reads during batch operations
- **Retry Logic**: Exponential backoff with jitter prevents thundering herd during network failures
- **Content Extraction**: Efficient multi-pass DOM approach for typical article sizes

### Scalability

The extension is designed to handle:
- Articles up to 10MB in size
- Pending queue of 1000+ articles
- Multiple repository configurations
- Concurrent save operations with deduplication

*Note: For queues exceeding 1000 articles, consider migrating to IndexedDB for better performance.*

## Why Git-based storage?

- **No server to maintain** - Uses GitHub/GitLab APIs directly
- **Version control** - Full history of your saved articles
- **Data portability** - Plain markdown files you can read anywhere
- **Free hosting** - Uses your existing GitHub/GitLab account
- **Privacy** - Your data stays in your private repository

## Documentation

- **[User Guide](USER_GUIDE.md)** - Step-by-step setup instructions for beginners
- **Developer documentation** - See below for building from source

---

## Installation

### From Source

1. Clone this repository:
   ```bash
   git clone https://github.com/your-username/my-stash-mark.git
   cd my-stash-mark
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Build the extension:
   ```bash
   npm run build
   ```

4. Load the extension in your browser:

   **Chrome:**
   - Go to `chrome://extensions/`
   - Enable "Developer mode"
   - Click "Load unpacked"
   - Select the `dist` folder

   **Firefox:**
   - Go to `about:debugging#/runtime/this-firefox`
   - Click "Load Temporary Add-on"
   - Select any file in the `dist` folder

## Setup

1. Create a repository on GitHub or GitLab to store your articles
2. Create a Personal Access Token:
   - **GitHub**: [Create token](https://github.com/settings/tokens/new?description=Read%20Later%20Git&scopes=repo) with `repo` scope
   - **GitLab**: [Create token](https://gitlab.com/-/user_settings/personal_access_tokens) with `api` and `write_repository` scopes
3. Click the extension icon and go to Settings
4. Enter your repository details and token
5. Click "Save Settings"

## Usage

1. Navigate to any article you want to save
2. Click the extension icon
3. (Optional) Add tags separated by commas
4. Click "Save Article"

Your article will be saved as a markdown file in your repository:

```
articles/
├── 2025/
│   └── 01/
│       ├── article-title-12345678.md
│       └── another-article-12345679.md
```

## Article Format

Articles are saved as markdown with YAML frontmatter:

```markdown
---
title: "Article Title"
url: "https://example.com/article"
saved_at: "2025-01-10T15:30:00Z"
tags: ["tech", "programming"]
status: "unread"
source: "browser"
author: "Author Name"
site: "Example Site"
---

# Article Title

> Original: [https://example.com/article](https://example.com/article)

[Article content in markdown format...]
```

## Development

```bash
# Install dependencies
npm install

# Build with watch mode
npm run dev

# Production build
npm run build
```

### Project Structure

```
my-stash-mark/
├── src/
│   ├── manifest.json           # Extension manifest
│   ├── background/
│   │   └── service-worker.ts   # Background processing
│   ├── content/
│   │   └── extractor.ts        # Page content extraction
│   ├── popup/
│   │   ├── popup.html
│   │   ├── popup.ts
│   │   └── popup.css
│   ├── options/
│   │   ├── options.html
│   │   ├── options.ts
│   │   └── options.css
│   ├── lib/
│   │   ├── api/
│   │   │   ├── github.ts       # GitHub API client
│   │   │   └── gitlab.ts       # GitLab API client
│   │   ├── readability.ts      # Content extraction
│   │   ├── markdown.ts         # Markdown formatting
│   │   └── storage.ts          # Browser storage helpers
│   ├── types/
│   │   └── index.ts
│   └── icons/
├── dist/                        # Built extension
├── package.json
├── tsconfig.json
└── vite.config.ts
```

## Adding Icons

The extension requires icon files in the following sizes:
- `icon16.png` (16x16)
- `icon32.png` (32x32)
- `icon48.png` (48x48)
- `icon128.png` (128x128)

Place these in `src/icons/` before building.

## Permissions

The extension requires these permissions:
- `storage` - Store configuration and pending articles
- `activeTab` - Access current tab to extract content
- `scripting` - Inject content script for article extraction
- `https://api.github.com/*` - GitHub API access
- `https://github.com/*` - GitHub OAuth (if needed)
- `https://gitlab.com/*` - GitLab API access

## Privacy

- Your Personal Access Token is stored locally in your browser
- No data is sent to any server except GitHub/GitLab
- All article content is extracted locally
- Your reading list stays in your own repository

## Troubleshooting

### "Extension not configured" error
Go to the extension settings and enter your repository details and token.

### "Cannot access repository" error
- Make sure the repository exists
- Verify your token has the correct permissions
- Check that you've entered the correct owner/username

### Articles not appearing in repository
- Check the extension popup for pending sync status
- Make sure you're online
- Try clicking "Sync Now" in the popup

## License

MIT License - see [LICENSE](LICENSE) for details.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.
