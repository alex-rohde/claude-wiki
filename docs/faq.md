# FAQ

## General

**Do I need a separate account for Claude Code and Cowork?**  
No. All three tools — Claude.ai web, Claude Code, and Cowork — use the same Claude account. Sign in once.

**What plan do I need?**  
Pro, Max, Team, or Enterprise. The free Claude.ai plan does not include Claude Code or Cowork access. Check with your manager if you're unsure what you've been provisioned.

**Is my code sent to Anthropic?**  
When you use Claude Code or paste code in the web interface, that content is sent to Anthropic's servers to generate a response. Check your company's data handling policy if you're working with sensitive or proprietary code. See [Data Privacy](data-privacy.md) for full details.

**Can Claude remember our previous conversations?**  
Not automatically. Claude only sees the current conversation. To carry context between sessions you have two options: use a Claude Project (web or Cowork) with a system prompt that describes your environment, or use a `CLAUDE.md` file in your project directory for Claude Code. See [Projects and Memory](projects/overview.md).

**Why does Claude sometimes give wrong answers?**  
Claude generates responses based on patterns learned during training — it doesn't look things up or verify facts in real time. It can produce a confident-sounding answer that is incorrect. Always review output before using it, especially for SQL, business logic, or anything with side effects. See [What is AI?](getting-started/what-is-ai.md) for more context on how LLMs work.

**Why am I getting generic or unhelpful responses?**  
Usually one of three things: the prompt is too vague, Claude is missing context about your environment, or the conversation has accumulated too much noise. Try being more specific about what you want, give Claude your schema or file structure, or use `/clear` in Claude Code to reset the context. See [Best Practices](best-practices.md).

---

## Projects and Memory

**What are Claude Projects?**  
Projects are persistent workspaces available in both Claude.ai web and Claude Cowork. You add a system prompt with your stack, conventions, and standing rules — Claude applies that context to every conversation inside the project so you don't have to repeat it. See [Projects Overview](projects/overview.md).

**What is a CLAUDE.md file?**  
A `CLAUDE.md` is a plain text file that Claude Code reads automatically when you start a session in that directory. It serves as Claude's briefing document for the project — schema, naming conventions, environment details, standing rules. You can have a global one that applies everywhere and a project-level one for each codebase. See [Creating a CLAUDE.md](projects/creating-claude-md.md).

**What's the difference between a Project and a CLAUDE.md?**  
Projects are for Claude.ai web and Cowork — context lives in the system prompt. CLAUDE.md is for Claude Code — context lives in a file in your project directory. Both solve the same problem: giving Claude persistent context so it doesn't start from scratch every session.

---

## Claude Code

**Claude Code says `command not found` after installing.**  
Your shell's PATH may not include `~/.local/bin`. Run:
```bash
export PATH="$HOME/.local/bin:$PATH"
```
Then add that line to your `~/.zshrc` or `~/.bashrc` to make it permanent. You can also run `claude doctor` for a guided diagnosis.

**How do I update Claude Code?**  
Native installs update automatically. To force an immediate update:
```bash
claude update
```

**Can I use Claude Code without a terminal?**  
Yes — install the Claude Code extension for VS Code or JetBrains. It gives you a Claude Code panel inside your editor.

**Can Claude Code access files outside my project directory?**  
By default it works within your current directory. You can reference files by path, but Claude won't browse your entire filesystem unprompted.

---

## Claude Cowork

**What's the difference between Claude Code and Cowork?**  
Claude Code is interactive — you guide it step by step in your terminal. Cowork is autonomous — you describe an outcome, it executes a multi-step plan, and delivers finished output files. Cowork also runs in an isolated VM, while Claude Code runs directly in your shell.

**Can Cowork access the internet?**  
Yes, for research tasks.

**Can Cowork access my browser or saved passwords?**  
No. Cowork runs in a sandboxed virtual machine and cannot access other applications, browser sessions, or your system keychain.

**Where does Cowork save output files?**  
Wherever you tell it to in your prompt. Be explicit — e.g., "save to ~/Desktop/output.xlsx".

---

## SQL and Stored Procedures

**Does Claude know our database schema?**  
Not unless you tell it. Claude has no connection to your database. You need to provide the relevant `CREATE TABLE` scripts so it knows the column names, data types, and relationships. Paste them into your prompt, add them to a Claude Project, or include them in your `CLAUDE.md`. See [T-SQL Claude Code](tsql-claude-code.md).

**Can Claude follow our stored procedure template?**  
Yes, if you give it the template. The most reliable approach is to include the sproc template in your global `CLAUDE.md` so Claude Code applies it automatically. For the web, add it to your Project system prompt. See [Global CLAUDE.md Setup](projects/global-claude-md.md).

**Claude wrote a sproc but it doesn't match our conventions. How do I fix that?**  
Either give Claude the template directly in your prompt ("rewrite this following the template below: ...") or set up a CLAUDE.md or Project so it applies the conventions automatically every session. One-off fixes with a follow-up prompt work too — tell it specifically what to change ("use TRY/CATCH", "add SET NOCOUNT ON", "use the #tmp_ prefix").

---

## Didn't find your answer?

- Claude Code docs: [code.claude.com/docs](https://code.claude.com/docs)
- Cowork support: [support.claude.com](https://support.claude.com)
- Ask your team lead or whoever provisioned your account
