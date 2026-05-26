# GitHub Mass Upload Admin Guide

This guide is for admins who want to bulk-create or bulk-update GitHub Issues and connect them to a GitHub Project.

It is intentionally generic and can be reused for other projects.

## What This Guide Covers

Use this guide when you want to:

- create many GitHub Issues from a planning list
- create or update labels
- create or update milestones
- add existing issues to a GitHub Project
- set Project fields such as `Status` and `Due Date`
- avoid duplicate issues and duplicate sub-issues

This guide assumes project field names stay in English:

- `Status`
- `Due Date`
- `Todo`
- `In Progress`
- `Done`

Optional additional status options:

- `Blocked`
- `Waiting`
- `Review`

## Recommended Setup

For every project, use:

- one GitHub repository for the work items
- one GitHub Project for planning
- one import owner responsible for mass uploads
- one source file for the upload data
- one dry-run or review step before writing

Avoid letting many people run mass upload scripts independently. Normal contributors should edit issues and Project cards in the browser.

## Permissions Needed

The admin running a mass upload needs:

- repository access with `Write` or `Admin`
- project access with `Write` or `Admin`
- a personal access token with repository and project permissions

For classic personal access tokens, use:

```text
repo
project
```

For fine-grained tokens, ensure the token can access:

- the target repository
- Issues: read and write
- Contents: read and write, only if files need to be committed
- Projects: read and write, if available for the account/project type

For user-owned Projects, classic tokens are often simpler because fine-grained token support can be limited.

## Creating a Token

Each admin should use their own token.

1. Open:

   https://github.com/settings/tokens

2. Go to:

   `Personal access tokens` -> `Tokens (classic)`

3. Click:

   `Generate new token` -> `Generate new token (classic)`

4. Set:

   - Note: `project-mass-upload`
   - Expiration: `7 days` or `30 days`

5. Select scopes:

   ```text
   repo
   project
   ```

6. Generate the token and copy it immediately.

7. If the organization uses SSO, authorize the token for the organization.

Never paste the token into chat, issues, screenshots, commits, or documentation.

## Setting the Token Locally

PowerShell:

```powershell
$env:GITHUB_TOKEN="YOUR_TOKEN_HERE"
```

Check whether a token is set:

```powershell
if ($env:GITHUB_TOKEN) { "Token is set" } else { "Token is missing" }
```

Remove the token from the current shell:

```powershell
Remove-Item Env:\GITHUB_TOKEN
```

## Repository and Project Checklist

Before importing:

- [ ] Repository exists.
- [ ] Issues are enabled.
- [ ] Project exists.
- [ ] Project has a `Status` field.
- [ ] `Status` has at least `Todo`, `In Progress`, `Done`.
- [ ] Project has a `Due Date` field of type Date.
- [ ] Labels are defined or can be created by the import script.
- [ ] Milestones are defined or can be created by the import script.
- [ ] The source list has stable task titles.
- [ ] The source list has clear categories/labels.
- [ ] The source list has due dates or milestone mapping.

## Recommended Project Fields

Minimum:

| Field | Type | Purpose |
| --- | --- | --- |
| `Status` | Single select | Board columns |
| `Due Date` | Date | Sorting and scheduling |
| `Assignees` | Built-in | Responsibility |
| `Labels` | Built-in | Workstream/category |
| `Milestone` | Built-in | Planning phase |
| `Repository` | Built-in | Source repository |

Recommended status options:

| Status | Meaning |
| --- | --- |
| `Todo` | Not started |
| `In Progress` | Someone is working on it |
| `Blocked` | Waiting for input or dependency |
| `Review` | Needs review or decision |
| `Done` | Complete |

## Source Data Format

Keep the source data explicit. A CSV or Markdown list should include:

| Column | Required | Example |
| --- | --- | --- |
| `title` | yes | `Confirm event date` |
| `body` | yes | Markdown description |
| `label` | recommended | `Planning` |
| `milestone` | recommended | `4 weeks before` |
| `due_date` | recommended | `2026-06-01` |
| `status` | recommended | `Todo` |
| `assignee` | optional | `username` |

Use ISO dates for scripts:

```text
YYYY-MM-DD
```

Example:

```csv
title,label,milestone,due_date,status
Confirm event date,Planning,4 weeks before,2026-05-30,Todo
Set up ticket shop,Finance,5 weeks before,2026-05-23,In Progress
```

## Issue Body Template

Use a consistent issue body:

```markdown
## Task
Describe what needs to be done.

## Goal
Describe the outcome.

## Open Questions
- Question 1
- Question 2

## Discussion
Add decisions, links, screenshots, offers, and notes here.

## Checklist
- [ ] First step
- [ ] Second step
- [ ] Final confirmation
```

If the repository uses GitHub sub-issues, convert checklist items into sub-issues only once.

## Safe Import Process

Use this process for every mass upload:

