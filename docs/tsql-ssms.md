# Using Claude for T-SQL and Stored Procedures

This page covers how to use Claude effectively when working in SSMS, Page Editor, and Advantage Architect. Since the code lives in SQL Server and not on a local filesystem, **Claude.ai web is the primary tool** for this workflow.

---

## The core workflow

1. Write or select code in SSMS (or copy from Page Editor / Advantage Architect)
2. Paste it into Claude.ai web with context about what you need
3. Review Claude's response, copy the result back into SSMS
4. Test and iterate

The goal of this page is to make that loop as fast and accurate as possible.

---

## Set up a Project for your database

The biggest time-saver is a Claude **Project** with your schema loaded in as context. Instead of pasting table definitions into every conversation, you paste them once into the project and every conversation in that project already knows your structure.

### How to set it up

1. Go to [claude.ai](https://claude.ai) and click **Projects** in the left sidebar.
2. Click **New project** and give it a name (e.g., `Infios Dev - [Database Name]`).
3. Click **Project knowledge** and add your schema context (see below).
4. Start all your SQL work from inside this project.

### What to put in the project context

Paste in the `CREATE TABLE` statements for the tables you work with most, or generate them from SSMS:

```sql
-- In SSMS: right-click table → Script Table as → CREATE To → Clipboard
```

Also include:

- **Naming conventions** — e.g., "stored procedures are prefixed with `usp_`, temp tables use `#tmp_`"
- **Common patterns** — e.g., how error handling is done, how transactions are structured
- **Any standing rules** — e.g., "never use `SELECT *`", "always include `SET NOCOUNT ON`"

Example project context:

```
Database: [YourDBName] on SQL Server 2019
Environment: Infios platform — procedures are called by Page Editor and Advantage Architect

Conventions:
- Stored procedures prefixed with usp_
- SET NOCOUNT ON at the top of every procedure
- Use TRY/CATCH for error handling
- Temp tables prefixed with #tmp_
- No SELECT * in production procedures

Key tables:
[paste CREATE TABLE statements here]
```

---

## Common workflows

### Writing a new stored procedure

Give Claude the table context (or rely on the Project), describe the inputs, the logic, and the expected output.

```
Write a stored procedure called usp_GetActivePatientsByProvider.

Parameters:
- @ProviderID INT
- @AsOfDate DATE

Logic: return all patients assigned to the provider where their record is active
as of the given date. Join to the appointments table to include their last
appointment date. Exclude patients with a status of 'Discharged' or 'Inactive'.

Return columns: PatientID, PatientName, DOB, LastAppointmentDate, StatusCode
```

---

### Debugging a failing procedure

Paste the procedure and the full error message. Include the parameters you're calling it with.

```
This stored procedure is throwing the following error:

Msg 8114, Level 16, State 5
Error converting data type varchar to numeric.

I'm calling it with: EXEC usp_ProcessClaim @ClaimID = 10045, @ProcessDate = '2026-05-01'

[paste procedure]
```

---

### Optimizing a slow query

Paste the query and the execution plan, or describe what's slow about it.

```
This query is taking 45+ seconds on a table with ~2M rows. 

[paste query]

The table has indexes on ClaimID and SubmittedDate. The execution plan shows a
clustered index scan on the Claims table. How can I rewrite this or restructure
the indexes to speed it up?
```

To get the execution plan text from SSMS:

1. Run the query with **Include Actual Execution Plan** on (`Ctrl+M`)
2. Right-click the plan → **Save Execution Plan As** → XML
3. Paste the XML into Claude, or describe the expensive operators you see

---

### Refactoring an existing procedure

Tell Claude what's wrong with it and what you want instead. Be specific about what must not change.

```
Refactor this stored procedure. It was written 10 years ago and has grown to
300 lines. Specific issues:
- Cursor being used where a set-based approach would work
- Same subquery repeated four times
- No error handling

Don't change the procedure signature or the output — only change the internals.

[paste procedure]
```

---

### Reviewing a procedure before deploying

```
Review this stored procedure for potential issues before I deploy it to production.
Look for: SQL injection risks, implicit conversions, missing indexes that would
hurt performance, and any edge cases in the logic.

[paste procedure]
```

---

### Explaining unfamiliar code

Useful when inheriting old procedures or reviewing someone else's work.

```
Explain what this stored procedure does step by step. I'm particularly confused
by the logic in the second cursor block.

[paste procedure]
```

---

### Writing documentation

```
Write a comment block for this stored procedure documenting: what it does,
the parameters, return values, and any important notes about side effects.
Use our standard header format:

-- =============================================
-- Author:
-- Create date:
-- Description:
-- Parameters:
-- =============================================

[paste procedure]
```

---

## Tips for better results

**Paste the relevant table definitions.** Even with a Project set up, if you're asking about a table Claude doesn't have context on, paste the `CREATE TABLE` statement. The more Claude knows about column types and nullability, the better its output.

**Include sample data for logic problems.** If the logic depends on specific data shapes, show an example:

```
The input table looks like this:
ClaimID | LineNum | Amount | Status
1001    | 1       | 150.00 | A
1001    | 2       | 75.00  | D
1002    | 1       | 200.00 | A

Expected output:
ClaimID | TotalApproved
1001    | 150.00
1002    | 200.00
```

**Paste the full error.** Don't paraphrase SQL Server error messages. The message number, severity, and state are all useful. Copy the exact text from SSMS.

**Ask for explanations alongside the code.** If you're going to be maintaining this code, ask Claude to explain the approach so you understand what was changed and why:

```
Rewrite this with set-based logic instead of a cursor, and explain what you
changed and why.
```

**Iterate.** Claude's first pass may not be exactly right. Paste back the result and describe what needs adjusting:

```
Good, but the totals are double-counting because claims can appear in both
tables. Add a DISTINCT or adjust the join to fix that.
```

---

## What Claude can't do in this workflow

- **Run your queries** — Claude can't connect to SQL Server. It can write and review code, but you run it in SSMS.
- **See your actual data** — Claude works from what you paste. If a logic problem depends on specific data, include a representative sample.
- **Access Page Editor or Advantage Architect directly** — copy the relevant T-SQL out of those tools and paste it into Claude the same way you would from SSMS.
