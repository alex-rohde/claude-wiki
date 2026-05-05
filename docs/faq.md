# FAQ

## General

**Do I need a separate account for Claude Code and Cowork?**  
No. All three tools — Claude.ai web, Claude Code, and Cowork — use the same Claude account. Sign in once.

**What plan do I need?**  
Pro, Max, Team, or Enterprise. The free Claude.ai plan does not include Claude Code or Cowork access. Check with your manager if you're unsure what you've been provisioned.

**Is my code sent to Anthropic?**  
When you use Claude Code or paste code in the web interface, that content is sent to Anthropic's servers to generate a response. Check your company's data handling policy if you're working with sensitive or proprietary code.

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

## Didn't find your answer?

- Claude Code docs: [code.claude.com/docs](https://code.claude.com/docs)
- Cowork support: [support.claude.com](https://support.claude.com)
- Ask your team lead or whoever provisioned your account
