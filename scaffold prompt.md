# Scaffold Prompt

Paste everything below into Claude Code in a new empty project folder to recreate this repository as faithfully as possible.

---

Build a local-first project named `JobSearchAutomation`.

Do not start onboarding. Do not ask for job criteria. Do not ask for a resume upload. Do not run the workflow. Your job in this session is only to scaffold the repository so that a later session can run it.

Your output should be the repository itself, not a high-level approximation.

## Primary Goal

Reproduce this project with the same operating model:

- `workflows/` contains step-by-step written procedures the agent follows
- `tools/` contains deterministic Python scripts that do repeatable work
- `run.md` is the main entry point the agent reads when the user says things like `start`, `go`, or `run the workflow`
- the agent handles judgment, narration, and sequencing
- Python tools handle file I/O, Google APIs, and repeatable mechanics

This is a local Claude Code automation project, not a web app, not a SaaS, not a Kubernetes deployment, and not a cloud platform.

## Non-Negotiable Constraints

- No hardcoded credentials, API keys, tokens, or personal data
- Google auth must be machine-local only, using `~/.config/gws/client_secret.json` and `~/.config/gws/token.json`
- Indeed access is via Claude's Indeed integration in `claude.ai`, not via custom scraping code
- `.env` stores project state and user preferences, but not secrets
- `.tmp/` is for temporary/regenerable files
- Resume tailoring must preserve the original resume formatting by using the original `.docx` as the template
- The generated repo must include public-sharing files: `.gitignore`, `.env.example`, and a noncommercial license
- Preserve a legacy `tools/update_sheet.py` stub even though the active implementation lives in `tools/sheets.py`

## Create This Exact Repository Shape

Create these folders and files:

```text
.claude/settings.json
.claude/settings.local.json
.gitignore
.env.example
CLAUDE.md
LICENSE
README.md
requirements.txt
run.md
scaffold prompt.md
ideal_role.json
resume/README.txt
tools/drive_helpers.py
tools/drive_upload.py
tools/google_auth.py
tools/notify.py
tools/onboarding.py
tools/search_indeed.py
tools/sheets.py
tools/tailor_resume.py
tools/update_sheet.py
workflows/onboarding.md
workflows/search_jobs.md
workflows/tailor_resume.md
```

Also create the directories `resume/`, `tools/`, `workflows/`, `.claude/`, and `.tmp/`.

## File-by-File Requirements

### `.claude/settings.json`

Create a JSON file with:

- empty `mcpServers`
- `allowedTools` including:
  - `mcp__claude_ai_Indeed__search_jobs`
  - `mcp__claude_ai_Indeed__get_job_details`
  - `mcp__claude_ai_Indeed__get_company_data`
  - `mcp__claude_ai_Indeed__get_resume`
  - `Bash`
  - `Read`
  - `Write`
  - `Edit`
  - `Glob`
  - `Grep`

### `.claude/settings.local.json`

Create a local-permissions file that reduces approval friction for this project.

Include allow rules for:

- the four Indeed MCP tools above
- `Bash(py tools/*)`
- `Bash(python tools/*)`
- `Bash(cd * && py tools/*)`
- `Bash(cd * && python tools/*)`
- `Bash(powershell*)`
- `Bash(date*)`
- `Bash(ls*)`
- `Write(.tmp/*)`
- `Write(resume/*)`

### `.gitignore`

Ignore:

- `.env`
- `.tmp/`
- `__pycache__/`
- `*.pyc`, `*.pyo`, `*.pyd`
- `.pytest_cache/`
- `.mypy_cache/`
- `.ruff_cache/`
- all files in `resume/` except `resume/README.txt`
- `.DS_Store`
- `Thumbs.db`

### `.env.example`

Create an env template with these keys in this order:

```text
RESUME_DRIVE_URL=
JOB_TITLES=
LOCATION=
SALARY_MIN=0
KEYWORDS=
GOOGLE_SHEET_ID=
USER_EMAIL=
DRIVE_FOLDER_ID=
ROLE_FUNCTION=
ROLE_CATEGORY=
SECTOR=
ORG_SIZE=
JOB_LEVEL=
JOB_LEVEL_TYPE=
LAST_SEARCH_DATE=
```

### `LICENSE`

Use `PolyForm Noncommercial 1.0.0`.

The repo must clearly allow noncommercial use and disallow commercial use without separate permission.

### `requirements.txt`

Use exactly:

```text
google-api-python-client
google-auth-oauthlib
google-auth-httplib2
python-dotenv
python-docx
```

### `resume/README.txt`

Create a plain one-line instruction telling the user to drop their resume there in PDF, DOC, or DOCX format.

### `ideal_role.json`

