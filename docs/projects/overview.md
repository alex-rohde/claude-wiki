# Projects and Memory — Overview

Each Claude tool has its own way of maintaining context across sessions. They are separate, context does not carry over between the web, Claude Code, and Cowork.

| Tool | Feature | Set up by |
|---|---|---|
| **Claude.ai Web** | Projects | You |
| **Claude Code** | `CLAUDE.md` files + Auto memory | You + Claude |
| **Cowork** | Cowork Projects | You |

---

## Which one do I use?

For most developers on this team the answer is straightforward:

- **Doing T-SQL / Infios work in SSMS, Page Editor, or Advantage Architect?** → Set up a **Claude.ai web Project** with your schema and conventions
- **Using Claude Code in a local project?** → Create a **CLAUDE.md** in that project
- **Running recurring autonomous tasks in Cowork?** → Create a **Cowork Project**

The three systems don't share context, so if you work across tools you'll need to maintain context in each one separately.
