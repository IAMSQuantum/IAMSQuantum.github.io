# IAMS Yb Lab website — Claude maintainer notes

This repo is the live GitHub Pages site **iamsquantum.github.io** (owner: IAMSQuantum;
`shaynebennetts` has collaborator write access). **Pushing = publishing.** The user usually
commits/pushes themselves — always ask before pushing.

Read the detailed handover before making changes:

- [.claude/HANDOVER.md](.claude/HANDOVER.md) — architecture, where every kind of content lives,
  step-by-step recipes for adding papers / team members / photos / projects / news / atom chips.
- [.claude/WORKFLOW.md](.claude/WORKFLOW.md) — editing conventions (UTF-8 pitfalls), the headless-Edge
  screenshot recipe, and the link audit that must be run after every change.

## The five rules that prevent most mistakes

1. **Two sites, two languages.** `classic/` (old, light) and `new/` (dark space theme) run in
   parallel; almost every page exists as EN + ZH (`_zh`). Most content changes need 2–4 variants.
   ZH pages put the Mandarin name/label first with an English subtitle.
2. **Data-driven pages have "edit here" arrays/comments.** Team, gallery, publications, and AI
   projects are JS arrays inside the page; look for the comment block instead of editing markup.
3. **Verify visually.** The new-site pages are JS-built — one syntax error renders an empty page.
   Screenshot every JS-driven page you touch (recipe in WORKFLOW.md), then run the link audit.
4. **Watch encodings.** Plenty of Traditional Chinese text. Small edits via the Edit tool are safe;
   bulk edits go through Python (`io.open(..., encoding="utf-8")`), never PowerShell string ops.
5. **`Live\website\website\` (outside this repo) is a stale copy — ignore it.** The only expected
   broken link in the audit is `images/cavity.jpg` (referenced by both projects pages).

Session memory (`~/.claude/projects/...IAMSQuantum.../memory/`) holds additional private
maintainer context that should not live in a public repo.
