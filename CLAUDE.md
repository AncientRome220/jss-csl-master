# CLAUDE.md

## Project overview

This repository contains CSL-M (Citation Style Language Multilingual) style files for use with Zotero and Juris-M. It is forked from https://github.com/keisato0/jss-csl. The owner is 藤本俊哉.

- **jss-lewis-sato.csl** — Japan Sociological Society (社会学評論) style. Modified from lewis-original.csl by Jonathan Lewis.
- **jss-history.csl** — Japanese history journal style. Based on jss-lewis-sato.csl with a custom bibliography macro for Japanese entries and language-specific formatting for multilingual entries.
- **lewis-original.csl** — The original by Jonathan Lewis. Not modified.

## CSL-M basics

These files use CSL-M (`version="1.1mlz1"`), a superset of standard CSL 1.0.1. Key CSL-M features used here:

- **Multiple `<layout>` elements with `locale` attribute** — Zotero routes each item to the layout matching its Language field. Currently supported:
  - `locale="ja"` → Japanese (layout uses Japanese formatting)
  - `locale="de"` → German (layout uses German formatting)
  - `locale="fr"` → French (layout uses French formatting)
  - `locale="it"` → Italian (layout uses Italian formatting)
  - `locale="es"` → Spanish (layout uses Spanish formatting)
  - Default (no locale attribute) → English and other languages
  
  **How it works:** Each bibliography item in Zotero has a Language field (e.g., `ja`, `de`, `fr`, `it`, `es`). When generating a bibliography, Zotero matches this field to the corresponding layout or uses the default layout.

- **`<alternative>` / `alternative-text`** — For "再録：" (reprinted-in) citations. Juris-M only; silently ignored by Zotero.
- **`alt-*` variables** (`alt-title`, `alt-issued`, etc.) — Juris-M only.
- Macro names use 半角 (half-width, for English entries) and 全角 (full-width, for Japanese entries) suffixes to distinguish the two layout styles. Language-specific suffixes (`-de`, `-fr`, `-it`, `-es`) are used for German, French, Italian, and Spanish.

## Language-specific formatting in jss-history.csl

### Supported languages

| Language | Locale | Author Connector | Editor Term | Example |
|----------|--------|------------------|-------------|---------|
| English | (default) | and | Ed. / Eds. | Smith and Jones |
| Japanese | ja | ・ (middle dot) | 編者 | 田中・佐藤編 |
| German | de | **und** | **Hrsg.** | Mueller und Schmidt |
| French | fr | **et** | **éd.** | Dupont et Martin |
| Italian | it | **e** | **a cura di** | Rossi e Bianchi |
| Spanish | es | **y** | **ed.** | García y López |

### Setting the Language field in Zotero

Each bibliography item must have its Language field set correctly for the appropriate layout to be applied:

1. In Zotero 7/8, open an item
2. Find the **Language** field (typically under "Advanced" or scroll down)
3. Set it to:
   - `en` or leave blank → English layout (default)
   - `ja` → Japanese layout
   - `de` → German layout
   - `fr` → French layout
   - `it` → Italian layout
   - `es` → Spanish layout

Example workflow for creating a mixed-language bibliography:
- German book: Set Language to `de`; generate bibliography → shows "Mueller und Schmidt" (not "Mueller and Schmidt") and "Hrsg." (not "Ed.")
- French article: Set Language to `fr` → shows "et" and "éd."
- Japanese book: Set Language to `ja` → shows "・" delimiter and Japanese formatting per `history-bibliography-ja` macro
- English article: Leave Language blank or set to `en` → shows "and" and "Ed."

### Macro structure for language-specific formatting

For each language, three primary macros are defined:

1. **`contributors-LANG`** (e.g., `contributors-de`, `contributors-fr`)
   - Formats author/editor/translator names as primary contributors
   - Uses language-specific connector (和 "and", German "und", French "et", etc.)
   - Example suffix: `delimiter=" und "` for German

2. **`secondary-contributors-LANG`**
   - Formats editors/translators for non-chapter items
   - Uses language-appropriate labels ("Edited by", "Herausgegeben von", etc.)

3. **`container-contributors-LANG`**
   - Formats editors for book chapters and conference proceedings
   - Uses language-specific connectors between names

### Bibliography layouts

The jss-history.csl file includes separate `<layout>` elements for each language in the `<bibliography>` section:

```xml
<bibliography>
  <layout locale="ja">...</layout>      <!-- Japanese -->
  <layout>...</layout>                 <!-- English (default) -->
  <layout locale="de">...</layout>     <!-- German -->
  <layout locale="fr">...</layout>     <!-- French -->
  <layout locale="it">...</layout>     <!-- Italian -->
  <layout locale="es">...</layout>     <!-- Spanish -->
</bibliography>
```

Each layout routes author/editor processing to the corresponding language-specific macros (e.g., `<text macro="contributors-de"/>` for German).

## jss-history.csl: key macros

`history-bibliography-ja` (around line 1015) controls the Japanese bibliography output. It handles these item types with custom formats:

| Type | Format |
|---|---|
| `article-journal` | 著者、出版年「タイトル」『雑誌名』巻数、ページ数。 |
| `book` | 著者、出版年『タイトル』出版社。 |
| `chapter` | 著者、出版年「タイトル」編者編『書名』出版社、ページ数。 |
| `thesis` | 著者、出版年『タイトル』大学名博士論文。 |
| `article-newspaper` | （著者、）年月日「タイトル」『紙名』。 |
| `manuscript` | （著者、）年「文書名」所蔵機関名、請求番号。 |

All other types fall through to `bibliography-body-全角` (the sociology style default).

For non-Japanese items, language-specific macros (like `contributors-de` for German) handle author name formatting with proper connectors and terminology.

### Structure pattern for Japanese bibliography entries

Separators follow this rule:
- `、` between author and year: use `<group delimiter="、">` wrapping only those two
- No `、` between year and title: the author-year group and the title group are siblings inside an outer `<group>` with no delimiter
- For article-journal, `、` between volume and page: inner `<group delimiter="、">` with `[title+container+volume]` and `page` as two children

### Notes on newspaper and manuscript types

These use bare `<names variable="author">` **without** `<substitute>`, to avoid duplicating the title when no author is present. Other types use the `contributors-全角` macro which has a title substitute.

### Zotero field mappings for manuscript

| Zotero field | Maps to |
|---|---|
| Archive | 所蔵機関名 (`archive` variable) |
| Loc. in Archive | 請求番号 (`archive_location` variable) |

## Zotero vs Juris-M

- **Zotero 7/8** is the current version. Preferences are under `設定` (not `環境設定`).
- **Juris-M** is stuck on version 6 (Zotero 6 base, released 2023) and is effectively unmaintained as of 2026. Uses `環境設定`.
- Right-click menu for bibliography in Zotero 7/8: `出典表記／参考文献目録を作成...`
- The Language field (`言語`) on each Zotero item must be set to `ja`, `de`, `fr`, `it`, `es`, or left blank for English for the correct layout to apply.
