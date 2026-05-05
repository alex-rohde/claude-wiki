# Training Sessions

!!! note "Draft"
    This page is a work in progress.

Four in-person sessions for developers and SQL folks. The wiki is the reference material — sessions are for live demos and hands-on practice.

---

## Session 1 — What is Claude and Getting Started

**Format:** 45-60 min | **Tool:** Claude.ai web

### Agenda

1. What Claude is — an AI coding assistant, not a search engine
2. What it's good at vs. where it falls short
3. Data privacy — what goes to Anthropic, what doesn't
4. Live demo: paste a query, ask Claude to explain it, then improve it
5. Live demo: describe a problem in plain English, let Claude write the first draft

### Talking Points

- It doesn't know your database — you have to give it context
- Treat it like a smart colleague who needs to be briefed, not a magic button
- The more specific your prompt, the better the output
- You can paste errors directly and ask what's wrong

---

## Session 2 — Cowork (Claude Desktop)

**Format:** 45-60 min | **Tool:** Claude Desktop — Cowork

### Agenda

1. What Cowork is and how it differs from the web
2. Setting up a Cowork project — schema context, naming conventions, standing rules
3. Attaching reference files (data dictionary, templates, lookup lists)
4. Live demo: set up a project for a recurring task (e.g. weekly report)
5. Live demo: run a task inside the project, show context persistence

### Talking Points

- Memory lives in the project, not the conversation — Claude won't remember what you said last time unless it's in the instructions
- Keep standing instructions short — one rule per line
- Best for recurring tasks where you want Claude to already know your environment
- Use the "Customer Name - DEV - AAD" naming format for projects

---

## Session 3 — Claude Code (CLI)

**Format:** 45-60 min | **Tool:** Claude Code (terminal)

### Agenda

1. What Claude Code is — Claude inside your terminal, inside your project
2. Installation walkthrough (wiki reference: [Claude Code (CLI)](getting-started/claude-code.md))
3. What a CLAUDE.md is and why it matters
4. Live demo: start a session from a project folder, ask Claude to read a file and explain it
5. Live demo: make a real edit — fix a bug, rename something, add a section

### Talking Points

- Claude Code reads your actual files — no copy/pasting
- CLAUDE.md is your briefing document — schema, conventions, environment all go there
- Global CLAUDE.md covers standards that apply everywhere (sproc template, naming rules)
- Project-level CLAUDE.md covers the specific account/database
- It will ask for confirmation before anything destructive

---

## Session 4 — SQL and Stored Procedures

**Format:** 45-60 min | **Tool:** Claude Code or Claude.ai web

### Agenda

1. Giving Claude the right context for SQL work (schema, platform, conventions)
2. Walkthrough of the sproc template — what each section does and why
3. Live demo: prompt Claude to build a sproc from a plain English description
4. Live demo: paste an existing sproc, ask Claude to add error handling or a new param
5. Prompting tips specific to T-SQL

### Talking Points

- Always tell Claude the DB name, platform (WebWise/Advantage Architect), and relevant tables
- Paste `CREATE TABLE` scripts for the tables involved — Claude needs to know the columns
- Use `@in_debug = 'YES'` pattern for development and production troubleshooting
- `@error_tag` pattern — set a unique tag before each statement so failures are traceable in `t_error_log`
- Claude won't know about `t_log_control`, `t_error_log`, etc. unless you tell it — that's what the CLAUDE.md is for
