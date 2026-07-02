---
name: daily-kickoff
description: "Morning daily kickoff routine for Michael Lemke. Pulls Jira tasks, checks urgent emails and Slack messages, checks new people to support, checks Pigment status, and writes a kickoff note to Obsidian. Use when: starting the day, morning check-in, daily standup prep, kickoff, start of day, morning routine, daily kickoff."
---

# Daily Kickoff

Run this every morning to orient for the day. Produces a `daily-kickoff-{date}.md` file in the Obsidian vault.

## Obsidian Vault Path

`/Users/mlemke/Documents/Obsidian Vault/`

## MCP Servers

| Purpose | MCP | Use for |
|---------|-----|---------|
| Jira | `atlassian-snow` | Search and read issues |
| Email | `gmail-nova` | Search inbox for urgent items |
| Slack | `slack-snow` | Check unread DMs and mentions |

## Jira Context

- Project: `REVOPS`
- Assignee ID: `712020:c1db006d-719d-48d1-abbf-0b7ce0229717`
- JQL for open tasks: `project = REVOPS AND assignee = currentUser() AND statusCategory != Done ORDER BY updated DESC`

## Workflow

### Step 1: Pull Jira Tasks

**Actions:**

1. Use the `atlassian-snow` MCP to search for issues:
   - JQL: `project = REVOPS AND assignee = currentUser() AND statusCategory != Done ORDER BY updated DESC`
   - If the MCP has a `search_issues` or `jira_search` tool, call it with that JQL
   - Request fields: `summary`, `status`, `priority`, `duedate`, `updated`

2. Categorize results:
   - **Needs Action Today** — status is "In Progress", overdue, or high priority
   - **Coming Up** — status is "Selected for Development" or "To Do"
   - **Waiting / Backlog** — status is "Backlog" or blocked

**Fallback:** If the MCP is unavailable, note "Jira unavailable — check manually" and continue.

**Output:** Structured task list with status and priority.

### Step 2: Check Urgent Emails

**Actions:**

1. Use the `gmail-nova` MCP to search the inbox:
   - Query: `is:unread newer_than:1d`
   - Also run a second query for high-signal items: `is:unread subject:(urgent OR "action required" OR "ASAP" OR "by EOD") newer_than:7d`

2. Scan results for:
   - Unread messages from the last 24 hours
   - Subject lines with urgency signals: "urgent", "action required", "ASAP", "today", "by EOD", "deadline"
   - Emails from leadership, direct team members, or key stakeholders

3. List urgent items with sender and subject only — do not retrieve full email body unless needed for triage.

**Fallback:** If MCP is unavailable, note "Email unavailable — check Gmail manually."

**Output:** List of urgent emails (sender + subject) or "No urgent emails."

### Step 3: Check Urgent Slack Messages

**Actions:**

1. Use the `slack-snow` MCP to check for unread messages:
   - Try `get_unread_messages`, `list_unreads`, or equivalent tool
   - Also try `search_messages` with query `is:dm is:unread` to find unread DMs

2. Look for:
   - Unread DMs from the last 12–24 hours
   - Any `@mentions` or `@here` / `@channel` notifications
   - Messages in key channels: `specialist-attainment-issues`, `cx-specialists`, `temp-specialist-remediation-task-force`

3. List urgent items (sender/channel + message preview).

**Fallback:** If MCP is unavailable, note "Slack unavailable — check desktop app."

**Output:** List of urgent Slack items or "No urgent Slack messages."

### Step 4: Check for New People to Support

**⚠️ PLACEHOLDER — Full workflow TBD**

Note in the kickoff file: "Check for new people to support — workflow not yet defined."

### Step 5: Check Pigment

**⚠️ PLACEHOLDER — Full workflow TBD**

Note in the kickoff file: "Check Pigment status — workflow not yet defined."

### Step 6: Write Kickoff Note to Obsidian

**Actions:**

1. Get today's date in `YYYY-MM-DD` format via bash: `date "+%Y-%m-%d"`
2. Assemble the note from all steps above
3. Write to: `/Users/mlemke/Documents/Obsidian Vault/daily-kickoff-{YYYY-MM-DD}.md`

**Note template:**

```markdown
# Daily Kickoff — {YYYY-MM-DD}

## Jira Tasks

### Needs Action Today
{list or "None"}

### Coming Up
{list or "None"}

### Waiting / Backlog
{list or "None"}

## Urgent Emails
{list or "None"}

## Urgent Slack
{list or "None"}

## New People to Support
- [ ] Workflow not yet defined — check manually

## Pigment Status
- [ ] Workflow not yet defined — check manually

## Notes
{any additional observations}
```

**Output:** File written to Obsidian vault. Show the user the path and a brief summary.

## Stopping Points

- ✋ After Step 3 — if anything is truly urgent, surface it immediately before writing the note
- ✋ After Step 6 — confirm path and summary to user

## Output

File at `/Users/mlemke/Documents/Obsidian Vault/daily-kickoff-{YYYY-MM-DD}.md`.

## Troubleshooting

**MCP tool name is unknown** — try listing available tools for the MCP server first, then call the most relevant one. Common Jira tool names: `search_issues`, `jira_search`, `get_issues`. Common Gmail names: `search_emails`, `list_emails`, `gmail_search`. Common Slack names: `get_unread_messages`, `list_unreads`, `search_messages`.

**MCP returns auth error** — tell the user the MCP needs to be reconnected, continue with remaining steps.

**No results from Jira MCP** — verify JQL by simplifying to `project = REVOPS ORDER BY updated DESC` and check if results come back.