Create this file as an empty placeholder or a tiny comment-free JSON placeholder object, but do not put real data in it.

### `CLAUDE.md`

Write a project operating guide with these rules:

- use the `workflows/` plus `tools/` split consistently
- find and read the relevant workflow before taking action
- prefer existing tools over manual chat execution
- stop and flag obvious mismatch before spending meaningful compute, API calls, or user time
- if a resume is clearly misaligned with the target role, pause before continuing
- fix the smallest issue when commands fail, then rerun carefully
- update workflows deliberately, not casually
- `.tmp/` is temporary, `.env` holds state, `~/.config/gws/` holds Google auth
- on Windows, detect Python by trying `py --version`, then `python --version`, then `python3 --version`
- do not pass large text blobs on the command line; write them to `.tmp/` files first
- explain permission prompts in plain English during interactive setup
- if the user says `start`, `go`, `run`, `run the workflow`, `start the workflow`, `let's go`, `kick it off`, or `do your thing`, read `run.md` and execute from there

### `README.md`

Write user-facing documentation matching this project's actual behavior.

It must:

- explain that the project searches Indeed, tracks results in Google Sheets, and tailors resumes
- say the first run is guided onboarding and later runs are silent/automatic
- recommend the career workshop resources and mention `ideal_role.json`
- explain the prerequisites:
  - Google account
  - connect Indeed in `claude.ai -> Settings -> Integrations`
  - place a resume in `resume/`
- explain onboarding in plain English
- explain the scheduled run behavior
- contain a `Public Repo Notes` section that says:
  - copy `.env.example` to `.env` before first run
  - do not commit `.env`, `.tmp/`, or actual resume files
  - this is a local project and does not need Kubernetes/cloud infrastructure
  - it depends on local execution, Claude Code, Indeed integration, and user-owned Google OAuth
- contain a `License` section referencing `PolyForm Noncommercial 1.0.0`
- explain how to change criteria by deleting relevant `.env` lines and running again

### `run.md`

Write the entry point doc.

It must say:

- this file is read every time the workflow starts
- the system does three things:
  - searches Indeed
  - adds new jobs to a Google Sheet
  - rewrites the resume for each new job
- onboarding runs first and self-skips when `.env` is complete
- after onboarding, the workflow should stop and offer an optional initial run
- recurring automation should be set up through Claude Code with the `/loop` slash command
- include examples such as:
  - `/loop 5m check the deploy`
  - `/loop 30m /babysit-prs`
  - `/loop 1h run the workflow`
- job search then runs silently
- Claude should narrate major actions in plain English before doing them
- the security model is:
  - `.env` stores preferences and URLs
  - Google credentials live in `~/.config/gws/`
  - Indeed auth is handled by `claude.ai`
- include a troubleshooting table

## Python Tool Requirements

Create real Python files, not pseudocode. They should be runnable from the command line.

### `tools/google_auth.py`

Implement:

- Google OAuth using:
  - `CLIENT_SECRET_PATH = ~/.config/gws/client_secret.json`
  - `TOKEN_PATH = ~/.config/gws/token.json`
- scopes:
  - `https://www.googleapis.com/auth/drive.file`
  - `https://www.googleapis.com/auth/spreadsheets`
  - `https://www.googleapis.com/auth/gmail.send`
- `get_credentials()`
  - loads token if present
  - refreshes expired token if possible
  - otherwise launches browser OAuth flow
  - saves token back to disk
- `check_auth()`
  - returns True/False without starting a fresh auth flow unless refresh is possible
- when run as a script:
  - print friendly progress lines
  - exit `0` on success
  - exit `1` on missing `client_secret.json` or other auth failure

### `tools/drive_helpers.py`

Implement `get_or_create_folder(service, name, parent_id=None)` using the Google Drive API.

Behavior:

- find a non-trashed folder by exact name
- if `parent_id` is provided, constrain the search to that parent
- if found, return the existing folder ID
- if not found, create it and return the new folder ID

### `tools/drive_upload.py`

Implement:

- `upload_file(local_path, folder_id=None)`
  - validate the local file exists
  - upload to Drive using `drive.file` access
  - place in `folder_id` if provided
  - set permission to `anyone with link can view`
  - return `webViewLink`
- command line interface:
  - `--file`
  - `--folder_id`
- clean error handling and exit codes

### `tools/onboarding.py`

Implement actions:

- `create_project_folder`
- `write_env`
- `read_env`
- `check_env`

Behavior:

- `.env` lives at the project root
- `.env` parser ignores blanks and `#` comments
- `write_env` updates an existing key in place or appends it
- `create_project_folder` authenticates via `tools.google_auth` and creates/finds a top-level Drive folder named `JobSearchAutomation`
- `check_env` must enforce this exact required field list:

