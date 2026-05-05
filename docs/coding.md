# General Coding

Claude can help at every stage of the development process. Writing new code, debugging, refactoring, reviewing, and writing tests are all possible with Claude. This page covers how to get the most out of it for each workflow.

---

## General principles

**Give Claude the right context.** Before diving into a task, tell Claude what it's working with:

- Language and framework
- Relevant file paths
- Any constraints or conventions to follow

In Claude Code, this happens naturally because Claude can read your project files. In the web interface, paste the relevant code and describe the setup.

**Describe the outcome, not the steps.** You don't need to tell Claude which functions to call or how to structure the solution. Describe what you want the code to do.

> Instead of: "Create a function that loops through the array and checks each item"  
> Try: "Write a function that returns all users whose subscription has expired"

**Include the full error.** When debugging, paste the complete error message and stack trace, not a paraphrase. The exact wording often contains what Claude needs.

---

## Writing new code

Tell Claude what the code should do, where it fits in the codebase, and any interfaces it needs to match.

**Example prompts:**

```
Write a middleware function for Express that validates a JWT token and attaches
the decoded user to req.user. Use the jsonwebtoken package. Return a 401 if the
token is missing or invalid.
```

```
Add a Prisma query to src/services/userService.ts that fetches all users who
haven't logged in within the last 90 days. Return just their id and email.
```

```
Create a React hook called useDebounce that takes a value and a delay in ms
and returns the debounced value. Use TypeScript.
```

**Tips:**

- Specify the exact file path when you want code to fit into an existing module.
- Mention packages already in the project rather than letting Claude choose its own.
- If you have a coding style or convention, describe it or show an example from the codebase.

---

## Debugging

Paste the broken code, the full error message, and a brief description of what the code is supposed to do.

**Example prompts:**

```
This function is throwing "Cannot read properties of undefined (reading 'id')".
The full stack trace is below. The function is supposed to find the matching order
and return the user's id from it.

[paste code]
[paste stack trace]
```

```
This API route returns a 200 but the response body is always empty. Expected it
to return the created record. Here's the handler:

[paste code]
```

**Tips:**

- Include what you've already tried, it stops Claude from suggesting things you've ruled out.
- If the bug is intermittent, describe the conditions when it occurs.
- For async bugs, mention whether the code is running in a browser, Node, or both.

---

## Refactoring

Tell Claude what's wrong with the existing code and what you want instead. Be specific "clean this up" is hard to act on.

**Example prompts:**

```
Refactor this function to replace the nested callbacks with async/await.
Don't change the behavior or the function signature.

[paste code]
```

```
This file has grown to 400 lines and mixes data fetching, transformation, and
rendering. Split it into three files: a service for the API calls, a utils file
for the data transformation, and keep the component lean. Here's the current file:

[paste code]
```

```
Replace the repeated error handling in these three route handlers with a shared
middleware. The pattern is always the same: catch the error, log it, return a
500 with a JSON body.

[paste code]
```

**Tips:**

- Tell Claude what must not change. Function signatures, return types, behavior, variables.
- Ask Claude to explain what it changed and why, especially for large refactors.
- For big refactors, ask for a plan first before Claude starts writing code.

---

## Code review

Paste a diff or a block of code and ask Claude to review it for a specific concern.

**Example prompts:**

```
Review this function for security issues. It takes user input and builds a
database query.

[paste code]
```

```
I'm about to merge this PR. Review it for any edge cases I might have missed,
particularly around error handling and concurrent requests.

[paste diff or code]
```

```
Does this SQL query have any performance problems at scale? The orders table
will have millions of rows.

[paste query]
```

**Tips:**

- Scope the review. "Review this" is broad. "Review this for SQL injection risks" or "review this for race conditions" gets more useful feedback.
- Ask for severity ratings if you want to triage findings quickly.
- Follow up on any finding you don't understand, ask Claude to explain the risk and show a fixed version.

---

## Writing tests

Paste the code you want tested and specify the testing framework and what kinds of tests you need.

**Example prompts:**

```
Write Jest unit tests for this function. Cover the happy path, invalid input,
and the case where the database call throws an error.

[paste function]
```

```
Write integration tests for these three API endpoints using Supertest. The
database is already seeded in beforeAll. Test that auth is enforced, that
valid requests return the correct shape, and that invalid input returns a 400.

[paste route handlers]
```

```
This function has no tests and I need to refactor it. Before I change anything,
write tests that cover its current behavior so I can refactor safely.

[paste function]
```

**Tips:**

- Tell Claude which test runner, assertion library, and mocking approach you're using.
- If you want Claude to mock dependencies, specify which ones and how (jest.mock, sinon, etc.).
- Ask for edge cases explicitly. Claude will often add them unprompted, but calling it out helps.

---

## Explaining code

Claude is good at explaining unfamiliar code. This is useful when onboarding, reading legacy code, or picking up someone else's PR.

**Example prompts:**

```
Explain what this function does and why it's structured this way.

[paste code]
```

```
Walk me through this SQL query step by step. I understand basic SQL but I'm
not familiar with window functions.

[paste query]
```

```
What does this regex match? Break down each part.

[paste regex]
```

---

## Working in Claude Code vs the web

| Task | Claude Code (CLI) | Claude.ai Web |
|---|---|---|
| Writing new code in your project | Reads existing files for context automatically | Paste relevant files manually |
| Debugging a live error | Can run commands, inspect logs | Paste the error and code |
| Large refactors across multiple files | Can edit multiple files in one session | Better for single-file work |
| Quick one-off questions | Works, but heavyweight | Faster for simple questions |
| Sharing output with a teammate | Not built for sharing | Share conversation link |

For most coding tasks that touch your actual codebase, Claude Code is the better tool. Use the web for quick questions, exploration, or anything you want to share.

---

## Example: a full coding session with Claude Code

```bash
cd ~/your-project
claude
```

> "This project is a Node/TypeScript REST API using Express and Prisma with PostgreSQL.
> I'm adding a password reset flow. The user model is in src/models/user.ts.
> Start by creating a resetToken field on the user model and a Prisma migration."

Claude reads the existing model, generates the schema change, and creates the migration file. You review the diff and approve it. Then:

> "Now write the POST /auth/reset-password endpoint. It should accept an email,
> generate a secure token, save it with a 1-hour expiry, and send an email using
> the existing mailer in src/services/mailer.ts."

Claude reads your mailer service to match the existing pattern, writes the handler, and shows you the diff. You can steer, ask questions, or approve and move on.
