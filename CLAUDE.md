# CLAUDE.md

## Project overview

This repository contains history-oriented style files for use with Zotero and Juris-M. It is forked from https://github.com/keisato0/jss-csl. The owner is 藤本俊哉.

- **jss-lewis-sato.csl** — Japan Sociological Society (社会学評論) style. Modified from lewis-original.csl by Jonathan Lewis.
- **jss-history.csl** — Juris-M / CSL-M history style. Based on jss-lewis-sato.csl with a custom bibliography macro for Japanese entries and language-specific formatting for multilingual entries.
- **jss-history-zotero.csl** — Zotero-friendly history style. Japanese entries use the custom history bibliography format; all non-Japanese entries use one unified Roman-script fallback.
- **lewis-original.csl** — The original by Jonathan Lewis. Not modified.

## CSL-M basics

`jss-lewis-sato.csl` and `jss-history.csl` use CSL-M (`version="1.1mlz1"`), a superset of standard CSL 1.0.1. `jss-history-zotero.csl` is the Zotero-oriented copy and should be preferred when testing in Zotero.

Key CSL-M features used here:

- **Multiple `<layout>` elements with `locale` attribute** — Juris-M can route each item to the layout matching its Language field. Currently supported in `jss-history.csl`:
  - `locale="ja"` → Japanese (layout uses Japanese formatting)
  - `locale="de"` → German (layout uses German formatting)
  - `locale="fr"` → French (layout uses French formatting)
  - `locale="it"` → Italian (layout uses Italian formatting)
  - `locale="es"` → Spanish (layout uses Spanish formatting)
  - Default (no locale attribute) → English and other languages
  
  **How it works:** In Juris-M, each bibliography item can be matched against the Language field (e.g., `ja`, `de`, `fr`, `it`, `es`). In Zotero, language-specific bibliography layouts are not reliable enough, so `jss-history-zotero.csl` uses only Japanese plus a single non-Japanese fallback.

- **`<alternative>` / `alternative-text`** — For "再録：" (reprinted-in) citations. Juris-M only; silently ignored by Zotero.
- **`alt-*` variables** (`alt-title`, `alt-issued`, etc.) — Juris-M only.
- Macro names use 半角 (half-width, for Roman-script entries) and 全角 (full-width, for Japanese entries) suffixes to distinguish the two layout styles.

## Style behavior

### `jss-history.csl` (Juris-M)

| Language | Locale | Author Connector | Editor Term | Example |
|----------|--------|------------------|-------------|---------|
| English | (default) | and | Ed. / Eds. | Smith and Jones |
| Japanese | ja | ・ (middle dot) | 編者 | 田中・佐藤編 |
| German | de | **und** | **Hrsg.** | Mueller und Schmidt |
| French | fr | **et** | **éd.** | Dupont et Martin |
| Italian | it | **e** | **a cura di** | Rossi e Bianchi |
| Spanish | es | **y** | **ed.** | García y López |

### `jss-history-zotero.csl` (Zotero)

- Japanese items: `Language = ja` → Japanese bibliography path
- Non-Japanese items: any other language code or blank → unified Roman-script fallback
- No per-language `de/fr/it/es` bibliography formatting is attempted in Zotero
- In both history styles, Japanese bibliography entries now end with `。`

### Setting the Language field

Each bibliography item must have its Language field set correctly for the appropriate layout to be applied:

1. In Zotero 7/8 or Juris-M, open an item
2. Find the **Language** field (typically under "Advanced" or scroll down)
3. Set it to:
   - `ja` → Japanese layout
   - `en` or leave blank → non-Japanese fallback in Zotero, English/default in Juris-M
   - `de`, `fr`, `it`, `es` → language-specific layout in Juris-M, non-Japanese fallback in Zotero

Example workflow for creating a mixed-language bibliography:
- Japanese book: Set Language to `ja` → shows "・" delimiter and Japanese formatting per `history-bibliography-ja` macro
- German book in `jss-history.csl`: Set Language to `de` → can show "Mueller und Schmidt" and "Hrsg."
- German book in `jss-history-zotero.csl`: Set Language to `de` → uses the non-Japanese fallback
- English article: Leave Language blank or set to `en`

### Bibliography layouts

`jss-history.csl` includes separate `<layout>` elements for multilingual bibliography handling in Juris-M:

`jss-history-zotero.csl` intentionally simplifies this to:

```xml
<bibliography>
  <layout locale="ja">...</layout>
  <layout>...</layout>
</bibliography>
```

## jss-history.csl: key macros

`history-bibliography-ja` controls the Japanese bibliography output. It handles these item types with custom formats:

| Type | Format |
|---|---|
| `article-journal` | 著者、出版年「タイトル」『雑誌名』巻数、ページ数。 |
| `book` | 著者、出版年『タイトル』出版社。 |
| `chapter` | 著者、出版年「タイトル」編者編『書名』出版社、ページ数。 |
| `thesis` | 著者、出版年『タイトル』大学名博士論文。 |
| `article-newspaper` | （著者、）年月日「タイトル」『紙名』。 |
| `manuscript` | （著者、）年「文書名」所蔵機関名、請求番号。 |

All other types fall through to `bibliography-body-全角` (the sociology style default).

The Japanese bibliography wrapper in `jss-history.csl` and `jss-history-zotero.csl` appends `。` as the final punctuation.

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
- In `jss-history-zotero.csl`, the Language field mainly distinguishes Japanese (`ja`) from non-Japanese fallback.
- In `jss-history.csl`, Juris-M can use language-specific layouts beyond Japanese.