```text
DRIVE_FOLDER_ID
RESUME_DRIVE_URL
USER_EMAIL
JOB_TITLES
LOCATION
SALARY_MIN
KEYWORDS
GOOGLE_SHEET_ID
```

### `tools/sheets.py`

Implement a real Google Sheets integration with these actions:

- `create`
- `get_urls`
- `get_job_hashes`
- `append_row`
- `update_notes`

Behavior:

- create a spreadsheet named `Job Tracker`
- create a tab named `Jobs`
- header row must be:

```text
Date Found, Job Title, Company, Location, Salary, Date Posted, URL, Status, Notes, Job ID
```

- `get_urls` reads column G, skipping the header
- `get_job_hashes` reads column J, skipping the header
- `append_row` accepts a 10-value JSON array and returns the new 1-based row number
- `update_notes` writes to column I
- if `folder_id` is passed to `create`, move the sheet into that Drive folder
- include `extract_job_hash(job_id)` that extracts the stable 5th dash-delimited segment of the Indeed job ID and lowercases it

### `tools/update_sheet.py`

Create a legacy stub file, not the active implementation.

It should:

- explain in the docstring that it is legacy and the real implementation lives in `tools/sheets.py`
- define placeholder functions that raise `NotImplementedError`
- keep a basic CLI for backward reference

### `tools/search_indeed.py`

This script does not call Indeed directly. The agent calls the Indeed MCP tools and passes results into this script for filtering and backup.

Implement:

- `parse_keywords(raw)`
  - parse strings like `include:python,sql exclude:unpaid,internship`
- `parse_date_posted(date_str)`
  - support:
    - `Just posted`
    - `Today`
    - `Active today`
    - `Posted today`
    - `Yesterday`
    - `X day ago`
    - `X days ago`
    - `30+ days ago`
    - `YYYY-MM-DD`
    - `March 7, 2026`
    - `Mar 7, 2026`
- `filter_by_date(results, since_date_str)`
  - fail open on unparseable dates
- `save_to_tmp(results, suffix="")`
  - write timestamped JSON files to `.tmp/`
- CLI arguments:
  - `--titles`
  - `--location`
  - `--salary_min`
  - `--keywords`
  - `--since_date`
  - `--results_file`
- behavior:
  - read raw results from `--results_file` if provided, otherwise stdin
  - save raw backup
  - apply date filter
  - save filtered backup
  - print a summary to stderr
  - print filtered JSON to stdout

### `tools/notify.py`

Implement:

- Gmail API email send from the user's own account to themselves
- a helper `build_email(...)` that creates both plain text and HTML variants
- `send_resume_notification(to, job_title, company, resume_url, job_url, sheet_url)`
- CLI arguments:
  - `--to`
  - `--job_title`
  - `--company`
  - `--resume_url`
  - `--job_url`
  - `--sheet_url`
- success prints `ok`
- errors print to stderr and exit nonzero

### `tools/tailor_resume.py`

Implement template-preserving resume tailoring mechanics.

Actions:

- `read_resume_structured`
- `create_doc_from_template`
- `convert_to_docx`

Behavior:

- search `resume/` for a resume, preferring `.docx`, then `.doc`, then `.pdf`
- `read_resume_structured`
  - read the source `.docx`
  - emit one line per paragraph as `N|[TAG] text`
  - support tags:
    - `[HEADER]`
    - `[BULLET]`
    - `[NORMAL]`
    - `[EMPTY]`
  - detect headings using style name, boldness, or all-caps heuristics
  - if the file is `.doc`, raise a useful error telling the user to run `convert_to_docx`
- `convert_to_docx`
  - use LibreOffice if installed
  - try common Windows, macOS, and Linux `soffice` locations
  - if not installed, raise a helpful error
- `create_doc_from_template`
  - read a rewritten content file from `.tmp/`
  - parse numbered lines back into paragraph rewrites
  - copy the original resume to `.tmp/`
  - replace paragraph text in the copy while preserving original formatting
  - upload the new `.docx` to a `Tailored Resumes` folder in Drive, nested under `DRIVE_FOLDER_ID` if present
  - return the Drive URL
- never pass rewritten resume content inline on the shell; always use `--content_file`

## Workflow Markdown Requirements

These are critical. Make the workflow docs specific enough that a future Claude session can actually operate the project from them.

### `workflows/onboarding.md`

Write a complete first-run workflow.

It must include:

- objective: collect config once, save to `.env`, and skip if complete
- Step 1: run `tools/onboarding.py --action check_env`
  - if output is `[]`, log that onboarding is skipped and stop
