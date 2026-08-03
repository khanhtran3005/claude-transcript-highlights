---
name: extracting-transcript-highlights
description: Use when the user provides a meeting or call transcript (docx, txt, vtt, srt, or pasted text with speaker names and timestamps) and asks to summarize it, or to pull out decisions, action items, or open questions — e.g. "summarize this transcript", "what did we decide on this call", "extract action items from this recording", "what's still unresolved from this meeting".
---

# Extracting Transcript Highlights

## Overview
Pulls decisions, action items, and open questions out of a meeting/call transcript into a markdown summary. Every item traces back to its source timestamp and a verbatim quote. Built for messy real-world transcripts — people float ideas that never get agreed on, walk back earlier statements, talk over each other.

## When to Use
- User shares a transcript file (`.docx`, `.txt`, `.vtt`, `.srt`) or pastes transcript text and asks to summarize it, extract decisions, pull action items, or find what's still unresolved.
- NOT for structured meeting notes someone already wrote by hand — this is for raw speaker-by-speaker transcripts.

## Workflow

1. **Normalize input to plain text.**
   - `.docx` → convert first (the Read tool cannot open binary docx): macOS `textutil -convert txt -stdout <file>`; fall back to `pandoc` or `docx2txt` if unavailable.
   - `.vtt` / `.srt` / `.txt` → read directly.
   - Don't assume one speaker/timestamp layout — Zoom, Teams, Otter, Fireflies, Read.ai all format `speaker + timestamp` differently. Read the converted text and infer the convention in use rather than regex-matching one fixed pattern.

2. **For long transcripts** (either condition alone triggers it: >45 min OR >8k words — don't require both), split into chunks and extract each separately (subagents in parallel where available), then merge and de-duplicate before final output. A long single-pass read dilutes attention and drops items. Cut chunks at topic transitions if they're identifiable from the discussion; otherwise use fixed ~20-minute windows. Never split mid-topic if you can tell where a topic starts/ends.

3. **Extract three categories.** Each item gets a stable ID (D1, D2… / A1… / Q1…) and a timestamp in the transcript's own label format (don't convert to seconds):
   - **Decisions** — tag `Confirmed` only if someone explicitly agreed to or closed it. Tag `Tentative` if it was floated, discussed, or met with enthusiasm but never explicitly agreed — including cases where a speaker later says "we haven't finalized this." Never upgrade tentative to confirmed based on tone alone. A hand-off or next step (e.g. "I'll send this to design," "I'll write a ticket for it") can itself be `Confirmed` as an Action Item even while the underlying design question it relates to stays `Tentative` as a Decision — don't conflate agreeing to figure something out later with agreeing on the answer.
   - **Action Items** — assign an owner ONLY when someone explicitly claims it ("I'll check X"). Otherwise `Unassigned`. Do not infer an owner from who was discussing the topic.
   - **Open Questions** — only count something as open if a direct question was posed AND never given a substantive answer. A trailing non-committal reply ("yeah, maybe," "yeah, yeah") does not count as an answer, but don't manufacture a question where no one actually asked one — a topic that was merely discussed and dropped is not an open question.
   - Consolidate recurring topics into one item at their first substantive mention. If status changes later (e.g., tentative → confirmed), update that same row's status in place and note the earlier float in its summary/quote — don't create a second row for the same topic.
   - The source transcript may be machine-transcribed and garbled in places. Quote it as-is; only add a bracketed `[likely word]` where the intended word is clearly inferable and the garbled text would otherwise be confusing. Never invent or smooth over meaning that isn't actually inferable.

4. **Write the output** using the template below. Ask the user for the save path each run — don't assume a destination.

## Output Template

```markdown
# Meeting Summary — <title>
Source: <file> | Date: <if present> | Duration: <if present>

## Executive Summary
<3-5 sentences: purpose, key themes, overall outcome. This is synthesis, not a sourced claim — keep it brief and don't cite it as fact in later discussion.>

## Decisions
| ID | Status | Summary | Time | Quote |
|----|--------|---------|------|-------|
| D1 | Confirmed / Tentative | ... | mm:ss | "..." — Speaker |

## Action Items
| ID | Owner | Task | Time | Quote |
|----|-------|------|------|-------|
| A1 | Name / Unassigned | ... | mm:ss | "..." |

## Open Questions
| ID | Raised By | Question | Time | Quote |
|----|-----------|----------|------|-------|
| Q1 | Name | ... | mm:ss | "..." |
```

Column order is deliberate: substance first (status/owner/summary), timestamp and quote last — those exist only to trace back to source, not for scanning.

## Common Mistakes

| Mistake | Fix |
|---|---|
| Marking a floated idea "Confirmed" because everyone sounded positive | Require an explicit agreement/close statement |
| Guessing an action item owner from context | Leave "Unassigned" unless explicitly claimed |
| One row per every mention of a topic | Consolidate; new row only on status change |
| Treating the executive summary as a sourced claim | Label it synthesis; only table rows carry quotes/timestamps |
