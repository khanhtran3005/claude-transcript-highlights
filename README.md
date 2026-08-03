# claude-transcript-highlights

A Claude Code plugin that extracts **decisions**, **action items**, and **open questions** from meeting/call transcripts into a markdown summary — each item traceable back to its source timestamp and a verbatim quote.

Handles `.docx`, `.txt`, `.vtt`, and `.srt` transcripts (Zoom, Teams, Otter, Fireflies, Read.ai, etc. — the skill infers whichever speaker/timestamp convention is in use rather than assuming one fixed layout).

## What it does

- **Executive summary** — a few sentences on purpose, themes, and outcome
- **Decisions** — tagged `Confirmed` (explicitly agreed/closed) vs. `Tentative` (floated, discussed, never ratified)
- **Action Items** — owner assigned only when explicitly claimed, otherwise `Unassigned`
- **Open Questions** — raised, never answered
- Long transcripts (45+ min) are automatically chunked and merged so items aren't lost to attention dilution over a long single-pass read

## Install

From within Claude Code, first add the marketplace:
```
/plugin marketplace add <your-github-username>/claude-transcript-highlights
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
