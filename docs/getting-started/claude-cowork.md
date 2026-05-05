# Claude Cowork

Claude Cowork is a feature inside the Claude Desktop app. It lets you describe a multi-step outcome, step away, and come back to finished work. Formatting documents, organized files, spreadsheets with formulas, and more.

Claude Cowork runs code and shell commands in an isolated virtual machine on your Mac. It has access to your local file system but is sandboxed from your browser sessions, other applications, and system credentials.

## Requirements

- macOS (check [claude.ai/download](https://claude.ai/download) for minimum version)
- Paid Claude account (Pro, Max, Team, or Enterprise)
- Internet connection

## Installation

1. Download the Claude Desktop app from [claude.ai/download](https://claude.ai/download).
2. Open the `.dmg` file and drag Claude to your Applications folder.
3. Launch Claude from Applications and sign in with your Claude account.

## Using Cowork

1. Open Claude Desktop.
2. Click the **Cowork** tab at the top of the window.
3. Describe the outcome you want in plain language.
4. Review Claude's planned approach.
5. Let it run. Monitor progress, steer mid-task if needed, or step away and come back.

## Writing good Cowork prompts

Be specific about:

- **Input** — where the source files or data are located
- **Output** — what format you want and where to save it
- **Constraints** — any rules, structure, or style requirements

**Vague:**
> Summarize the reports in my downloads folder

**Specific:**
> Read all CSV files in ~/Downloads/q1-reports/, combine them into a single Excel spreadsheet with one sheet per file and a summary pivot table on the first sheet, and save it to ~/Desktop/q1-summary.xlsx

## Example tasks

- `Go through ~/projects/api/ and write a markdown doc summarizing every API endpoint — method, path, params, and what it returns. Save to ~/Desktop/api-reference.md`
- `Read the 10 PDF files in ~/Documents/contracts/, extract the key dates and payment terms from each, and create a comparison table in Excel`
- `Research our three main competitors and write a formatted comparison document covering pricing, features, and positioning. Save as ~/Desktop/competitive-analysis.md`

## What Cowork can and can't access

| Can access | Cannot access |
|---|---|
| Your local file system | Your browser or browser sessions |
| The internet | Other running applications |
| Shell commands (sandboxed VM) | System keychain or credentials |
| Your Claude Projects and memory | |

## Scheduled tasks

Pro and Max users can schedule Cowork tasks to run automatically on a set cadence. You can also assign tasks from your phone using the Claude mobile app.
