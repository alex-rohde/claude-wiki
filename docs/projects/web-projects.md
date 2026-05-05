# Web Projects (Claude.ai)

Projects let you give Claude persistent context that applies to every conversation inside that project. Instead of re-explaining your database schema, naming conventions, or coding standards every session, you write it once in the project and Claude knows it from the start.

---

## When to use a Project

- You work in the same codebase or database regularly
- You want Claude to follow consistent conventions without reminding it each time
- You want to organize work by database, application, or team

---

## How to create a Project

1. Go to [claude.ai](https://claude.ai) and click **Projects** in the left sidebar
2. Click **New project**
3. Give it a name — e.g., `Customer Name - DEV - AAD`
4. Click **Project knowledge** and add your context (see below)
5. Start all related work from inside this project

---

## What to put in a Project

The goal is to capture everything you'd otherwise have to re-explain. Good candidates:

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

Any rules that should always apply: performance constraints, patterns to avoid, required comment headers, etc.

---

## Tips

- Keep the project knowledge focused and concise, long walls of text are less effective than clear, specific rules
- Update it when conventions change or you add new tables
- Create separate projects for different databases or applications rather than one giant project for everything
