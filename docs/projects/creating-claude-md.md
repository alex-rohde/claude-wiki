# Creating a CLAUDE.md from a Database Schema

A `CLAUDE.md` file gives Claude Code persistent context about your project. For database work, the most valuable thing you can put in it is a schema reference: table names, key columns, and how the tables relate to each other.

The challenge: schema exports are often large. A direct paste of a raw DDL or column-list export can be hundreds of kilobytes, which bloats context and slows down every session. The right approach is to have Claude read the schema file and generate a **summarized** CLAUDE.md for you.

---

## The process

### 1. Export your schema to a file

In SSMS, you can generate a column list for an entire database with a query like:

```sql
SELECT
    TABLE_NAME,
    COLUMN_NAME,
    DATA_TYPE,
    CHARACTER_MAXIMUM_LENGTH,
    IS_NULLABLE
FROM INFORMATION_SCHEMA.COLUMNS
ORDER BY TABLE_NAME, ORDINAL_POSITION
```

Export the results as a `.txt` or `.csv` file (right-click the results grid → **Save Results As**). Save it somewhere accessible, your Desktop is fine.

---

### 2. Ask Claude to summarize it into a CLAUDE.md

Start a Claude Code session from the directory where you want the CLAUDE.md to live (your project root, or `~` for your home directory). Then ask:

```
/Users/yourname/Desktop/schema_export.txt — read this and generate a 
summarized CLAUDE.md. Place it where you need it to use for context.
```

Claude will:

1. Read the schema file in chunks (large files can't be read all at once)
2. Identify the table groups, key columns, and naming patterns
3. Write a compact CLAUDE.md organized by functional area rather than raw DDL

The result is a reference that's useful in context, not a line-by-line copy of the export.

---

### 3. Add missing tables iteratively

Claude reads the file in chunks and may miss some tables on the first pass. After the CLAUDE.md is created, spot-check it:

```
Is t_stored_item in the context now?
```

If a table is missing, Claude can look it up directly in the schema file and add it:

```
t_hu_master, t_allocation, t_pick_detail, t_work_q, t_location, 
t_item_master, t_order, t_order_detail should all be included
```

Claude will grep the schema file for each table, pull the column list, and add summarized entries to the CLAUDE.md.

---

### 4. Add environmental context and coding conventions

Once the schema is in place, add a section at the top of the CLAUDE.md with your environment details and any coding standards you want Claude to follow on every session:

```
add environmental context to the CLAUDE.md:
DB = AAD, Microsoft SQL Server Management Studio 18, Customer: ePlus
```

```
add coding conventions:
Stored procedures: usp_ prefix
Temp tables: #tmp_ prefix
Always include SET NOCOUNT ON
Use TRY/CATCH for error handling
No SELECT * in production code
```

Claude will insert an **Environment** block and a **Coding Conventions** section above the schema reference. From that point on, every stored procedure or query Claude writes in that session will follow those rules automatically without needing to repeat them in each prompt.

---

## What a good CLAUDE.md entry looks like

Each table entry should capture:

- The **primary key** and any important foreign keys
- **Key columns** — the ones you'd actually filter or join on
- **What the table represents** in one phrase
- Any flags or status columns that control behavior

**Example:**

```markdown
| `t_order` | **Outbound order header** — (order_id PK, wh_id, order_number, 
status, type_id, customer_id, priority, order_date, 
earliest/latest/actual_ship_date, carrier, carrier_scac, load_id, 
load_seq, ship_to/bill_to addresses, partial_order_flag, client_code) |
```

You don't need every column, just enough that Claude can write correct JOINs and WHERE clauses without guessing.

---

## Tips

**Group tables by function, not alphabetically.**
Raw exports are alphabetical. A CLAUDE.md organized by area (order management, inventory, host integration, yard automation, etc.) is far more useful when Claude needs to reason about which tables to use.

**Include common key columns at the top.**
Columns like `wh_id`, `item_number`, `lot_number`, `order_number`, and `client_code` appear across dozens of tables. Documenting them once at the top saves space and makes JOINs obvious.

**Note integration patterns.**
If certain table groups have a shared pattern — like all `T_AL_DAX_*` tables having `hjs_parent_id` / `hjs_error_number` columns — call that out once rather than repeating it per table.

**Place the CLAUDE.md at the right level.**
- `~/CLAUDE.md` — loaded in every Claude Code session regardless of directory. Good for a schema that's relevant to all your work.
- `~/your-project/CLAUDE.md` — loaded only when you're working inside that project. Good for project-specific conventions.

**Keep it updated.**
When you add tables or the schema changes, ask Claude to add the new entries:

```
t_new_table was added to the schema — add it to CLAUDE.md. 
Here are the columns: [paste column list]
```

---

## Why not just paste the raw DDL?

Raw DDL or a full column export for a large database can easily be 500KB+ and tens of thousands of lines. Loading that into context on every session:

- Slows down responses (more tokens to process)
- Consumes context space that could be used for your actual conversation
- Contains noise (system tables, deprecated tables, irrelevant columns) that doesn't help Claude

A summarized CLAUDE.md for a large WMS database with hundreds of tables typically compresses to 3–5KB — small enough to load instantly, dense enough to be genuinely useful.