- Step 2: dependency checks
  - Python detection order: `py`, `python`, `python3`
  - success only if output contains `Python 3.`
  - install Python if all fail
  - install Python packages from `requirements.txt`
  - verify Indeed MCP exists or stop with instructions to reconnect it in `claude.ai`
- Step 2d: Google Cloud project setup explanation in plain English
- require Drive, Sheets, and Gmail Send scopes
- explain `External` does not mean public
- require the user to add themselves as a test user
- move `client_secret.json` into `~/.config/gws/client_secret.json`
- run `python tools/google_auth.py`
- if auth already exists but `USER_EMAIL` is missing, re-auth to add the `gmail.send` scope
- create the Drive folder and save `DRIVE_FOLDER_ID`
- Step 3: job search criteria
  - if `ideal_role.json` exists, use it to populate:
    - `ROLE_FUNCTION`
    - `ROLE_CATEGORY`
    - `SECTOR`
    - `ORG_SIZE`
    - `JOB_LEVEL`
    - `JOB_LEVEL_TYPE`
    - `JOB_TITLES`
    - `KEYWORDS`
  - if it does not exist, offer the workshop resources first, otherwise collect fields manually
- always ask for `LOCATION` and `SALARY_MIN`
- Step 4: resume review and upload
  - scan `resume/` first
  - if multiple files, ask which one
  - if no files, ask the user to add one and type `done`
  - compare the resume against the role direction
  - warn on obvious mismatch before uploading
  - upload via `tools/drive_upload.py`
  - save `RESUME_DRIVE_URL`
- Step 5: create the Google Sheet using `tools/sheets.py --action create --folder_id "<DRIVE_FOLDER_ID>"`
- Step 6: confirm all saved values and links back to the user
- remind the user that recurring runs can be configured with the `/loop` slash command, and include example commands

### `workflows/search_jobs.md`

Write a silent scheduled-run workflow.

It must include:

- never ask the user anything during normal execution
- load config from `.env`, including `LAST_SEARCH_DATE`
- call Indeed via:
  - `mcp__claude_ai_Indeed__search_jobs`
  - `mcp__claude_ai_Indeed__get_job_details`
- normalize each result to include:
  - `job_id`
  - `job_hash`
  - `title`
  - `company`
  - `location`
  - `salary`
  - `date_posted`
  - `url`
  - `description`
- write raw results to a temp file and run `tools/search_indeed.py`
- deduplicate against `tools/sheets.py --action get_job_hashes`
- append rows with 10 columns including `job_hash`
- call `workflows/tailor_resume.md` for each new job
- write the returned Drive URL back to the Notes column
- save today's date into `.env` as `LAST_SEARCH_DATE`
- send a Windows desktop notification summarizing jobs found, resumes created, and errors
- log errors to `.tmp/search_jobs_log.txt`

### `workflows/tailor_resume.md`

Write a per-job tailoring workflow.

It must include:

- inputs:
  - `job_url`
  - `job_description`
  - `company`
  - `job_title`
  - `sheet_row`
- Step 0: resume-role fit check
  - if obviously mismatched, pause and ask before spending compute
- Step 1: run `python tools/tailor_resume.py --action read_resume_structured`
- if `.doc`, convert first
- Step 2: analyze the job description for required skills, responsibilities, tone, and priorities
- Step 3: rewrite the resume in the exact same paragraph-numbered format
  - same paragraph count
  - same role order
  - no invented facts
  - no crossing role boundaries
  - preserve all numbers, dates, company names, and factual content
- Step 3.5: verify section integrity before saving
- Step 4: write rewritten content to `.tmp/resume_<Company>_<JobTitle>.txt`
- Step 5: build and upload the tailored resume with `tools/tailor_resume.py --action create_doc_from_template`
- Step 6: update the Notes column in the Google Sheet
- Step 7: email the user with `tools/notify.py`
- Step 8: return the Drive URL
- log errors to `.tmp/tailor_resume_log.txt`

## Acceptance Criteria

Before stopping, verify all of the following:

1. Every file listed in the repository shape exists.
2. All Python scripts have working CLIs and import paths.
3. `tools/update_sheet.py` exists as a legacy stub, not a duplicate implementation.
4. The workflows reference the active Python tools consistently.
5. The public-sharing files exist: `.gitignore`, `.env.example`, `LICENSE`.
6. The repo clearly uses local execution and user-owned Google OAuth, not hosted cloud infrastructure.
7. No credentials or tokens are stored in the project folder.

## Final Response

When done, show:

- the complete file tree
- the contents of `run.md`
- the contents of `.claude/settings.json`
- the contents of `.claude/settings.local.json`
- a short confirmation that:
  - no credentials are stored in the repo
  - Google auth is machine-local in `~/.config/gws/`
  - Indeed auth comes from `claude.ai`
  - commercial use is not allowed under the included license
