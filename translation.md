# Translator's Guide

This guide explains how to translate the BrailleQuran website into a new language or update existing translations. No programming knowledge is required — you only need to edit Markdown (`.md`) text files.

## How Translations Work

Each language has its own folder under `content/`:

```
content/
  en/          ← English (reference language)
  tr/          ← Turkish
  de/          ← German
  fr/          ← French
```

The folder structure inside each language directory must be identical. Hugo matches pages across languages by their file path. For example, these are all translations of the same page:

```
content/en/about/mission.md   ← English version
content/tr/about/mission.md   ← Turkish version
content/de/about/mission.md   ← German version
content/fr/about/mission.md   ← French version
```

## Translating Existing Content

### Step 1: Find the English source

English (`content/en/`) is the reference language. Start by reading the English file you want to translate.

Example — `content/en/about/mission.md`:

```markdown
---
title: "Our Mission"
weight: 1
---

Our mission is to make the Quran accessible to everyone through Braille and other assistive formats.
```

### Step 2: Create the translated file

Copy the file to the same path under your language directory and translate the content:

```markdown
---
title: "Unsere Mission"
weight: 1
---

Unsere Mission ist es, den Koran durch Braille und andere unterstützende Formate für alle zugänglich zu machen.
```

Rules:
- **Translate** the `title` value
- **Keep** `weight` and other non-text fields unchanged
- **Keep** any shortcodes like `{{</* sections */>}}` exactly as-is (do not translate them)
- **Do not** rename the file — the filename must match the English version

### Step 3: Section index files

Sections (folders) have a special `_index.md` file. These must also be translated. Example:

English — `content/en/about/_index.md`:
```markdown
---
title: "About"
weight: 1
---

Learn more about the BrailleQuran project.

{{</* sections */>}}
```

German — `content/de/about/_index.md`:
```markdown
---
title: "Über uns"
weight: 1
---

Erfahren Sie mehr über das BrailleQuran-Projekt.

{{</* sections */>}}
```

The `{{</* sections */>}}` line auto-generates a list of child pages. Always keep it.

## Adding a New Language

If your language is not yet supported, follow these steps.

### 1. Ask a developer to add the language config

The developer needs to add a block to `config/_default/hugo.yaml`:

```yaml
languages:
  # ... existing languages ...
  ar:
    languageName: العربية
    weight: 5
    title: "BrailleQuran"
    contentDir: content/ar
    languageDirection: rtl    # only for RTL languages
    params:
      description: "BrailleQuran – موارد القرآن الكريم"
    menus:
      main:
        - name: الرئيسية
          pageRef: /
          weight: 1
```

### 2. Create the UI strings file

Create `i18n/<lang>.toml` with the search label translation:

```toml
[search]
other = "بحث"
```

The Ananke theme already ships with UI translations for many languages in `themes/ananke/i18n/`. Your `i18n/<lang>.toml` file only needs to contain strings that are missing or that you want to override.

### 3. Create the content directory

Mirror the English structure:

```
content/ar/
  _index.md
  about/
    _index.md
    mission.md
  resources/
    _index.md
    downloads.md
```

### 4. Translate the home page

The home page is `content/<lang>/_index.md`:

```markdown
---
title: "مرحباً"
description: "BrailleQuran – موارد القرآن الكريم"
---

مرحباً بكم في BrailleQuran. يرجى استكشاف الأقسام أدناه:

{{</* sections */>}}
```

### 5. Translate all other pages

Work through each `.md` file, translating the `title` and body text while keeping the file structure and shortcodes intact.

## Translation Checklist

For each language, ensure:

- [ ] `content/<lang>/_index.md` — Home page
- [ ] Every `_index.md` in every section (e.g. `about/_index.md`, `resources/_index.md`)
- [ ] Every leaf page (e.g. `about/mission.md`, `resources/downloads.md`)
- [ ] `i18n/<lang>.toml` — UI string for "Search"
- [ ] Menu name in `config/_default/hugo.yaml` — translated "Home" link

## File Format Reference

All content files are Markdown with YAML front matter:

```markdown
---
title: "Translated Title"    ← TRANSLATE this
weight: 1                    ← KEEP as-is
description: "Optional"      ← TRANSLATE if present
---

Translated body text here.

{{</* sections */>}}          ← KEEP as-is (do not translate)
```

## Tips

- Use the English content as the source of truth. When in doubt, match the English structure.
- File and folder names are never translated — only the content inside the files.
- You can preview your work locally by running `hugo server --environment development` and navigating to `http://localhost:1313/<lang>/`.
- If a page is not yet translated, it simply won't appear in that language's navigation. There is no need for placeholder files.
- The language switcher in the header always links to the home page of each language, so visitors can switch even if a specific page isn't translated yet.
