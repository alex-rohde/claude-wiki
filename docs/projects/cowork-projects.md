# Cowork Projects

Cowork has its own Projects feature inside Claude Desktop. A Cowork project is a persistent workspace with its own files, instructions, and memory — useful for recurring or long-running tasks.

!!! note
    Memory is retained within a Cowork project but is **not** carried over to standalone Cowork sessions or to Claude.ai web conversations.

---

## When to use a Cowork Project

- You run the same type of task regularly (e.g., a weekly report or data pull)
- You want Claude to remember context between Cowork sessions for a specific workflow
- You want to organise related Cowork tasks together

---

## How to create a Cowork Project

1. Open **Claude Desktop** and go to the **Cowork** tab
2. Click **New project** and give it a name
3. Add any standing instructions or files the project should always have access to
4. Run all related tasks from inside that project going forward

---

## What to put in a Cowork Project

### Schema context

Generate `CREATE TABLE` scripts from SSMS (right-click table → Script Table as → CREATE To → Clipboard) and paste them in for the tables you work with most.

### Naming conventions

```
Stored procedures: usp_ prefix
Temp tables: #tmp_ prefix
Always include SET NOCOUNT ON
Use TRY/CATCH for error handling
No SELECT * in production code
```

### Environment context

```
Database: [YourDBName]
SQL Server version: [e.g. 2019]
Platform: Infios — procedures are called by Page Editor and Advantage Architect
```

### Standing rules

Any rules that should always apply — performance constraints, patterns to avoid, required comment headers, etc.

### Reference files

You can attach files directly to a Cowork project — useful for:

- A data dictionary or table reference
- A template the output should follow
- A list of item numbers, location IDs, or other lookups Claude will need repeatedly

### Example projects

| Project name | What it does |
|---|---|
| Weekly Inventory Report | Runs a standard inventory summary each Monday |
| Order Exception Review | Checks for orders past promised date and summarizes exceptions |
| Cycle Count Prep | Generates cycle count lists by location zone |

---

## Tips

- Give the project a name that reflects the recurring task, not a generic label
- Keep standing instructions short — one clear rule per line works better than paragraphs
- If a task changes significantly, update the instructions rather than fighting the old ones in the prompt
- Cowork projects remember files and instructions between sessions but **not** conversation history — Claude won't remember what you discussed last time unless it's in the instructions or an attached file
