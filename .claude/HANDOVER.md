# Handover — site architecture & content recipes

Last updated: 2026-08-16.

## Architecture

- **Two parallel sites.** `classic/` = original light-theme static HTML (+ `classic/style.css`).
  `new/` = dark "space" theme; every page carries its own inline CSS and most content is built by
  in-page JS from data arrays. Root `index.html` redirects to `new/index.html`.
- **Bilingual.** EN page + `*_zh.html` Traditional Chinese twin. ZH convention: Mandarin
  name/label primary, English subtitle. The new-site nav on ZH pages: 研究方向 團隊成員 論文著作
  主要專案 短期專題 實驗室相簿 加入我們 聯絡我們 (join_zh/news_zh use short labels: 研究 團隊 著作
  主要專案 短期專題 相簿 …).
- **Shared assets at repo root** (both sites reference via `../`): `Papers/ Theses/
  QuantumInterfaces/ AtomicClocks/ gallery/ team/ images/ fonts/` (self-hosted woff2: Exo 2,
  Space Mono, Noto Sans TC) + `favicon.svg`/`favicon.ico`.
- **~26 legacy redirect stubs at root** map pre-restructure URLs to classic pages;
  `new/publications_zh.html` is a stub redirecting to `publications-papers_zh.html`.
- Every page has cross-site switch buttons ("Classic site" / "Switch to the new website") mapped
  page-to-page.

## Where content lives (recipes)

### Publication (4 files; in-file comments at each edit point)
- `new/publications-papers.html` + `_zh`:
  - `YB_PAPERS` = the 3D shelf, newest first. The JS **sorts `featured:true` to the front**, so the
    Nature paper stays the gold headline book regardless of insertion order. Only ONE entry may be
    featured.
  - `YB_ALL` = the full list. A `"sec"` field starts a section heading AT that entry ("Recent" /
    "Before starting our Taiwan lab" / "Older papers"). When inserting at the top, MOVE
    `"sec":"Recent"` onto the new first entry. Fields: y/a/t/v/u (+ optional `p` local PDF,
    `x` arXiv, `feat` Nature chip). The renderer auto-bolds lab-member names.
  - EN and ZH arrays are near-identical BUT some URLs differ (journals.aps.org vs doi.org) — never
    anchor a scripted edit on a URL without checking both files.
- `classic/publications-papers.html` + `_zh`: "Selected publications" `<ol>` mirrors the shelf
  (Nature stays #1; new lab papers go directly after it). "All publications" uses
  `<ol reversed start=N>` — bump `start` by 1 on the list you insert into. Bold lab members with
  `<strong><u>` (EN) / `<u><strong>` (ZH).
- Author lists: fetch the `citation_author` meta tags from the DOI landing page (curl works on APS).

### Team member
- New site: `PEOPLE` array in `new/team.html` + `new/team_zh.html` (keep both identical; fields
  name/zh/role/color/photo/aff/affUrl/meta/initials/alumni; photo file goes in `team/`).
  Names use U+2011 non-breaking hyphens — copy exact text when anchoring edits.
- Classic: hand-edit the `pi-card` blocks in `classic/team*.html`.

### Gallery photo
- New site: one line in `PHOTOS_SRC` in `new/gallery.html` + `_zh` (src/cap/tag/yr/hero).
- Classic: `photo-card` divs in `classic/gallery*.html`. Photos live in `gallery/`
  (some older ones in `images/` — do NOT move them, classic pages point there).

### Thesis
- New pubs pages have two sections: `id="theses"` **Our Theses** and `id="other-theses"`
  **Other Useful Theses** (`.tbook` cards; cover JPGs in `Theses/`, rendered from PDF page 1 with
  PyMuPDF `fitz` Matrix(2,2) then PIL-thumbnailed to ~640px). Classic mirrors the same two
  sections in `classic/publications-theses*.html`.
- One thesis PDF is intentionally absent from the repo for now; ~12 places across both sites had
  links to it removed around 2026-08-11 (see git history of that date to restore when the user
  says so). Do not mention the reason anywhere on the site.

### Main projects / Short projects
- `new/projects.html` + `_zh` = **Main Projects** (blueprint "drawing set" style; research
  platforms Pathfinder / Nanofiber / Cavity + tech-development table).
- `new/short-projects.html` + `_zh` = **Short Projects** (student projects; new-site only).
  Static `<article class="pcard">` cards — copy one to add a project. Each card: inline SVG
  cartoon (language-neutral, shared verbatim between EN/ZH), badge (Short=mint, Medium=gold via
  `--gc`), title + other-language subtitle, description, mailto contact pills.

### AI project
- One `{...}` line in the `AI_PROJECTS` array in `new/ai.html` + `_zh` (name/group/desc/by/img/
  link/repo/tag; groups: optical/atomic/electronics/aitools/automation; empty groups auto-render a
  "coming soon" card; images belong in `images/ai/` — folder not created yet).

### News item
- `psec` sections in `new/news.html`/`news_zh.html`; plain sections in `classic/news*.html`.

### Homepage atom (`new/index.html` + `_zh`)
- Physically honest Yb atom: shells 2,8,18,32,8,2 = 70 electrons; 174 nucleons.
- Chip groups: O shell (`MENU=4`, n=8) = the 8 site-menu electrons; 6s² valence pair (`VAL=5`) =
  News + GitHub (`class="e out"`); N shell (`NSH=3`, n=32) hosts the Short Projects chip
  (`class="e nsh"`) with 31 slots free for future chips.
- To add a chip on the N shell: copy the `.nsh` pattern — anchor markup in `#stage`, extend the
  `links` selector exclusion, add a `k<...length` skip in the electron-draw loop, a `placeChips`
  call, and include it in the click-handler list. Never exceed a shell's electron count.
- The top-left fixed cluster holds the Classic site / News / GitHub / AI buttons with the compact
  MissAlign pill below them (hidden on ≤640px).

### Nav changes
- Every new-site page has its own topbar copy — nav changes are a bulk edit across ~28 files.
  Insert after a stable anchor line, first occurrence only (footers repeat the same links).
  Remember the two short-label ZH variants (join_zh, news_zh).

## Content facts worth knowing
- New site + MissAlign built by Hao-Rong Yang 楊皓蓉 (footer credit "New website by Hao-Rong
  Yang ↗ and Claude" on all new-site pages).
- Google Scholar links on pubs pages: Chun-Chia Chen and Shayne Bennetts.
- Contact emails: chunchia.chen@g.iams.sinica.edu.tw, s.p.bennetts@g.iams.sinica.edu.tw.
