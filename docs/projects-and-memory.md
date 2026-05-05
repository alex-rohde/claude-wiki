# Projects and Memory

Each Claude tool has its own way of maintaining context across sessions. They are separate — context does not carry over between the web, Claude Code, and Cowork.

---

## Overview

| Tool | Feature | Set up by |
|---|---|---|
| **Claude.ai Web** | Projects | You |
| **Claude Code** | `CLAUDE.md` files + Auto memory | You + Claude |
| **Cowork** | Cowork Projects | You |

---

## Claude.ai Web — Projects

Projects let you give Claude persistent context that applies to every conversation inside that project. Instead of re-explaining your database schema, naming conventions, or coding standards every session, you write it once in the project and Claude knows it from the start.

### When to use a Project

- You work in the same codebase or database regularly
- You want Claude to follow consistent conventions without reminding it each time
- You want to organize work by database, application, or team

### How to create a Project

1. Go to [claude.ai](https://claude.ai) and click **Projects** in the left sidebar
2. Click **New project**
3. Give it a name — e.g., `Infios Dev — [Database Name]`
4. Click **Project knowledge** and add your context (see below)
5. Start all related work from inside this project

### What to put in a Project for T-SQL / Infios work

The goal is to capture everything you'd otherwise have to re-explain. Good candidates:

**Schema context**
Generate `CREATE TABLE` scripts from SSMS (right-click table → Script Table as → CREATE To → Clipboard) and paste them in for the tables you work with most.

**Naming conventions**
```
Stored procedures: usp_ prefix
Temp tables: #tmp_ prefix
Always include SET NOCOUNT ON
Use TRY/CATCH for error handling
No SELECT * in production code
```

**Environment context**
```
Database: [YourDBName]
SQL Server version: [e.g. 2019]
Platform: Infios — procedures are called by Page Editor and Advantage Architect
```

**Standing rules**
Any rules that should always apply — performance constraints, patterns to avoid, required comment headers, etc.

### Tips

- Keep the project knowledge focused and concise — long walls of text are less effective than clear, specific rules
- Update it when conventions change or you add new tables
- Create separate projects for different databases or applications rather than one giant project for everything

---

## Claude Code — CLAUDE.md and Auto Memory

Claude Code does not use Claude.ai web Projects. It has two of its own memory systems that work together.

### CLAUDE.md

A markdown file you create in your project directory. Claude reads it at the start of every session. Use it for instructions that should always apply to that project.

```
your-project/
└── CLAUDE.md   ← Claude reads this every session
```

Example contents:

```markdown
# Project: Payments API
Node.js / TypeScript / Express / Prisma / PostgreSQL

## Conventions
- Use async/await, not callbacks
- All handlers in src/api/handlers/
- Run npm test before committing
- Follow existing error handling pattern in src/middleware/error.ts
```

You can also create a personal `CLAUDE.md` at `~/.claude/CLAUDE.md` for preferences that apply across all your projects.

### Auto memory

Claude Code can also write its own notes as it learns things about your project — build commands, debugging patterns, preferences you've corrected. These are stored in `~/.claude/projects/<project>/memory/` and loaded automatically.

You can view and edit everything Claude has saved by running `/memory` inside a Claude Code session.

### What this means in practice

If you're working in Claude Code, set up a `CLAUDE.md` with your project's conventions. You won't need to repeat context at the start of every session.

---

## Cowork — Cowork Projects

Cowork has its own Projects feature inside Claude Desktop. A Cowork project is a persistent workspace with its own files, instructions, and memory — useful for recurring or long-running tasks.

!!! note
    Memory is retained within a Cowork project but is **not** carried over to standalone Cowork sessions or to Claude.ai web conversations.

### When to use a Cowork Project

- You run the same type of task regularly (e.g., a weekly report)
- You want Claude to remember context between Cowork sessions for a specific workflow
- You want to organise related Cowork tasks together

### How to create a Cowork Project

1. Open Claude Desktop and go to the **Cowork** tab
2. Create a new project and give it a name
3. Add any standing instructions or files the project should always have access to
4. Run all related tasks from inside that project

---

## Which one do I use?

For most developers on this team the answer is straightforward:

- **Doing T-SQL / Infios work in SSMS, Page Editor, or Advantage Architect?** → Set up a **Claude.ai web Project** with your schema and conventions
- **Using Claude Code in a local project?** → Create a **CLAUDE.md** in that project
- **Running recurring autonomous tasks in Cowork?** → Create a **Cowork Project**

The three systems don't share context, so if you work across tools you'll need to maintain context in each one separately.
