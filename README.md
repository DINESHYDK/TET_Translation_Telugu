# TET Telugu Translation

Translates T.E.T (Teacher Eligibility Test) English-medium practice question banks
into Telugu, so a Telugu-medium background candidate can understand and prepare,
while the actual English terminology being tested stays visible in brackets.

## How it works
Drop a subject's English PDF into `source-pdfs/`, then ask Claude Code to translate
it. Claude Code follows `.claude/skills/telugu-exam-translator/SKILL.md` to produce
a Telugu LaTeX document + compiled PDF in `output/<subject>/`.

## Folder structure
```
source-pdfs/                                     # original English PDFs, one per subject
output/                                           # generated Telugu translations (tex + pdf)
.claude/skills/telugu-exam-translator/SKILL.md    # the reusable translation skill
```

## Adding a new subject
1. Push the subject's English PDF into `source-pdfs/`
2. Open a Claude Code session on this repo (model: Opus)
3. Ask: "Translate source-pdfs/<filename>.pdf to Telugu using the telugu-exam-translator skill"
4. Review `output/<subject>/final.pdf`

## Translation conventions
- Section/paper headings stay in English
- Questions and passages are translated fully to Telugu
- The word being tested + MCQ options: Telugu meaning with (English word) in brackets
- Question numbers, marks, and answer keys are left unchanged

## Requirements
XeLaTeX + Noto Sans Telugu — installed automatically by the skill at the start of
each session (see SKILL.md).
