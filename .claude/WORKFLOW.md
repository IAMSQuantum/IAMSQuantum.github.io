# Workflow — editing, verifying, and shipping changes

Hard-won conventions from bulk-editing ~85 bilingual HTML files on Windows. Violating them
corrupts Chinese text or fails silently.

## Editing & encodings
- Small edits (even with Chinese text) via Claude's Edit/Write tools are safe (UTF-8).
- **Bulk edits: use Python** — `io.open(path, encoding="utf-8")`, write with `newline=""` to
  preserve line endings. Run from a script file, not a long shell heredoc.
- Never put non-ASCII literals in `.ps1` files (PowerShell 5.1 reads BOM-less .ps1 as ANSI →
  mojibake). `[IO.File]` uses the process CWD — always pass absolute paths.
- **Guard every scripted replacement**: assert the anchor exists and is unique before replacing,
  so partial runs are loud instead of silent.
- Team `PEOPLE` names contain U+2011 non-breaking hyphens — anchor on ASCII substrings or copy
  the exact text from a fresh read.
- EN/ZH twins are near-identical but not byte-identical (labels, some URLs differ). Check both
  before assuming an anchor exists in each.

## Visual verification (required for JS-driven pages)
Pages built by in-page JS (`new/team*`, `gallery*`, `publications-papers*`, `ai*`, `index*`)
render EMPTY on a single script error — always screenshot after editing them.

1. `python -m http.server 8123` in the repo root.
2. Headless Edge (from Git Bash), fresh `--user-data-dir` every attempt:
   ```
   "/c/Program Files (x86)/Microsoft/Edge/Application/msedge.exe" --headless=new --disable-gpu \
     --no-first-run --user-data-dir=<FRESH-TMP-DIR> --window-size=1400,H \
     --virtual-time-budget=14000 --screenshot=<out.png> <url>
   ```
   - Screenshots randomly fail silently (no file) — retry in a loop with a NEW user-data-dir.
   - Heights above ~3600px always fail.
   - `#fragment` URLs get caught mid smooth-scroll; lazy images render blank.
   - For content deep in a long page: copy the section into a temporary test HTML inside the repo
     (so `../` assets resolve), screenshot that, delete it.
3. Stop the server:
   `Get-NetTCPConnection -LocalPort 8123 -State Listen | % { Stop-Process -Id $_.OwningProcess -Force }`

## Link audit (run after every change set)
Python scan of all `.html` for relative `href`/`src`, resolved against each file's directory,
`os.path.exists` check. **Pitfall:** when skipping `.git`, prune the path SEGMENT
(`dirs[:] = [d for d in dirs if d != ".git"]`) — a substring test silently skips everything
because the repo folder name contains `.github.io`. Sanity-check the totals (~85 files,
~1900 links). Only expected failure: `../images/cavity.jpg` (4 hits, both projects pages).

## Useful tooling already installed
- PyMuPDF (`import fitz`) — thesis/paper cover thumbnails:
  `doc[0].get_pixmap(matrix=fitz.Matrix(2,2)).save(out, jpg_quality=88)`, then PIL thumbnail to
  ~640px width.
- PIL — favicon generation, resizing, contact sheets.
- `curl` works on APS/DOI pages — pull `citation_author`/`citation_title` meta tags for new papers.

## Git & publishing
- Remote is pinned to `https://shaynebennetts@github.com/IAMSQuantum/IAMSQuantum.github.io.git`
  so the right cached credential is used (the machine also holds an IAMSQuantum login).
- **Ask before pushing** — a push publishes the live site. The user often prefers to push
  themselves after reviewing.
