# CLAUDE.md — MediaPragueBoatsCz

## Project Overview

Static media hub / portfolio website for **Prague Boats** (Pražské Benátky). Showcases fleet photography, event content, sightseeing locations, and video embeds. The site is in Czech (`lang="cs"`) and marked `noindex, nofollow` (internal use only).

## Tech Stack

- **Pure static site** — HTML5, CSS3, Vanilla JavaScript (ES6+)
- **No framework**, no build system, no package manager
- **No external dependencies** — completely self-contained (no CDN)

## Repository Structure

```
/
├── index.html          # Main SPA (3,880 lines) — all content and inline JS
├── styles.css          # All styling (540 lines) — CSS variables, grid, responsive
├── logo/
│   └── logo.svg        # Prague Boats SVG logo
├── thumbs/             # Image thumbnails (~90 JPG/JPEG files, 3.9 MB)
│   └── index.html      # Copy of main index (kept in sync manually)
├── old/
│   └── index.html      # Legacy version (archived)
└── CLAUDE.md           # This file
```

## Architecture

### Single-Page Application Pattern

`index.html` is a single HTML file with anchor-based navigation:

- **Sidebar** with collapsible `<details>` groups for fleet/category navigation
- **Main content area** with image gallery cards and embedded YouTube videos
- **Hash-based routing** — `history.replaceState()` tracks current section via URL hash

### JavaScript (inline, end of index.html)

- Navigation via `[data-target]` click handlers
- Lazy image loading: `data-src` → `src` on demand
- Clipboard: `navigator.clipboard.writeText()` on `[data-clipboard-text]` buttons
- Hash restoration on page load

### CSS Conventions

- CSS custom properties for theming (defined in `:root`)
- Responsive breakpoints: 1199px, 900px, 767px
- Grid layouts for galleries (3 → 2 → 1 columns)
- Class naming: kebab-case (`.sidebar`, `.nav-group`, `.media-card`)

### Content Organization

Fleet boats are organized hierarchically with sub-sections per boat (Exterior, Interior, Events, NYE, Ads, Reels). Additional sections: Docks (3 locations), Sightseeing (10 Prague landmarks), Historical photos, Catering, Logos, Video, Events.

## Development

### Running Locally

Serve the project root with any static HTTP server:

```bash
python3 -m http.server 8000
# or
npx serve .
```

### Build & Test

- **No build step** — files are served directly
- **No test suite** — no unit tests or E2E tests configured
- **No linter** — no ESLint or Prettier configured

### Making Changes

- Content is added directly to `index.html` (no CMS or templates)
- Images go in `thumbs/` as JPG/JPEG
- Use `data-src` for lazy-loaded images, `data-clipboard-text` for copy buttons
- Keep `thumbs/index.html` in sync with root `index.html` if needed

## Git Conventions

- Author: miladvorak <dvmilan@gmail.com>
- Commit messages are short and descriptive
- `.gitattributes` enforces LF line endings (`* text=auto`)

## Key Patterns for AI Assistants

1. **No build/lint/test commands exist** — don't try to run them
2. **All JS is inline** at the bottom of `index.html` inside a `<script>` tag
3. **All CSS is in `styles.css`** — uses CSS variables, no preprocessor
4. **Media links point to Dropbox** — hardcoded external URLs
5. **Czech language** — all user-facing text is in Czech
6. **`index.html` is large** (~3,880 lines) — use line offsets when reading
7. **No environment variables or secrets** — purely static content
