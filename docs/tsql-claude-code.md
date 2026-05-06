# T-SQL Claude Code

This page covers how to use Claude Code (the CLI) for T-SQL work. Because Claude Code reads files directly from your local filesystem, the key is giving it a home folder for your SQL work so it automatically picks up the right context every time you open it.

---

## Why Claude Code for T-SQL?

Claude Code reads your `CLAUDE.md` file automatically when you open it in a directory. That means if you set up a folder for a customer or project and drop a `CLAUDE.md` in it with their schema and conventions, every session starts with full context — no pasting, no repeating yourself.

It also lets you work directly on `.sql` files. Claude Code can read, write, and edit files on disk, so you can have it generate a stored procedure and save it directly to the folder rather than copying from a chat window.

---

## Set up a SQL work folder

### Step 1 — Create the folder structure

Create a dedicated folder for the customer and environment. The top-level `~/sql/` folder holds a global `CLAUDE.md` with conventions that apply everywhere. Each customer/environment pair gets its own subfolder with a `CLAUDE.md` scoped to that database.

```
~/sql/
├── CLAUDE.md                        ← global conventions (applies to all sessions)
├── customer-name/
│   ├── aad-dev/
│   │   └── CLAUDE.md               ← customer AAD dev schema and environment
│   ├── aad-uat/
│   │   └── CLAUDE.md               ← customer AAD UAT schema and environment
│   └── aad-prod/
│       ├── CLAUDE.md               ← customer AAD prod schema and environment
│       ├── sprocs/
│       ├── queries/
│       └── scripts/
```

You can create this from the terminal:

```bash
mkdir -p ~/sql/customer-name/aad-dev
mkdir -p ~/sql/customer-name/aad-uat
mkdir -p ~/sql/customer-name/aad-prod/sprocs
mkdir -p ~/sql/customer-name/aad-prod/queries
mkdir -p ~/sql/customer-name/aad-prod/scripts
```

---

### Step 2 — Create the CLAUDE.md

The `CLAUDE.md` in the environment folder is what gives Claude Code context about that specific database. Create one there:

```bash
touch ~/sql/customer-name/aad-prod/CLAUDE.md
```

Then open it and fill it in. Here's a starting template:

```markdown
# Customer Name — SQL Context

## Database
- Server: [server name or IP]
- Database: [database name]
- SQL Server version: [e.g., 2019]

## Conventions
- Stored procedures prefixed with `usp_`
- Temp tables prefixed with `#tmp_`
- Always include `SET NOCOUNT ON`
- Use `TRY/CATCH` for all error handling
- No `SELECT *` in production code

## Key Tables
[Paste CREATE TABLE statements for the tables you work with most]

## Notes
[Any customer-specific quirks, special patterns, or things to watch out for]
```

The more you put in here, the less you have to explain in each session. Schema definitions, naming rules, and known patterns are all worth including.

---

### Step 3 — Open Claude Code in the folder

Always open Claude Code from inside the environment folder so it picks up the right `CLAUDE.md`. It will also inherit the global `CLAUDE.md` from `~/sql/` automatically.

```bash
cd ~/sql/customer-name/aad-prod
claude
```

From that point on, Claude Code knows both the global conventions and the customer's specific schema, and can read or write any `.sql` file in the folder.

---

## Common workflows

### Writing a new stored procedure

Ask Claude Code to write the procedure and save it directly to the `sprocs/` folder.

```
Write a stored procedure called usp_GetOpenOrders.

Parameters:
- @wh_id NVARCHAR(10)
- @start_date VARCHAR(30)
- @end_date VARCHAR(30)

Logic: return all orders where status = 'OPEN' within the date range for the
given warehouse. Join to t_location to include the location type.

Save it to sprocs/usp_GetOpenOrders.sql
```

Claude Code will create the file using the conventions in your `CLAUDE.md` — the right prefix, `SET NOCOUNT ON`, `TRY/CATCH`, and so on — without you having to specify them every time.

---

### Reviewing a procedure before deploying

Drop the `.sql` file in the folder and ask Claude Code to review it:

```
Review sprocs/usp_ProcessReceipt.sql before I deploy it.
Check for: SQL injection risks, implicit conversions, missing error handling,
and any edge cases in the logic.
```

---

### Refactoring an existing procedure

```
Refactor sprocs/usp_AllocateInventory.sql. It uses a cursor where a set-based
approach would work, and the same subquery is repeated three times.
Don't change the procedure signature or output columns.
```

Claude Code will edit the file in place. You can review the diff before accepting the change.

---

### Debugging a failing procedure

Paste the error message directly into the chat:

```
usp_ProcessShipment is throwing this error when called from Page Editor:

Msg 8114, Level 16, State 5
Error converting data type varchar to numeric.

Called with: @wh_id = '101', @hu_id = 'HU-00442'

The procedure is in sprocs/usp_ProcessShipment.sql
```

Claude Code will read the file and walk through where the type mismatch is likely happening.

---

### Generating test scripts

```
Write a test script for usp_GetOpenOrders that wraps the EXEC in a BEGIN TRAN / ROLLBACK
so I can safely run it against the real database. Save it to scripts/test_usp_GetOpenOrders.sql
```

---

## Tips

**Keep one environment folder per database.** If you mix customers or environments in one folder the context gets muddled. One folder, one `CLAUDE.md`, one database.

**Seed the CLAUDE.md with CREATE TABLE output from SSMS.** Right-click any table in SSMS → Script Table as → CREATE To → Clipboard, then paste it into the `CLAUDE.md`. Claude Code will reference those column names and types automatically.

**Use the `sprocs/`, `queries/`, `scripts/` split.** It keeps things organized and lets you say "look at everything in sprocs/" when you want Claude to review a set of procedures.

**You still run the code in SSMS.** Claude Code writes and edits the files. You copy the final `.sql` into SSMS (or open it from the folder) to execute it. Claude Code can't connect to SQL Server directly.

---

## What this looks like end to end

1. Open terminal, `cd ~/sql/customer-name/aad-prod`, run `claude`
2. Ask Claude to write or modify a procedure — it reads the `CLAUDE.md` automatically
3. Claude creates or edits the `.sql` file in the folder
4. Open the file in SSMS and run it
5. If something's wrong, paste the error back into Claude Code and iterate

The `CLAUDE.md` does the heavy lifting so you spend time on the actual logic, not re-explaining conventions every session.
