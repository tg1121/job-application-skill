---
name: job-application-pipeline
description: "Process a job description (JD) into a complete, tracked application: save the JD, assess fit, tailor a LaTeX resume to one ATS-friendly page, write a cover letter, and update an Excel tracker. Use whenever the user pastes a job posting or link, says 'process this JD', 'apply to this', 'tailor my resume for this', or asks about application status, follow-ups, or which applications are ready."
---

# Job Application Pipeline

Turn a pasted JD into an application-ready folder with a tailored resume,
cover letter, and tracker entry. NEVER skip the approval gates.

## Workspace resolution (do this FIRST, every run)

Determine `<WORKSPACE>` before doing anything else, in this order:

1. **Config override.** If `workspace.txt` sits next to this SKILL.md, use
   the single path inside it as `<WORKSPACE>`. This wins over everything.
2. **Persistent local folder.** If the session can reach the user's own
   computer (desktop app, linked folder, mounted drive) and a `JobPipeline/`
   folder exists there, use it. Files persist and the tracker accumulates.
3. **Ephemeral sandbox.** Otherwise read profile/tracker from files the user
   uploaded this session and set `<WORKSPACE>` to an outputs folder the user
   can download from (e.g. `/mnt/user-data/outputs/JobPipeline/`).
   This filesystem RESETS between sessions. At the end of the run, tell the
   user plainly to download the updated `tracker.xlsx` (and any new folder)
   and re-upload it next time.

State the resolved mode in one line at the start. Every path below is
relative to `<WORKSPACE>`.

Some environments do not let you delete or rename files in the user's
folders without asking. Avoid writing scratch files into `<WORKSPACE>`
(see the LaTeX compilation note in Step 4).

## Locations

- Tracker: `<WORKSPACE>/tracker.xlsx` (source of truth)
- Master profile: `<WORKSPACE>/profile/`
  - `*.tex` — the master resume as LaTeX source (e.g. exported from
    Overleaf). If several `.tex` files exist, ask which is the master.
    ONLY finished or solidly completed experience, projects, and skills.
    Tailor COPIES; never edit this file.
  - `profile.md` (optional) — context that does not belong on the resume:
    skills being learned, works in progress, background notes. Items from
    here may appear in materials ONLY framed as "currently learning" /
    "in progress".
  - `settings.md` (optional) — candidate preferences: language levels,
    availability, cover letter style. See `settings.template.md`. If
    missing, use the defaults described below and ask about anything a
    specific JD makes important (e.g. language level for a German JD).
  - If `profile/` has no master resume, STOP and ask the user to add one.
- Per application: `<WORKSPACE>/<Company>/` containing
  - `YYYY-MM-DD_<Role>.md` (the JD)
  - `resume_<Company>_<Role>.tex` and `.pdf` (if a compiler is present)
  - `cover_letter_<Company>_<Role>.docx` (or `.md` if preferred)

Sanitize folder/file names: alphanumerics, `_`, `-` only.

## Candidate profile (built dynamically — never hardcoded)

At the start of every run, read the master resume, `profile.md`, and
`settings.md` if present.
- Master resume → claimable experience, projects, skills.
- `profile.md` → "currently learning" / "in progress" items only.
- `settings.md` → language levels, availability, style preferences.

This combined profile is the CEILING on every claim. Nothing may appear
in a tailored resume, cover letter, or fit assessment that these files
do not support.
- A JD-relevant skill absent from all files is a gap — name it, never add it.
- `profile.md` items may appear in a fit assessment or cover letter as
  honest "currently learning" statements, never in the resume's
  skills/experience sections.
- When the user mentions new experience in chat, suggest adding it to the
  master resume (if finished) or `profile.md` (if in progress) first.

## Honesty rules (hard constraints)

1. **Language levels.** State each language exactly at the level in
   `settings.md` (CEFR). Never inflate. For German, map honestly, e.g.
   B1 → "gute Deutschkenntnisse (B1)"; never "sehr gute" or
   "verhandlungssicher" unless the stated level is C1+. If no level is
   recorded and the JD needs one, ask.
2. **Write at the candidate's level.** A cover letter in a non-native
   language must be writable and defensible by the candidate in an
   interview. Below C1: short simple sentences, everyday vocabulary,
   present tense where possible, no complex subordinate clauses.
   Simple and clear beats impressive.
3. Never invent experience, inflate scope, or keyword-stuff. Accuracy
   beats ATS keywords.
4. If the JD demands something the candidate lacks (language level,
   years of experience, a required degree), say so plainly in the fit
   assessment.
5. Framing must match reality. A toy project, thesis technique, or
   self-study topic is not "a background in X". When in doubt, list it as
   a skill or technique rather than a foundation.

## Workflow (follow in order)

### Step 1 — Ingest
Extract: company, role, location, apply link/email, language
requirements, key requirements. If given a URL, fetch it. If company or
role is unclear, ask.

Save as `<WORKSPACE>/<Company>/YYYY-MM-DD_<Role>.md` with a header block
(company, role, location, apply link, date, source: manual) followed by
the full JD text.

