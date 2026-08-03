# claude-transcript-highlights

A Claude Code plugin that extracts **decisions**, **action items**, and **open questions** from meeting/call transcripts into a markdown summary — each item traceable back to its source timestamp and a verbatim quote.

Handles `.docx`, `.txt`, `.vtt`, and `.srt` transcripts (Zoom, Teams, Otter, Fireflies, Read.ai, etc. — the skill infers whichever speaker/timestamp convention is in use rather than assuming one fixed layout).

## What it does

- **Executive summary** — a few sentences on purpose, themes, and outcome
- **Decisions** — tagged `Confirmed` (explicitly agreed/closed) vs. `Tentative` (floated, discussed, never ratified)
- **Action Items** — owner assigned only when explicitly claimed, otherwise `Unassigned`
- **Open Questions** — raised, never answered
- Long transcripts (45+ min) are automatically chunked and merged so items aren't lost to attention dilution over a long single-pass read

## Example Output

Run against a ~40-minute product sync recording (fictional example, trimmed here to a few representative rows — a real run covers the entire transcript):

```markdown
# Meeting Summary — Weekly Product Sync
Source: product-sync-2026-08-03.vtt | Date: August 3, 2026 | Duration: 41m 12s

## Executive Summary
The team reviewed the sign-up flow redesign ahead of next sprint. They agreed to drop
phone-number verification in favor of email-only, cutting a known drop-off point. A
proposal to add a first-time-user tutorial overlay was discussed at length but not
finalized — the group wants funnel data first. Follow-ups: instrument the sign-up funnel
and draft a concrete tutorial-overlay proposal.

## Decisions
| ID | Status | Summary | Time | Quote |
|----|--------|---------|------|-------|
| D1 | Confirmed | Drop phone-number verification from sign-up; keep email-only verification | 12:04 | "Yeah, let's just cut it, we don't need both." — Priya Patel |
| D2 | Tentative | Add an interactive tutorial overlay for first-time users | 24:37 | "I think we should try an overlay, but let's see the data first." — Alex Chen |

## Action Items
| ID | Owner | Task | Time | Quote |
|----|-------|------|------|-------|
| A1 | Priya Patel | Instrument funnel events to measure where users drop off during sign-up | 31:15 | "I'll get the events added this week so we have real numbers." |
| A2 | Unassigned | Draft a concrete proposal for the tutorial overlay's content and design | 26:02 | "Someone should sketch out what this overlay actually looks like." |

## Open Questions
| ID | Raised By | Question | Time | Quote |
|----|-----------|----------|------|-------|
| Q1 | Alex Chen | Should the tutorial overlay be skippable, and would that skew funnel metrics? | 27:48 | "If people can skip it, does that mess with how we read the funnel?" |
```

Notice `D1` is `Confirmed` (explicit agreement in the transcript) while `D2` is `Tentative` (proposed, discussed, never explicitly ratified) — and `A2` has no owner because no one claimed it. That distinction is the point of the skill: it won't tell you something was decided or assigned unless the transcript actually says so.

## Install

From within Claude Code, first add the marketplace:
```
/plugin marketplace add khanhtran3005/claude-transcript-highlights
```

Then install the plugin:
```
/plugin install claude-transcript-highlights@claude-transcript-highlights
```

## Usage

Just hand Claude a transcript and ask for a summary, e.g.:

> "Summarize this transcript and pull out the action items: ./sprint-review.docx"

The skill will ask where to save the resulting markdown file.

## License

MIT
