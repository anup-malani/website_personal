# Maintaining `anup-malani/website_personal`

This repo is a **PDF host**, not a built site. It stores the PDF assets that `anup-malani/anup-malani.github.io` (the al-folio Jekyll site) links to. No Jekyll, no GitHub Actions, no build step — just files in folders.

> **AI-assisted workflow.** A set of Claude Code skills under `~/UChicago Law Dropbox/Anup Malani/assistants/research-manager/projects/website/skills/` automates the publish flow. If those skills are available, prefer them.

## Repo layout

```
.
├── Malani Resume Sorted YYMM.pdf   # Dated CV releases (one per recompile)
├── publications/*.pdf              # Paper, op-ed, and other PDFs
├── README.md                       # Brief overview
└── MAINTAINING.md                  # This file
```

## What this repo holds

- **Dated CV PDFs at the root** — one per release, named `Malani Resume Sorted YYMM.pdf` where `YYMM` is the year-month of compilation (e.g. `2411`, `2503`, `2504`, `2604`). **Additive only — never delete old versions.** Old dated CVs preserve historical references for citations or grant materials that linked to a specific version.

- **Publication PDFs in `publications/`** — every paper, op-ed, working paper, book chapter PDF that the live site links to. Filename convention: `YYYY_FirstAuthorMalani_Venue_ShortTitle.pdf`.

  Naming examples:
  - `2024_MalaniEtAl_SciReports_CellullarImmunity.pdf` (paper, Anup first, multi-author)
  - `2025_LederLuisMalani_NBER33592_HealthcareFraud.pdf` (working paper, with NBER number)
  - `2008_Malani_HarvLRev_LawAsLocalAmenity.pdf` (sole author)
  - `2022_HoldenMalani_ManagementScience_VelocityICO.pdf` (two authors)

  Components:
  - `YYYY` — 4-digit publication year (or working-paper-issue year)
  - First author's surname, then "Malani" if not already first author. For 3+ authors and Anup first: `MalaniEtAl`. For 3+ authors and someone else first: `FirstMalaniEtAl`.
  - Venue abbrev: `SciReports`, `JPE`, `JLS`, `HarvLRev`, `ManagementScience`, `NBERwXXXXX`, etc.
  - Short title: 2–4 CamelCase words capturing the topic.

## How the website repo links to these PDFs

The Jekyll site (`anup-malani/anup-malani.github.io`) references PDFs in this repo via **GitHub raw URLs**:

```
https://raw.githubusercontent.com/anup-malani/website_personal/main/publications/<filename>.pdf
```

This is set in the `pdf` field of each BibTeX entry in `anup-malani.github.io/_bibliography/papers.bib`. al-folio's jekyll-scholar plugin renders this as a `[PDF]` link on the publications page.

The CV download link uses the **blob viewer URL** (better preview):
```
https://github.com/anup-malani/website_personal/blob/main/Malani%20Resume%20Sorted%20YYMM.pdf
```

This URL lives in `_pages/cv.md` of the website repo. When a new dated CV is pushed here, that file's link must be updated to the new YYMM.

## Adding a publication PDF (manual workflow)

If the AI-assisted `/add-paper` skill is unavailable:

```bash
cp "<source-pdf>" ~/github/website_personal/publications/<canonical-name>.pdf
cd ~/github/website_personal
git add "publications/<canonical-name>.pdf"
git commit -m "Add: <short-title> (<year>)"
git push
```

Then update the website repo's `_bibliography/papers.bib` to point at the new file (see `anup-malani.github.io/MAINTAINING.md`).

## Adding a new dated CV release (manual workflow)

```bash
# 1. Compile main_sorted.tex from the Overleaf folder
cd "/Users/amalani/UChicago Law Dropbox/Anup Malani/Apps/Overleaf/Resume"
latexmk -pdf -interaction=nonstopmode main_sorted.tex

# 2. Mint the dated filename (current YYMM, e.g. 2604 for April 2026)
YYMM=2604

# 3. Copy + push
cp main_sorted.pdf ~/github/website_personal/"Malani Resume Sorted ${YYMM}.pdf"
cd ~/github/website_personal
git add "Malani Resume Sorted ${YYMM}.pdf"
git commit -m "Update CV: ${YYMM} rebuild"
git push

# 4. Update the website repo's CV link
cd ~/github/anup-malani.github.io
# Edit _pages/cv.md: replace the previous YYMM in the GitHub blob URL with ${YYMM}
git add _pages/cv.md
git commit -m "Update CV link: ${YYMM}"
git push
```

## Don'ts

- Don't delete old dated CV PDFs. They're part of the historical record.
- Don't rename existing publication PDFs without also updating every `papers.bib` `pdf` field that points at them. Filenames in `publications/` are referenced from across the site; renaming silently breaks `[PDF]` links.
- Don't put non-PDF assets here. Images go in the website repo (`anup-malani.github.io/assets/img/`).

## Tooling assumptions

- `git` and `gh` CLI configured for the `anup-malani` GitHub account
- TeX Live 2024 with `latexmk` for the CV side (`/Library/TeX/texbin/`)
- The Overleaf-synced LaTeX source lives in Dropbox at `~/UChicago Law Dropbox/Anup Malani/Apps/Overleaf/Resume/`

## See also

- `anup-malani.github.io/MAINTAINING.md` — the website-side maintenance guide (`papers.bib` schema, build flow, custom-domain setup)