1. Prepare source data.
2. Review source data manually.
3. Check whether issues already exist.
4. Create or update labels.
5. Create or update milestones.
6. Create missing issues.
7. Add issues to the Project.
8. Set `Status`.
9. Set `Due Date`.
10. Verify counts and spot-check several issues.

Never skip the duplicate check.

## Duplicate Prevention

Before creating issues, search for existing issues by title.

Recommended matching logic:

- exact title match first
- then normalized title match
- never create a new issue if an open issue with the same title already exists
- if a closed issue exists with the same title, ask before reopening or duplicating

Normalization can include:

- trim whitespace
- collapse repeated spaces
- compare case-insensitively

Do not rely only on issue numbers. Issue numbers change between repositories.

## Safe Update Rules

Mass update scripts should:

- never delete issues
- never force-close issues unless explicitly requested
- never remove comments
- never remove labels unless explicitly requested
- never overwrite human discussion sections
- add machine-managed sections using markers

Recommended machine-managed markers:

```markdown
<!-- managed:start -->
## Imported Planning Metadata

...
<!-- managed:end -->
```

This allows scripts to update one section without destroying manual notes.

## Sub-Issues

Sub-issues are useful when a task has real subtasks that need separate ownership or progress.

Use sub-issues for:

- independent work packages
- tasks with different owners
- tasks that need separate status
- tasks that can be completed independently

Do not use sub-issues for:

- tiny reminders
- notes
- discussion bullets
- one-off comments

Before converting checklists into sub-issues:

- confirm the parent issue is correct
- check whether sub-issues already exist
- create sub-issues only for missing checklist items
- link created sub-issues to the Project
- copy label/milestone from the parent when useful

## Project Views

Views are usually created manually in the GitHub UI.

Recommended views:

| View | Layout | Sort/Group |
| --- | --- | --- |
| `Board` | Board | Group by `Status` |
| `All Tasks` | Table | Sort by `Due Date` ascending |
| `This Week` | Table | Filter by near due dates |
| `Blocked` | Table | Filter `Status: Blocked` |
| `Done` | Table | Filter `Status: Done` |

Keep field names standard where possible:

- `Status`
- `Due Date`

This makes scripts reusable.

## Admin Prompt for Codex

Use this prompt when asking Codex to prepare or run a mass upload:

```text
You are working on a GitHub mass upload for this repository:
[OWNER/REPO]

Project:
[PROJECT URL]

Rules:
- Inspect the repo/project setup first.
- Do not create duplicate issues.
- Do not run an import script until you explain exactly what it will create or update.
- Do not delete issues, labels, milestones, comments, or project fields.
- Do not overwrite human-written discussion.
- Use English Project field names: Status and Due Date.
- Use Status options: Todo, In Progress, Blocked, Review, Done.
- Use ISO dates for Due Date.
- If any field, token permission, or repository target is unclear, stop and ask.

Task:
[describe the upload/update]
```

## Admin Prompt for Updating Existing Issues

```text
Update existing issues only.

Repository:
[OWNER/REPO]

Project:
[PROJECT URL]

Rules:
- Do not create new issues.
- Do not create sub-issues.
- Do not change labels or milestones unless requested.
- Add updates to a clearly marked managed section.
- Preserve manual comments and discussion.
- Set Status and Due Date only when clear.
- Report which issue numbers were changed.

Update data:
[paste notes/data]
```

## Admin Prompt for Creating Sub-Issues

```text
Convert checklists to GitHub sub-issues for selected parent issues only.

Repository:
[OWNER/REPO]

Parent issues:
[#1, #2, #3]

Rules:
- Check whether sub-issues already exist before creating any.
- Create only missing sub-issues.
- Copy relevant labels and milestone from the parent.
- Add created sub-issues to the Project.
- Do not convert every issue unless explicitly requested.
- Report created sub-issue numbers.
```

## Verification After Upload

After any mass upload:

- [ ] Count issues before and after.
- [ ] Check for duplicate titles.
- [ ] Spot-check 5 issue bodies.
- [ ] Check labels.
- [ ] Check milestones.
- [ ] Check Project item count.
- [ ] Check `Status` values.
- [ ] Check `Due Date` values.
- [ ] Check if any private data was accidentally imported.

## Recovery If Something Goes Wrong

If a script goes wrong:

1. Stop running scripts.
2. Save terminal output.
3. Identify the commit or API run time.
4. Identify affected issue numbers.
5. Do not manually delete lots of issues without a plan.
6. Decide whether to:
   - close duplicate issues as `not planned`
   - remove duplicate Project items
   - restore issue bodies from history
   - run a targeted cleanup script

## Recommended Admin Policy

For most teams:

- one person owns mass imports
- one backup admin can run scripts
- everyone else works in the GitHub UI
- tokens are personal and short-lived
- scripts are committed or reviewed before use
- import scripts are not run twice

## Summary

Mass uploads are powerful but easy to misuse.

The safe pattern is:

1. prepare source data
2. check for existing issues
3. create only what is missing
4. update only managed fields/sections
5. verify immediately
6. keep normal contributors in the browser, not in scripts