### Step 2 — Track immediately
Open `tracker.xlsx` (create with header row if missing). Columns:
Date added | Source | Company | Role | Location | Status |
Match keywords | Job ID | Apply link | JD file.
Append with Source = "manual", Status = "reviewing",
Job ID = "manual:<company>-<date>". If the same company+role exists,
skip and tell the user.

### Step 3 — Fit assessment
Report briefly: strong matches, partial matches, gaps (especially
language and experience level). Verdict: apply / apply with caveats /
probably not worth the time. Set Status to "matched" (or leave
"reviewing" with a note if the user should decide). Record matched
skills in "Match keywords".

### Step 4 — Tailor resume (approval gate)
Content source = master `.tex` (+ `profile.md` under the honesty rules).
Output = an ATS-friendly resume that fits ONE page.

ATS format requirements:
- Single column. No sidebars, text boxes, or multi-column layouts.
- Plain standard section headings.
- Selectable text only; no photos, logos, icons, graphics; nothing
  essential in headers/footers.
- No tables for bullets or skills (a simple heading+date tabular is fine).
- Standard fonts, 10–11pt, margins ≥ 0.5in, simple bullets.

Section contract:
- MANDATORY, in this order, never dropped: Summary, Education,
  Projects, Skills, Experience. If space is tight, condense content.
  (Users may rename these in `settings.md`; tolerate equivalent master
  names such as Profile → Summary.) If the master has no content for a
  mandatory section (e.g. no work experience yet), ask rather than invent.
- OPTIONAL (Languages, Certifications, Awards, Extra-curricular…):
  include only if JD-relevant and space allows; cut first when trimming.

Propose tailoring as a plain-language CHANGE LIST first (what to
emphasize, reorder, rephrase toward JD vocabulary, which optional
sections to include). WAIT for approval.

After approval:
1. Write `resume_<Company>_<Role>.tex` with the approved content in an
   ATS-compliant layout (reuse the master's preamble if it is ATS-safe
   and compact; otherwise ≈10pt, ~0.5in margins, tight list spacing).
2. Compile in a SCRATCH directory outside `<WORKSPACE>` so `.aux`/`.log`
   clutter never lands there: `latexmk -pdf -interaction=nonstopmode`
   (fall back to `pdflatex` twice). Copy only the final `.tex` and `.pdf`
   back into the company folder.
3. ONE-PAGE HARD CONSTRAINT. If 2+ pages, reduce and recompile in order,
   stopping when it fits: (a) drop least-relevant optional sections;
   (b) cut the least-relevant bullet; (c) tighten verbose bullets.
   Never drop a mandatory section, never go below 10pt or 0.4in margins,
   never pad or fabricate. Report the final page count.
4. If no compiler exists, save the `.tex`, tell the user to compile it
   (e.g. in Overleaf), and ask them to confirm it fits one page.
Set Status to "tailored".

#### PDF "active content" rejections
Some portals (common with German corporate ATS systems) reject PDFs with
interactive elements ("files with active contents"). These come from
`hyperref`: link annotations (`/Annots`), `/OpenAction`, bookmarks
(`/Outlines`), forms (`/AcroForm`).
- `\usepackage[hidelinks]{hyperref}` does NOT fix this; it only hides
  link styling.
- Reliable fix: remove `hyperref` and write all URLs as plain text.
- Verify with `pikepdf`:
  ```python
  import pikepdf
  pdf = pikepdf.open("resume.pdf")
  assert all(p.get("/Annots") is None for p in pdf.pages)
  for key in ("/OpenAction", "/AcroForm", "/Outlines"):
      assert key not in pdf.Root
  ```
- Default deliverable keeps links; produce the links-free version when
  the user reports a rejection or asks for it, and say clearly which
  file is which.

### Step 5 — Cover letter (approval gate)
Language: the JD's language, or the one it requests; ask if unclear.
Non-native languages follow honesty rules 1–2.
Structure: why this company/role (1 short paragraph); what the candidate
brings, anchored to real profile items (1–2 paragraphs); availability
and close. Under one page.

Availability comes from `settings.md`, tuned to the JD's actual terms
(working student vs. internship vs. full-time, part-time hours, on-site
vs. remote). Don't default to generic phrasing.

Default style (override via `settings.md`):
- Plain, direct, everyday language. Short sentences. No corporate or
  "polished AI" phrasing; state points directly.
- Don't restate resume bullets or the tech stack; the letter is
  motivation and voice, referencing achievements lightly.
- Short paragraphs (2–4 sentences).
- `profile.md` items only as in-progress learning.

Show the draft in chat, WAIT for approval, then save. Set Status to
"approved".

### Step 6 — Hand off
Give the folder path and apply link; the application is ready to submit.
When the user says they applied, set Status to "applied" with the date.

## Status lifecycle

reviewing → matched → tailored → approved → applied
(also "rejected", "interview", "offer" — keep the tracker current
whenever the user mentions a status change.)

## Tracker queries

"Which applications are ready?", "what needs follow-up?", "show my
pipeline": answer from `tracker.xlsx`. Follow-up candidates = Status
"applied" and older than 14 days (configurable in `settings.md`).

## Style notes

- Excel: Arial, bold header row, data only, never overwrite rows.
- Be concise in chat; files carry the detail.
- One JD at a time unless the user batches.
