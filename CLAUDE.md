# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page LaTeX CV for Catarina Gamboa. All content lives in `main.tex`; the look is defined by a local, self-contained template (`simplehipstercv.cls` + `simplehipstercv.sty`) — a mashup of the hipstercv, friggeri, and twenty-seconds CV templates. There is no external dependency on a CTAN package for the class itself.

## Build

Compiled with **pdfLaTeX** (TeX Live 2023), driven by latexmk:

```bash
latexmk -pdf main.tex      # build main.pdf (runs pdflatex as many times as needed)
latexmk -pdf -pvc main.tex # continuous preview: rebuild on every save
latexmk -c                 # clean aux files (.aux .log .fls .fdb_latexmk .out .synctex.gz), keep PDF
```

Plain `pdflatex main.tex` also works but may need two passes for the `longtable` layout to settle.

Required TeX packages (must be installed in the TeX distribution): `raleway`, `fontawesome`, `paracol`, `tikz` + `tikz-3dplot`, `smartdiagram`, `titlesec`, `geometry`, `hyperref`. A full TeX Live install has all of these.

## Architecture

Three files, clean separation of content and presentation:

- **`main.tex`** — the *only* file to edit for content. The `\documentclass[lighthipster]{simplehipstercv}` line selects the theme. Each CV section is a `\sectionred{Title}` heading followed by a `longtable` whose rows are entry macros (see below).
- **`simplehipstercv.cls`** — defines themes as class options and all the colors. Selecting a theme = passing one option. Available: `darkhipster`, `lighthipster`, `pastel`, `allblack`, `grey`, `verylight`, `withoutsidebar`. Each option block redefines `cvred`, `headercolour`, `paracolbackgroundoptions`, etc. The class ends by `\usepackage{simplehipstercv}` to pull in the macros.
- **`simplehipstercv.sty`** — defines the entry macros and visual primitives. **Edit here to change how entries render, not what they say.**

### Entry macros (the content vocabulary)

Rows inside the `longtable`s call these. Picking the right one is how you add a CV line:

| Macro | Args | Used for |
|-------|------|----------|
| `\cvevent` | date, title, institution, location, description, **logo image** | Education entries (has a logo column) |
| `\cveventmed` | date, title, institution, description | Experience, talks, committees (no logo) |
| `\cveventshortdesc` | date, title, description | Awards, outreach (no institution line) |
| `\cveventpaper` | date, title, authors, **URL**, venue/description | Publications with a `\faFile` link |
| `\cveventlink` | date, title, authors, **URL**, description | Items with a `\faLink` link |

The Education and Professional-Experience `longtable`s use different column specs (the `p{0.09\textwidth}| p{0.75\textwidth} c` vs `... p{0.85\textwidth}` — the trailing `c` column is the logo). If you switch an entry between `\cvevent` and `\cveventmed`, the table's column count must match (logo column present or not), or compilation breaks.

Other primitives in the `.sty`: `\sectionred` (red section heading), `\simpleheader` (the top banner), `\infobubble` (icon + text chips in the header), `\roundpictwo` (circular profile photo).

### Header layout gotcha

In the `\simpleheader` block at the top of `main.tex`, the repeated `\hspace*{5cm}` before the name, tagline, and info bubbles is intentional — it shifts the text right to clear the circular profile photo placed by `\roundpictwo`. Keep it when editing header text.

## Images

Referenced by bare filename from the repo root (e.g. `gamboa.catarina1.jpeg`, `cmu_pt.png`, `institution_logo.png`). New logos/photos go in the repo root and are passed to the logo argument of `\cvevent` / the photo argument of `\roundpictwo`.

## Conventions

- **Commented-out content is a feature, not dead code.** `main.tex` keeps large `%`-commented blocks (an alternate undergraduate education entry, a full experience timeline, a skills/programming section, sample pirate-themed entries from the template). They are kept for easy re-enabling — don't delete them when editing.
- Build artifacts (`main.pdf`, `main.aux`, `main.log`, `main.fls`, `main.fdb_latexmk`, `main.out`, `main.synctex.gz`) currently sit untracked in the repo root; there is no `.gitignore`. Run `latexmk -c` to clear the auxiliaries before committing if you want a clean tree.
