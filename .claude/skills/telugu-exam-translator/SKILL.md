---
name: telugu-exam-translator
description: Translates English-medium competitive exam PDFs (TET practice question banks and similar) into Telugu, producing a compiled LaTeX PDF. English terminology, technical/grammar terms, and MCQ option words are preserved in brackets. Use whenever asked to translate a PDF in source-pdfs/ into Telugu for exam prep.
---

# Telugu Exam Translator

## When to use this skill
Trigger whenever asked to translate an English-medium exam/question-bank PDF
(TET or similar competitive exam practice material) into Telugu.

## Step 1 — Environment setup (once per session)
```bash
apt-get update -qq
apt-get install -y --no-install-recommends fonts-noto-core poppler-utils texlive-xetex
python3 -m pip install -q --ignore-installed pdfplumber
fc-list | grep -i "noto sans telugu"   # confirm it installed
which xelatex                           # confirm it installed
```
`--ignore-installed` stops pip reusing Ubuntu's preinstalled `cffi`/`cryptography`, which are
built for a different Python version and make `import pdfplumber` crash.

## Step 2 — LaTeX template
Use XeLaTeX, never pdflatex. Noto Sans Telugu has no Latin glyphs, so ALL English
text (headings, bracketed terms, quoted words) must be wrapped in `\en{...}`.

```latex
\documentclass[12pt,a4paper]{article}
\usepackage{fontspec}
\usepackage[margin=1in]{geometry}
\setmainfont{Noto Sans Telugu}
\newfontfamily\englishfont{Noto Sans}[Ligatures=TeX]   % ``word'' -> curly quotes
\newcommand{\en}[1]{{\englishfont #1}}
\begin{document}
% content
\end{document}
```

## Step 3 — Translation rules (default; confirm with the user if a new subject's
content type differs materially, e.g. math or science papers)
1. Section/paper headings → stay in English, wrapped in `\en{}`
2. Question instruction line (e.g. "Choose the synonym of the word...") → translate to Telugu
3. The target word/phrase being tested (quoted in the instruction) → keep in English via `\en{}`
4. Main question sentence/passage → translate fully to Telugu
5. MCQ options that are English vocabulary → Telugu meaning + `\en{(English word)}`
6. Options that are themselves the grammar form being tested (verb tenses etc.) →
   keep in English via `\en{}`
7. Question numbers, marks, answer-key numbers → unchanged
8. Answer key table → render as a LaTeX table, values unchanged, no translation needed

## Step 4 — Chunked processing (avoid hallucination on long documents)
1. Extract full text first with `pdfplumber`, save as `full_text.txt` for reference
2. Process source pages in chunks of ~10-12 pages at a time
3. Write each chunk as its own `.tex` file and save to disk BEFORE starting the next chunk
4. After each chunk, spot-check the translated content against `full_text.txt` for
   that page range before continuing
5. Combine all chunks into `final.tex` with `\input{}`, compile with `xelatex`,
   confirm no "Missing character" errors in the log

## Step 5 — Output
Save to `output/<subject-slug>/`:
- `final.tex`
- `final.pdf`
- `chunks/*.tex` (intermediate files, kept for review)

Report back: total pages processed, question count, and any page needing manual review.
