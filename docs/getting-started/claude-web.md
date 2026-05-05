# Claude.ai Web

The web interface at [claude.ai](https://claude.ai) is the fastest way to start using Claude with no setup required.

## Sign in

Go to [claude.ai](https://claude.ai) and sign in with the account your team provisioned. If you don't have an account yet, ask your manager.

## Key features

### Projects

Projects let you group related conversations and give Claude persistent context like your tech stack, coding standards, or project goals so you don't have to repeat it every session.

To create a project:

1. Click **Projects** in the left sidebar.
2. Click **New project**.
3. Add a name and a system prompt describing your context.

Example system prompt for a dev project:

```
This project is for our Node.js/TypeScript API. We use Express, Prisma, and PostgreSQL.
Follow the existing code style. Prefer async/await over callbacks. Write tests with Jest.
```

### File attachments

You can attach files directly to any message source. Files, error logs, config files, CSVs, PDFs, or images. Use the paperclip icon in the message input.

### Sharing conversations

You can share a conversation URL with a teammate by clicking **Share** at the top of a conversation. The recipient can view it but can't continue it unless they start their own conversation.

## Tips

- **Paste code directly** - Claude handles syntax highlighting automatically.
- **Be specific** - describe the actual problem, not just the symptom. Include file names, error messages, and what you've already tried.
- **Use Projects** for anything you'll return to. Ad-hoc conversations don't carry context between sessions.
