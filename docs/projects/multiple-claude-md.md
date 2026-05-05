# Managing Multiple CLAUDE.md Files

If you work across multiple accounts or databases, you need each one to have its own isolated context. Claude Code loads `CLAUDE.md` based on the directory you start it from, so the solution is one directory per account, each with its own `CLAUDE.md`.

---

## Directory structure

```
~/
├── eplus-aad/
│   └── CLAUDE.md        ← ePlus AAD context only
├── customer2-sqldb/
│   └── CLAUDE.md        ← Customer 2 context only
└── customer3-erp/
    └── CLAUDE.md        ← Customer 3 context only
```

When you start Claude Code from `~/eplus-aad/`, it loads only that folder's `CLAUDE.md`. Starting from `~/customer2-sqldb/` loads only that one. No context bleed between accounts.

---

## Setting up a new account

```bash
mkdir ~/customer-name-db
cd ~/customer-name-db
claude
```

Then follow the [Creating a CLAUDE.md](creating-claude-md.md) process from inside that directory. Each account gets its own folder and its own `CLAUDE.md` built from that customer's schema export.

---

## The global CLAUDE.md caveat

`~/CLAUDE.md` (your home directory) loads for **every** Claude Code session started from `~` or any subdirectory. Anything account-specific placed there (schema, DB name, customer name, coding conventions, etc) will bleed into every other account's sessions.

| File | Loads when | Use for |
|---|---|---|
| `~/CLAUDE.md` | Every session | Global preferences only (tone, general coding style) |
| `~/eplus-aad/CLAUDE.md` | Sessions started from that folder | ePlus AAD schema, conventions, environment |
| `~/customer2-sqldb/CLAUDE.md` | Sessions started from that folder | Customer 2 schema, conventions, environment |

Keep account-specific context (schema, DB name, customer, coding conventions) in the project-level `CLAUDE.md`, not the home-level one.
