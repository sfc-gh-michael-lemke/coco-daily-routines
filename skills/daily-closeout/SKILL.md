---
name: daily-closeout
description: "End-of-day routine for Michael Lemke. Closes resolved Jira tasks, checks urgent emails and Slack messages, checks new people to support, checks Pigment status, previews tomorrow, and writes a closeout note to Obsidian. Use when: end of day, daily closeout, wrapping up, EOD, end-of-day review, close out the day, daily wrap-up, closeout."
---

# Daily Closeout

Run this at the end of each workday to close out, wrap up loose ends, and prep for tomorrow. Produces a `daily-closeout-{date}.md` file in the Obsidian vault.

## Obsidian Vault Path

`/Users/mlemke/Documents/Obsidian Vault/`

## MCP Servers

| Purpose | MCP | Use for |
|---------|-----|---------|
| Jira | `atlassian-snow` | Search, read, and transition issues |
| Email | `gmail-nova` | Search inbox for urgent items |
| Slack | `slack-snow` | Check unread DMs and mentions |
| Calendar | `gcalendar-nova` | Preview tomorrow's meetings |

## Jira Context

- Project: `REVOPS`
- Assignee ID: `712020:c1db006d-719d-48d1-abbf-0b7ce0229717`

## Workflow

### Step 1: Find Tasks to Close

**Actions:**

1. Use the `atlassian-snow` MCP to find open and recently-done tasks:
   - **Open tasks JQL:** `project = REVOPS AND assignee = currentUser() AND statusCategory != Done ORDER BY updated DESC`
   - **Done tasks JQL:** `project = REVOPS AND assignee = currentUser() AND statusCategory = Done ORDER BY updated DESC MAXRESULTS 10`
   - Request fields: `summary`, `status`, `priority`, `updated`

2. Identify candidates for closure from the open tasks:
   - Tasks in "In Progress" with no recent update (potentially done but not transitioned)
   - Any tasks the user mentioned completing during the day

3. Present the candidate list:

```
The following In Progress tasks may be ready to close:

1. [TICKET-ID] — [Title] (Last updated: X days ago)
2. [TICKET-ID] — [Title] (Status: In Progress)

Which would you like to move to Done? (list numbers, "all", or "none")
```

**⚠️ STOP**: Wait for user confirmation before closing anything.

### Step 2: Transition Approved Tasks to Done

If the user approved any closures:

1. For each ticket, use `atlassian-snow` MCP:
   - First call `get_transitions` (or `jira_get_transitions`) for the ticket to get available transition IDs
   - Find the transition that leads to "Done" status
   - Call `transition_issue` (or `jira_transition_issue`) with that transition ID

2. Confirm the transition succeeded (check the returned status or call `get_issue` to verify).

3. Report which tickets were successfully closed.

**Fallback:** If the MCP can't transition (permission error or wrong workflow), note the ticket as "attempted close — manual action needed."

**Output:** List of closed ticket IDs, or "No tasks closed."

### Step 3: Pull Updated Task List

**Actions:**

1. Re-query open tasks using `atlassian-snow` MCP:
   - JQL: `project = REVOPS AND assignee = currentUser() AND statusCategory != Done ORDER BY updated DESC`

2. Categorize:
   - **In Progress** — active work carrying to tomorrow
   - **Selected for Development** — queued and next up
   - **Backlog** — not yet prioritized

**Output:** Updated task list for the closeout note.

### Step 4: Check Urgent Emails

**Actions:**

1. Use the `gmail-nova` MCP to search the inbox:
   - Query: `is:unread newer_than:1d`
   - Second query for time-sensitive items: `is:unread subject:(urgent OR "action required" OR "deadline" OR "ASAP") newer_than:7d`

2. Look for:
   - Unread messages that arrived since this morning
   - Anything requiring action before tomorrow
   - Emails from leadership or key stakeholders

3. List urgent items (sender + subject) — do not retrieve full body unless needed for triage.

