# Organizing Meeting Notes by Project

Meeting notes accumulate fast. This page covers how to use Claude to take Granola transcripts and turn them into organized, searchable records tied to your projects and Jira tickets.

---

## The basic workflow

1. Finish a meeting. Granola produces notes automatically.
2. Copy the notes out of Granola.
3. Paste into Claude (web or Claude Code) with a prompt telling it where this meeting fits.
4. Save the formatted output to your project folder or paste it into Confluence, Jira, Notion, etc.

The key is giving Claude enough context about the project so it can label and organize the notes correctly.

---

## Organizing a single meeting into a project folder

If you keep project notes as markdown files on your local machine, Claude Code can write them directly.

Open Claude Code in your notes folder and paste in the Granola output:

```
I just finished a meeting about the order management redesign project.
Save these notes as a new file in ./projects/order-management/meetings/

Use this filename format: YYYY-MM-DD_<short-topic>.md
Today's date is 2026-05-26. The topic was sprint planning.

Format the file like this:
- Date and attendees at the top
- Summary (3-5 sentences)
- Decisions made (bullet list)
- Action items (bullet list with owner and due date if mentioned)
- Open questions (bullet list)
- Raw notes at the bottom under a collapsible section

Here are the Granola notes:
[paste notes]
```

---

## Processing a batch of meeting notes at once

If you have a backlog of meetings that haven't been organized, use Claude Cowork to process them all in one pass.

See [Jira Reports with Cowork](jira-reports.md) for the Cowork setup, but the same approach works for meeting notes:

```
In ~/Documents/Granola Exports/, there are markdown files containing meeting notes
from the past two weeks.

For each file:
1. Read the notes and determine which project it belongs to based on the content.
   Projects are: Order Management, Warehouse Automation, Portal Redesign, Infrastructure.
2. Create a subdirectory under ~/Documents/Project Notes/<project-name>/meetings/
   if it doesn't already exist.
3. Reformat the notes using this structure:
   - Date (from the file or content)
   - Attendees (if mentioned)
   - Summary (3-5 sentences)
   - Decisions
   - Action items (with owner if named)
   - Open questions
4. Save the reformatted file to the appropriate project subfolder.
   Use the filename format: YYYY-MM-DD_<short-topic>.md

After processing all files, create a summary file at
~/Documents/Project Notes/meeting-log.md listing each meeting by date,
project, and a one-line description.
```

---

## Tagging notes with Jira tickets

When a meeting references Jira tickets, Claude can extract those references and make them easy to trace.

```
Here are meeting notes from a backlog grooming session:

[paste Granola notes]

Do the following:
1. Extract every Jira ticket number mentioned (format: ABC-1234)
2. For each ticket, list what was discussed or decided about it
3. Note any tickets that had their status, priority, or acceptance criteria changed
4. List any new tickets that were created as a result of this meeting (if mentioned)

Format the output as a table with columns: Ticket | Discussion | Status Change
```

---

## Building a running project log

If you want a single document that accumulates meeting notes for a project over time, use this prompt after each meeting:

```
I have an existing project log at ~/Documents/Projects/order-management/project-log.md

Here are notes from today's meeting (2026-05-26, sprint review):

[paste Granola notes]

Append a new entry to the project log with this structure:

## 2026-05-26 — Sprint Review

**Attendees:** [names if present]
**Summary:** [3-5 sentences]
**Decisions:** [bullet list]
**Action Items:** [bullet list with owners]
**Open Questions:** [bullet list]

Do not modify any existing content in the file. Only append.
```

---

## Setting up a Claude Project for your team

If you attend the same recurring meetings (sprint planning, standups, design reviews), a Claude Project saves a lot of setup time.

Add the following to the project context:

```
Team: [your team name]
Active projects: Order Management, Warehouse Automation, Portal Redesign
Jira board: [board URL or name]
Team members: [list first names or initials so Claude can identify speakers]

Notes are sourced from Granola transcripts. When I paste notes, assume they
came from a meeting on the current project unless I say otherwise.
```

With this context in place, you can paste raw Granola output into any conversation in that project and Claude already knows who the people are and what the work is about.

---

## Tips

**Include the date in your prompt.** Granola embeds the date in the file, but if you're copying just the notes body, tell Claude the date explicitly so filenames and headers come out right.

**Be explicit about what matters.** If your team cares about decisions more than discussion, say so: "Focus on capturing decisions and action items. Summarize the discussion in one sentence."

**Ask Claude to flag things that need follow-up.** At the end of any organizing prompt, add: "Also flag any items that seem time-sensitive or that reference a deadline in the next 7 days."
