# Job Application Pipeline (Claude Skill)

An honest, LaTeX-based job application pipeline for Claude, built by an
international master's student applying for working student and intern
roles in Germany. It is opinionated about three things most resume tools
skip: never inventing or keyword-stuffing experience, stating language
levels exactly as they are, and producing one-page PDFs that German ATS
portals actually accept.

Paste a job description and get a tracked, application-ready folder:
saved JD, honest fit assessment, a tailored one-page ATS-friendly LaTeX
resume, a cover letter, and an updated Excel tracker. Every resume and
cover letter goes through an approval step before it is saved.

## Install

**Claude (web, desktop, mobile):** download `job-application-pipeline.zip`
from the [latest release](../../releases/latest), then go to
**Customize → Skills → + → Create skill → Upload a skill**.

**Claude Code:** copy the `job-application-pipeline/` folder into
`~/.claude/skills/`.

## Setup

1. Install the skill (above).
2. Create a workspace folder, e.g. `JobPipeline/`, with:
   ```
   JobPipeline/
     profile/
       my_resume.tex      # master resume (LaTeX, e.g. from Overleaf)
       profile.md         # optional: things you're learning / in progress
       settings.md        # optional: copy of settings.template.md (in this skill folder)
   ```
3. Optional: put a `workspace.txt` next to `SKILL.md` containing the
   absolute path to your workspace.

Without a persistent folder (plain chat sessions), upload your profile
files and `tracker.xlsx` each session and download the updated tracker
afterwards.

## Design principles

- **No fabrication.** Your master resume and notes are the ceiling on
  every claim. Missing skills are reported as gaps, not added.
- **Honest language levels.** Stated exactly as in your settings; cover
  letters in non-native languages are written at a level you can defend
  in an interview.
- **One page, ATS-safe.** Single column, standard headings, compiled and
  page-checked automatically when LaTeX is available.
- **Portal-safe PDFs.** Includes a fix and a check for portals that
  reject PDFs with "active content".

## Requirements

- LaTeX (`latexmk` or `pdflatex`) for automatic compilation (optional)
- Python with `openpyxl`, and `pikepdf` for the PDF check (optional)

## Usage

Paste a job posting (or a link) and say "process this JD". Later, ask
"which applications are ready?" or "what needs follow-up?".

## License

Copyright (C) 2026 Tushti Goyal

Licensed under the [GNU General Public License v3.0](LICENSE). You may use,
modify and share this skill, including commercially, but any modified
version you distribute must also be released under GPL-3.0 with its
source available, and must keep this copyright notice.
