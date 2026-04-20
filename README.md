# BrailleQuran Website

A multilingual Hugo static site for [braillequran.org](https://braillequran.org).

Supports English, Turkish (Türkçe), German (Deutsch), and French (Français).

## Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) v0.156.0+
- Git

## Quick Start

```bash
git clone <repo-url>
cd braillequran
git submodule update --init
make setup  # configure git hooks (gitleaks pre-push)

# Development server
hugo server --environment development

# Production build
hugo --environment production
```

The dev server runs at `http://localhost:1313/` with live reload.

## Project Structure

```
config/
  _default/hugo.yaml       # Shared config (languages, menus, params)
  development/hugo.yaml    # Dev overrides (baseURL → localhost)
  production/hugo.yaml     # Prod overrides (baseURL → braillequran.org)

content/
  en/                      # English content
  tr/                      # Turkish content
  de/                      # German content
  fr/                      # French content

layouts/
  baseof.html              # Base template (breadcrumbs, search)
  partials/
    breadcrumb.html        # Breadcrumb navigation
    site-footer.html       # Footer with copyright
    i18nlist.html          # Language switcher
  shortcodes/
    sections.html          # Auto-lists child sections/pages
  _default/
    index.json             # JSON search index template

i18n/
  en.toml, tr.toml,       # UI string translations
  de.toml, fr.toml

themes/ananke/             # Ananke theme (git submodule)
```

## Configuration

Hugo config uses the [config directory](https://gohugo.io/getting-started/configuration/#configuration-directory) pattern:

- `config/_default/hugo.yaml` — shared settings (languages, menus, output formats)
- `config/development/hugo.yaml` — overrides when running `--environment development`
- `config/production/hugo.yaml` — overrides when running `--environment production`

To add a new environment (e.g. staging):

```bash
mkdir config/staging
cat > config/staging/hugo.yaml <<EOF
baseURL: "https://staging.braillequran.org/"
EOF
hugo --environment staging
```

## Content Management

Each language has its own directory under `content/`. The directory structure must mirror across languages so Hugo can link translations.

### Adding a new page

Create the Markdown file in the appropriate language directory:

```bash
# A leaf page (no children)
cat > content/en/about/team.md <<EOF
---
title: "Our Team"
weight: 2
---

Meet the team behind BrailleQuran.
EOF
```

The page appears automatically in its parent section if the parent uses `{{</* sections */>}}`.

### Adding a new section

Sections require an `_index.md` file:

```bash
mkdir -p content/en/guides

cat > content/en/guides/_index.md <<EOF
---
title: "Guides"
weight: 3
---

Browse our guides.

{{</* sections */>}}
EOF

# Add a page inside the section
cat > content/en/guides/getting-started.md <<EOF
---
title: "Getting Started"
weight: 1
---

Your content here.
EOF
```

The `{{</* sections */>}}` shortcode auto-lists all child sections and pages. Use it in any `_index.md` to create navigable section indexes.

### Front matter reference

| Field         | Required | Description                          |
|---------------|----------|--------------------------------------|
| `title`       | Yes      | Page title (used in nav, breadcrumb) |
| `weight`      | No       | Sort order (lower = first)           |
| `description` | No       | Meta description / subtitle          |
| `draft`       | No       | Set `true` to exclude from builds    |

### Removing content

Delete the `.md` file (or directory for sections). The site rebuilds without it — no config changes needed.

## Layout Customizations

The following layouts override the Ananke theme:

| File | Purpose |
|------|---------|
| `layouts/baseof.html` | Injects breadcrumbs and search bar into every page |
| `layouts/partials/site-footer.html` | Custom footer with "© {year} Braillequran.org" |
| `layouts/partials/i18nlist.html` | Language switcher showing all languages (not just translated pages) |
| `layouts/partials/breadcrumb.html` | Accessible breadcrumb trail |
| `layouts/shortcodes/sections.html` | Lists child sections and pages |
| `layouts/_default/index.json` | Generates per-language JSON index for search |

## Search

Client-side search powered by [Fuse.js](https://www.fusejs.io/). Each language gets its own `index.json` at build time. The search bar filters results to the current language only.

The JSON output is enabled via `outputs.home` in `config/_default/hugo.yaml`:

```yaml
outputs:
  home:
    - HTML
    - RSS
    - JSON
```

## Adding a New Language

See [translation.md](translation.md) for the full translator's guide.

Short version for developers:

1. Add the language block to `config/_default/hugo.yaml` under `languages:`
2. Create `content/<lang>/` and mirror the English content structure
3. Add `i18n/<lang>.toml` with UI string translations
4. Rebuild — the language switcher picks it up automatically

## Building for Production

```bash
hugo --environment production --minify
```

Output goes to `public/`. Deploy the contents of that directory to your web server or CDN.

## License

© Braillequran.org
