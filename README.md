# JobSearchAutomation

<p align="center">
  <img src="assets/Job%20Search%20Automation.jpg" alt="Job Search Automation logo" width="320">
</p>
Automatically searches Indeed for jobs matching your criteria, tailors your resume for each one, and tracks everything in a Google Sheet - on a schedule, while you sleep.

---

## How to Start

1. Open this folder in Claude Code
2. Type any of these:

```text
start
go
run the workflow
```

That's it. The first time you run it, you'll be walked through a one-time setup. Every run after that is silent and automatic.

---

## Before You Run for the First Time

Optional but recommended:

Complete one of these free AI-guided career workshops to define your ideal role. They take about 15 minutes and give the job search automation better inputs than manual entry.

[Find Your Way - 5-Step Career Workshop](https://chatgpt.com/g/g-69a00483e9f88191a21ef94004da7893-find-your-way-5-step-career-workshop)

[Linked Positioning Workshop](https://chatgpt.com/g/g-69ee70c2ad888191a34fcb973fd46fc3-linked-positioning-workshop)

When you finish, ask the workshop to export your results as `ideal_role.json` and drop that file into this project folder.

If you skip the workshop, Claude will use a lighter manual setup: job titles, location, salary floor, and keywords.

### Permission Warning

This repo includes project-level Claude Code settings files at `.claude/settings.json` and `.claude/settings.local.json` that are intentionally permissive so `/loop` runs can continue without repeated read/write approval prompts.

Review that file before running the workflow on your machine. It is appropriate only for a local, trusted copy of this repository where you are comfortable allowing Claude Code to read and modify project files without interactive confirmation.

## What You'll Need

| Item | Cost | Notes | Technical Difficulty |
|---|---|---|---|
| Claude subscription | $20/month | Required for access to Claude and the project workflow. | Low |
| Claude Code in VS Code or the Claude Code CLI | Included with Claude membership | Used to run the workflow locally. You can use the VS Code extension or work directly from the CLI. | Low to Medium |
| VS Code | Free | Optional if you prefer the CLI only. Useful for viewing project files and documents. | Low |
| Indeed Connector | Free with Claude subscription | Connect Indeed once through Claude Settings > Connectors. | Medium |
| Google account | Free | Any personal Gmail account works. Claude will walk you through connecting it during setup. | Medium to High |
| Resume file | Free | Place your resume in the `resume/` folder. Supported formats: PDF, DOC, or DOCX. | Low |

---

## Why This Instead of n8n

| Category | This Project | n8n Workflow |
|---|---|---|
| Monthly cost | About $20/month with a Claude subscription. | Often closer to $70/month once you add `n8n` plus an LLM provider and related services. |
| Privacy and security | Uses your local project files and your own connected accounts. No exposed API keys are required for the main Claude-driven workflow. | Usually requires storing and managing API keys and external service credentials across multiple nodes and integrations. |
| Control over Gmail and documents | Built around your own Gmail, Google Docs, Google Drive, and Google Sheets access during guided setup. | Can do the same work, but you must wire and maintain each connection yourself inside the workflow. |
| Initial technical setup | Requires a Claude subscription, Claude Code in VS Code or the Claude Code CLI, a Google account, and one-time Google and Indeed connection setup. | Requires an `n8n` account, workflow creation, model/provider selection, API key setup, and account connections for the job-search flow. |
| AI model setup | Claude is already the core experience, so there is less model plumbing to configure. | Usually requires choosing a provider such as OpenAI, Claude, or Google and manually adding API keys and billing. |
| Tool availability | High. Claude has strong access to useful integrations and workflow tools for this use case. | High. `n8n` has a large plugin and integration ecosystem built for automation. |
| Workflow setup effort | After the files are on your computer, you open the project in Claude Code for VS Code or run Claude Code from the CLI. It walks you through the setup step by step. | You typically need to build or import the workflow, adjust nodes, connect services, and troubleshoot triggers before it is usable. |
| Runtime behavior | Runs locally through Claude Code in VS Code or the CLI. For automatic recurring runs, use the `/loop` slash command from Claude Code to schedule this workflow. | Runs in the cloud once deployed, so it can keep running continuously without your desktop app staying open. This is better for always-on automations and immediate notifications. |
| Modifying or starting the workflow | Start by opening the project and telling Claude to run it. Most setup is guided in plain language. | Starting or changing the workflow often means editing several nodes, triggers, and credentials in the `n8n` editor. |
| Best fit | Better for someone who wants guided setup with less technical overhead. | Better for someone who wants to design and maintain a more technical automation stack manually. |

### Workflow Comparison

| Workflow | Notion + n8n Version | This Project |
|---|---|---|
| Workflow 1: Auto-Fetch Jobs | Pulls new job postings from your target companies daily. | Already does this. It fetches new jobs on a schedule and avoids duplicates. |
| Workflow 2: Relevance Scoring | Scores each job based on your criteria with a detailed score breakdown. | Partially covered today. This project already filters and tailors to relevant roles, and adding explicit scoring is straightforward. |
| Workflow 3: Match Contacts | Links jobs to people you know at those companies. | Does not do this yet. This is the main non-trivial gap because it requires reliable company-to-contact matching logic and contact data structure. |
| Workflow 4: Generate Materials | AI creates personalized resumes, cover letters, and outreach messages using the Advice Triangle framework. | Largely does this already for personalized resumes. Cover letters and outreach messages are simple extensions. |
| Workflow 5: Daily Morning Briefing | Daily audio summary of new opportunities, outreach reminders, contact gaps, and networking nudges sent to your email. | Does not do this yet, but it is a relatively simple add-on. |
| Workflow 6: Auto-Update Next Action | Automatically sets follow-up dates and next steps when you update a contact's outreach status. | Does not do this yet, but it is a relatively simple add-on once contact tracking is defined. |
| Workflow 7: Auto-Delete Stale Jobs | Automatically archives jobs older than 15 days that you haven't acted on. | Does not do this yet, but it is a relatively simple add-on. |
| Workflow 8: Company Intelligence Brief | On-demand research brief with product analysis, industry positioning, recent news, and interview talking points delivered as a PDF to your email. | Does not do this yet, but it is a relatively simple add-on. |
| Workflow 9: Error Notifications | Get alerted via email if any workflow fails. | Does not do this yet, but it is a relatively simple add-on. |
| Tracking Template | Uses a Notion template. | Uses Google Sheets for tracking instead of Notion. |

In practical terms, the biggest functional gap is contact matching. Most of the other missing pieces are lighter workflow additions around reporting, reminders, notifications, or extra generated outputs.

---

## What Happens on First Run

Claude walks you through setup step by step:

1. Checks Python and required packages
2. Confirms Indeed is connected
3. Sets up your Google connection
4. Confirms your resume
5. Collects your job-search criteria
   If `ideal_role.json` is present, Claude can also auto-fill a richer role profile.
6. Creates your Google Sheet tracker and uploads your resume to Drive
7. Confirms everything and reminds you how to run it from Claude Code in VS Code or the CLI, including scheduling with `/loop` if you want automation
8. Offers an optional initial run instead of starting one by default

---

## What Happens on Scheduled Runs

Everything below happens automatically:

- Searches Indeed for new matching jobs
- Skips anything already in your sheet
- Adds new jobs to the Google Sheet
- Tailors your resume for each new job
- Saves each tailored resume to Google Drive and links it in the sheet

To set up recurring runs from Claude Code, use the `/loop` slash command. Examples:

```text
/loop 5m check the deploy
/loop 30m /babysit-prs
/loop 1h run the workflow
```

---

## Folder Structure

```text
ideal_role.json  -> Optional workshop output
resume/          -> Put your resume here
workflows/       -> Step-by-step workflow instructions Claude follows
tools/           -> Python scripts that do the actual work
.claude/         -> Project configuration
requirements.txt -> Python packages this project needs
.env             -> Saved preferences created during onboarding
run.md           -> Entry point Claude reads to run the workflow
README.md        -> This file
```

---

## Changing Criteria

Delete the relevant line or lines from `.env`, then run the workflow again.

To update your role profile, edit `ideal_role.json` directly or redo the workshop and replace the file, then delete the relevant lines from `.env` and run again.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Google sign-in failed or expired | Run `python tools/google_auth.py` in a terminal |
| Indeed not found | Go to claude.ai -> Settings -> Integrations and reconnect |
| Wrong job criteria | Delete the relevant lines from `.env` and run again |
| Want to use a different resume | Replace the file in `resume/` and delete `RESUME_DRIVE_URL` from `.env` |

### Scheduled Task Keeps Asking for Approval

If a `/loop`-scheduled Claude Code run keeps asking for approval to read a `SKILL.md` file, that is normal behavior and is not specific to this project.

Claude Code uses a permission system with `allow`, `ask`, and `deny` rules. If the `/loop` task is running in a mode that still prompts for a tool or file access, the task will pause until you approve it. Anthropic's docs also note that `ask` rules take precedence over `allow` rules, so a task can keep prompting if its permissions are not fully pre-authorized.

To make the task run autonomously:

1. Run the same command manually once before relying on `/loop`
2. Approve each permission prompt and choose `always allow` when offered
3. Re-run the `/loop` schedule afterward so future executions can use the same permissions without stopping

If you want to pre-authorize access directly, you can also configure permission rules in your Claude Code settings file, such as `~/.claude/settings.json`, so the required read access is already allowed before the `/loop` schedule runs.

---

## License

This project is licensed under `PolyForm Noncommercial 1.0.0`.
