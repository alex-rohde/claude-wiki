# Jira Reports with Claude Cowork

Claude Cowork is well-suited for turning Jira exports into formatted, readable reports. This page covers how to export your tickets out of Jira and use Cowork to generate status reports, sprint summaries, and project overviews.

---

## The workflow

Cowork can't log into Jira directly, but Jira has solid CSV and Excel export options. The workflow is:

1. Export your Jira tickets to CSV or Excel
2. Hand the export to Claude Cowork with instructions on the report you want
3. Cowork generates a formatted report and saves it to your desktop or project folder

---

## Exporting tickets from Jira

### From a board or backlog

1. Go to your Jira project board or backlog.
2. Apply any filters you want (sprint, assignee, label, status).
3. Click the **Export** button (top-right area, may be under the `...` menu).
4. Choose **Export Excel CSV (current fields)** or **Export Excel CSV (all fields)**.
5. Save the file to a known location (e.g., `~/Downloads/jira-export.csv`).

### From a saved filter

1. Go to **Issues** > **Search for Issues**.
2. Build your JQL query, e.g.:
   ```
   project = "ORDER-MGMT" AND sprint in openSprints() ORDER BY priority DESC
   ```
3. Click **Export** > **Export Excel CSV**.

---

## Sprint status report

This prompt generates a clean status report from a sprint export, grouped by status.

```
I have a Jira CSV export at ~/Downloads/sprint-export.csv from our current sprint.

Generate a sprint status report and save it to ~/Desktop/sprint-report.md

Structure the report like this:

# Sprint Status Report — [today's date]

## Summary
- Total tickets: [count]
- Done: [count]
- In Progress: [count]
- To Do / Backlog: [count]
- Blocked: [count] (any ticket with a "Blocked" label or status)

## In Progress
[Table: Ticket | Summary | Assignee | Priority]

## Done This Sprint
[Table: Ticket | Summary | Assignee]

## Not Started
[Table: Ticket | Summary | Assignee | Priority]

## Blocked
[Table: Ticket | Summary | Assignee | Reason if in the notes field]

Sort each section by priority (Highest first).
```

---

## Weekly project status report

Useful for a stakeholder update or a Friday wrap-up email.

```
Read the Jira export at ~/Downloads/project-tickets.csv

Generate a weekly status report for the Order Management project.
Save it to ~/Desktop/weekly-status-2026-05-26.md

Format:

# Order Management — Weekly Status (week of May 26, 2026)

## What got done this week
[Bullet list of tickets moved to Done in the past 7 days, with ticket number and summary]

## What's in progress
[Bullet list of open tickets actively being worked, with assignee]

## Coming up next week
[Bullet list of tickets in To Do or Backlog that are high priority]

## Blockers and risks
[Any tickets labeled Blocked, or that have been In Progress for more than 10 days without movement]

Keep language plain, non-technical. This report goes to a project manager.
```

---

## Organizing tickets by project across multiple boards

If you have exports from multiple Jira projects and want a consolidated view:

```
I have three Jira exports in ~/Downloads/jira/:
- order-mgmt.csv
- warehouse.csv
- portal.csv

Create a consolidated report at ~/Desktop/all-projects-status.md

For each project, include:
- A one-line project description (infer it from the ticket summaries)
- Count of tickets by status (Done, In Progress, To Do, Blocked)
- Top 5 highest priority open tickets (ticket number, summary, assignee)
- Any tickets flagged as blocked

Then at the top, add an executive summary: 2-3 sentences covering overall health
across all three projects.
```

---

## Ticket aging report

Useful for surfacing tickets that have been sitting idle.

```
Read ~/Downloads/jira-export.csv

Generate a ticket aging report. Save to ~/Desktop/aging-report.md

Rules:
- "Stale" = In Progress for more than 14 days without status change
- "Old backlog" = In To Do / Backlog for more than 30 days
- "Long running" = Created more than 60 days ago and not Done

For each category, list: Ticket | Summary | Assignee | Days in current status

Sort each section longest-idle first. Include a one-line summary at the top
with total counts for each category.
```

---

## Generating a report on a recurring schedule

If you want this report to run automatically every Friday, use Cowork's scheduled task feature (Pro/Max accounts):

1. Open Claude Desktop and go to **Cowork**.
2. Write your prompt the same way as above.
3. Click **Schedule** and set it to run weekly on Friday at 4 PM.
4. Cowork will run the task and save the output file to your desktop each week.

You'll still need to refresh the Jira CSV export before the scheduled run, or see the next section for automating that step.

---

## Automating the Jira export with a URL

Jira supports direct CSV export via a URL if you're logged in. You can give Cowork a Jira export URL and it will fetch the latest data on each run.

To get your export URL:

1. Run the search or filter you want in Jira.
2. Click **Export** > **Export Excel CSV**.
3. Before it downloads, copy the URL from the browser address bar or network panel.

Then in your Cowork prompt:

```
Fetch the latest Jira export from this URL and save it to ~/Downloads/jira-export.csv:
[paste your Jira export URL]

Then generate the sprint status report as described below...
```

---

## Tips

**Include the field list in your project context.** Jira CSV exports include different columns depending on how your project is configured. If Claude misreads a column, add a note like: "The status column is called 'Status', the assignee is 'Assignee', tickets are identified by the 'Issue Key' column."

**Use JQL to pre-filter before exporting.** The more targeted your export, the cleaner the report. Filter by sprint, label, component, or assignee before exporting rather than asking Cowork to filter everything.

**Ask for both a file and a summary.** At the end of any Cowork prompt, add: "Also print a one-paragraph summary of the report to the screen so I can see it without opening the file."
