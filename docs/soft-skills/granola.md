# Granola Meeting Transcription

Granola is a meeting transcription app that runs on your Mac and automatically captures audio from your meetings — Zoom, Teams, Google Meet, and in-person calls. It produces a clean, searchable transcript and AI-generated notes after each meeting.

This page covers how to get the most out of Granola and how to use Claude to extend what Granola gives you.

---

## What Granola gives you

After a meeting ends, Granola produces:

- A full transcript with speaker labels
- A structured summary (agenda items, decisions, action items)
- Searchable notes you can access later

These outputs are the raw material for the workflows on this page and the [Organizing Meeting Notes](meeting-notes.md) page.

---

## Setting up Granola

1. Download Granola from [granola.so](https://granola.so) and install it on your Mac.
2. Grant microphone and screen recording permissions when prompted.
3. Granola runs in the background. When a meeting starts, it detects it and begins capturing automatically.
4. After the meeting, Granola generates notes. You can review and edit them in the app.

Granola captures system audio (what comes through your speakers or headphones), so it works regardless of which meeting platform you use.

---

## Using Claude with Granola output

Granola's built-in summary is useful, but Claude can go further — especially when you need a specific format, want to extract particular information, or need to tie the meeting back to a project or ticket.

### Get a focused summary

Granola summarizes everything. If you only care about a specific part of the meeting, ask Claude to extract it.

```
Here are the notes from today's sprint planning meeting:

[paste Granola notes]

Summarize only the decisions made about the authentication module. Ignore everything else.
```

---

### Extract action items with owners

Granola identifies action items, but Claude can format them the way your team actually uses them.

```
Here are the notes from today's meeting:

[paste Granola notes]

Extract all action items. For each one, list:
- The task
- Who owns it (if mentioned)
- Any deadline mentioned
- Which Jira ticket it relates to (if mentioned)

Format as a table.
```

---

### Rewrite notes for a specific audience

If the meeting involved both technical and non-technical people, Claude can rewrite the summary for a specific reader.

```
Here are the raw notes from a technical design meeting:

[paste Granola notes]

Rewrite this as a brief update for a non-technical project manager. Focus on decisions made, timeline impacts, and anything they need to approve or be aware of. Skip implementation details.
```

---

### Identify open questions and blockers

```
Review these meeting notes and identify:
1. Any questions that were raised but not answered
2. Any blockers or dependencies called out
3. Any items that were tabled for a follow-up meeting

[paste Granola notes]
```

---

### Generate a follow-up email

```
Based on these meeting notes, write a follow-up email to the attendees.

Include:
- A two-sentence recap of what was covered
- A bullet list of decisions made
- A bullet list of action items with owners
- Next steps / next meeting if mentioned

Keep it concise. Tone: professional but casual.

[paste Granola notes]
```

---

## Tips

**Edit Granola notes before pasting into Claude.** Granola's transcript is accurate, but if a speaker mumbled or got misidentified, a quick cleanup pass before sending to Claude will improve the output.

**Use a Claude Project for recurring meetings.** If you have a weekly standup or sprint review, set up a Claude Project with context about the team, the Jira board, and the project. Dropping meeting notes into a conversation inside that project means Claude already knows who the players are and what the work is about.

**Paste the full notes, not just the summary.** Granola's auto-summary can miss nuance. When you need Claude to reason carefully about what was said, give it the full transcript or notes, not just the highlights.