**Fallback:** If MCP unavailable, note "Email unavailable — check Gmail manually."

**Output:** List of end-of-day urgent emails or "No urgent emails."

### Step 5: Check Urgent Slack Messages

**Actions:**

1. Use the `slack-snow` MCP to check for unread items:
   - Try `get_unread_messages`, `list_unreads`, or `search_messages` with `is:dm is:unread`
   - Check for `@mentions` in key channels

2. Key channels to check: `specialist-attainment-issues`, `cx-specialists`, `temp-specialist-remediation-task-force`, `1-1-matt-michael`

3. List anything needing a response today.

**Fallback:** If MCP unavailable, note "Slack unavailable — check desktop app."

**Output:** List of urgent Slack items or "No urgent Slack messages."

### Step 6: Check for New People to Support

**⚠️ PLACEHOLDER — Full workflow TBD**

Note in the closeout file: "Check for new people to support — workflow not yet defined."

### Step 7: Check Pigment

**⚠️ PLACEHOLDER — Full workflow TBD**

Note in the closeout file: "Check Pigment status — workflow not yet defined."

### Step 8: Preview Tomorrow

**Actions:**

1. Check for an existing tomorrow kickoff note:
   - Path: `/Users/mlemke/Documents/Obsidian Vault/daily-kickoff-{tomorrow's date}.md`
   - Use the `read` tool — if it exists, summarize key tasks from it

2. From the open task list in Step 3, identify anything due tomorrow or marked high priority.

3. Use the `gcalendar-nova` MCP to get tomorrow's calendar:
   - Call `list_events` (or `gcalendar_list_events`) with `timeMin` = tomorrow 00:00 and `timeMax` = tomorrow 23:59 (ISO 8601, local timezone)
   - Extract: meeting titles, start times, attendees if relevant

4. Summarize: key tasks to pick up + meetings + any follow-ups from today.

**Fallback:** If calendar MCP unavailable, note "Calendar not checked — verify in Google Calendar."

**Output:** Tomorrow section for the closeout note.

### Step 9: Write Closeout Note to Obsidian

**Actions:**

1. Get today's date: `date "+%Y-%m-%d"`
2. Assemble all sections from above
3. Write to: `/Users/mlemke/Documents/Obsidian Vault/daily-closeout-{YYYY-MM-DD}.md`

**Note template:**

```markdown
# Daily Closeout — {YYYY-MM-DD}

## Tasks Closed Today
{list or "None"}

## Open Jira Tasks

### In Progress
{list or "None"}

### Selected for Development
{list or "None"}

### Backlog (notable)
{list or "None"}

## Urgent Emails
{list or "None"}

## Urgent Slack
{list or "None"}

## New People to Support
- [ ] Workflow not yet defined — check manually

## Pigment Status
- [ ] Workflow not yet defined — check manually

## Tomorrow's Outlook

### Key Tasks
{list}

### Meetings
{list from calendar or "Not checked"}

## Notes
{any end-of-day observations, decisions, or follow-ups}
```

**Output:** File written to Obsidian vault. Show user the path and a brief summary.

## Stopping Points

- ✋ Step 1 — MUST wait for user to approve which tasks to close
- ✋ Step 4/5 — Surface truly urgent items immediately if action needed tonight
- ✋ Step 9 — Confirm file path and summary to user

## Output

File at `/Users/mlemke/Documents/Obsidian Vault/daily-closeout-{YYYY-MM-DD}.md`.

## Troubleshooting

**MCP tool name is unknown** — list available tools for the server first. Common Jira names: `search_issues`, `get_transitions`, `transition_issue`. Common Gmail: `search_emails`, `list_emails`. Common Slack: `get_unread_messages`, `search_messages`. Common Calendar: `list_events`, `get_events`.

**MCP returns auth error** — tell the user the MCP needs to be reconnected, skip that step and continue.

**Jira transition fails** — use `get_transitions` first to find the correct transition ID for "Done", then retry `transition_issue`.
