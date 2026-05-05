# Best Practices

Tips for getting better results from Claude across all three tools.

## Give Claude context

The more Claude knows about your project, the better its output. At the start of a Claude Code session, describe what the project is and what you're working on:

> "This is a Node.js/TypeScript REST API using Express and Prisma. I'm working on the auth module. The codebase follows this structure: [describe it]."

For the web, use **Projects** and add a system prompt with your stack, conventions, and any standing rules.

## Be specific about the problem

Vague prompts produce vague results.

| Instead of... | Try... |
|---|---|
| "Fix the login bug" | "The JWT token isn't being validated in `src/middleware/auth.ts` — the expiry check is missing" |
| "Write tests for this" | "Write Jest unit tests for `src/services/userService.ts`, covering the `createUser` and `deleteUser` functions" |
| "Refactor this" | "Refactor `src/api/orders.ts` to replace the callback-based DB calls with async/await using Prisma" |

## Review before accepting

Claude Code shows you diffs and plans before making changes. Read them. Claude is fast, not infallible — especially for changes that touch business logic or have side effects.

## Include error messages

When debugging, paste the full error message and stack trace. Don't paraphrase it. The exact text often contains the information Claude needs to identify the root cause.

## Use `/clear` to reset context

In Claude Code, if a session has gone in the wrong direction or the context has gotten cluttered, use `/clear` to start fresh without losing your file changes.

## Break large tasks into steps

Claude handles large tasks well, but if you're doing something complex — like a multi-file refactor — it helps to describe the goal first and ask Claude to outline a plan before executing. You can then approve the plan and proceed step by step.

## Don't over-explain what to do — explain what you want

You don't need to tell Claude which functions to call or how to implement something. Describe the outcome you want. Claude will figure out the implementation.

> Instead of: "Call the `validateToken` function and check the `exp` field"  
> Try: "Add JWT expiry validation to the auth middleware"
