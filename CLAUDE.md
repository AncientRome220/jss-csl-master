# CLAUDE.md

## Project overview

This repository contains CSL-M (Citation Style Language Multilingual) style files for use with Zotero and Juris-M. It is forked from https://github.com/keisato0/jss-csl. The owner is 藤本俊哉.

- **jss-lewis-sato.csl** — Japan Sociological Society (社会学評論) style. Modified from lewis-original.csl by Jonathan Lewis.
- **jss-history.csl** — Japanese history journal style. Based on jss-lewis-sato.csl with a custom bibliography macro for Japanese entries.
- **lewis-original.csl** — The original by Jonathan Lewis. Not modified.

## CSL-M basics

These files use CSL-M (`version="1.1mlz1"`), a superset of standard CSL 1.0.1. Key CSL-M features used here:

- **Multiple `<layout>` elements** with `locale="ja"` and a default — Zotero routes each item to the layout matching its Language field (`ja` → Japanese layout, otherwise → default layout). This works in both Zotero and Juris-M.
- **`<alternative>` / `alternative-text`** — For "再録：" (reprinted-in) citations. Juris-M only; silently ignored by Zotero.
- **`alt-*` variables** (`alt-title`, `alt-issued`, etc.) — Juris-M only.
- Macro names use 半角 (half-width, for English entries) and 全角 (full-width, for Japanese entries) suffixes to distinguish the two layout styles.

## jss-history.csl: key macro

`history-bibliography-ja` (around line 746) controls the Japanese bibliography output. It handles these item types with custom formats:

| Type | Format |
|---|---|
| `article-journal` | 著者、出版年「タイトル」『雑誌名』巻数、ページ数。 |
| `book` | 著者、出版年『タイトル』出版社。 |
| `chapter` | 著者、出版年「タイトル」編者編『書名』出版社、ページ数。 |
| `thesis` | 著者、出版年『タイトル』大学名博士論文。 |
| `article-newspaper` | （著者、）年月日「タイトル」『紙名』。 |
| `manuscript` | （著者、）年「文書名」所蔵機関名、請求番号。 |

All other types fall through to `bibliography-body-全角` (the sociology style default).

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
- The Language field (`言語`) on each Zotero item must be set to `ja` or `en` for the correct layout to apply.
