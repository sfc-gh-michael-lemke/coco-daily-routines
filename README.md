# coco-daily-routines

A **Cortex Code (CoCo) plugin** containing two daily workflow skills for Snowflake RevOps / SE leadership: a structured morning kickoff and an end-of-day closeout — both powered by live Jira, Gmail, Slack, and Google Calendar data, with output written to your Obsidian vault.

[![Snowflake RevOps](https://img.shields.io/badge/Snowflake-RevOps-29B5E8?logo=snowflake)](https://www.snowflake.com)
[![CoCo Skill](https://img.shields.io/badge/CoCo-Skill-blue)](https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code)
[![Skills](https://img.shields.io/badge/Skills-2-green)](.cortex-plugin/plugin.json)

---

## What It Does

Two skills that replace a manual morning review and an end-of-day wrap-up with a single CoCo prompt. Each skill pulls live data from your work tools, surfaces what actually needs attention, and produces a structured Markdown note in your Obsidian vault.

**`daily-kickoff`** — Run every morning to orient for the day:
- Pulls open Jira tasks from `REVOPS` and categorizes them by urgency
- Scans Gmail for urgent / unread emails from the last 24 hours
- Checks Slack for unread DMs and `@mentions` in key channels
- Flags new people to support and Pigment status (placeholder steps)
- Writes a structured kickoff note to your Obsidian vault

**`daily-closeout`** — Run at end of day to wrap up and prep for tomorrow:
- Identifies Jira tasks that may be ready to close — pauses for your approval before changing anything
- Transitions approved tasks to Done in Jira
- Scans Gmail and Slack for end-of-day urgent items
- Pulls tomorrow's Google Calendar events
- Writes a structured closeout note to your Obsidian vault

---

## Benefits

**One prompt replaces 15–20 minutes of manual tab-switching.** A typical morning review — opening Jira, scanning Gmail, checking Slack, reviewing the calendar — takes 15–20 minutes across 4–5 apps. Both skills collapse that into a single CoCo prompt and return structured results in seconds.

**Nothing falls through the cracks.** Without a consistent process, urgent Slack messages and follow-up emails get buried in the noise. The kickoff skill scans multiple urgency signals (unread, `@mentions`, subject-line keywords) every morning so high-priority items surface before the day starts.

**Jira stays current without manual overhead.** The closeout skill identifies tasks that are probably done but haven't been transitioned — a common source of stale boards. It presents the candidates and waits for your approval, so transitions are deliberate and the board reflects reality.

**Tomorrow is always prepped.** The closeout routine pulls tomorrow's calendar and flags open tasks with upcoming due dates, so you start each day with context rather than reconstructing it from scratch.

**Consistent, searchable documentation.** Every kickoff and closeout produces a dated Markdown file in Obsidian. Over time this becomes a searchable log of what was prioritized, what was blocked, and what was carried forward — without any manual note-taking.

**Graceful fallbacks.** If any MCP server is unavailable (Jira down, Gmail auth expired), the skill notes the gap and continues with the remaining steps rather than failing silently or stopping entirely.

---

## Value Realization

### For RevOps Program Managers and Leads

- Start every day with a clear categorization of Jira tasks (Needs Action Today / Coming Up / Waiting) rather than eyeballing a flat backlog.
- End every day knowing which tasks were closed, which are carrying forward, and what's on the calendar tomorrow — without manually curating a status email.
- Build a 90-day log of daily priorities and outcomes automatically, useful for retrospectives, performance conversations, and handoffs.

### For SE Leaders and Specialists

- Surface urgent specialist attainment issues and remediation task-force messages from Slack every morning without manually checking channels.
- Close resolved Jira work items at EOD as part of a routine — not as a weekly cleanup — keeping the `REVOPS` board accurate in real time.
- Capture "new people to support" flags in a consistent place so no new team member or stakeholder falls off the radar.

### For Anyone Using Obsidian + CoCo

- Replace ad hoc daily notes with a structured, auto-populated template that is consistent across every working day.
- Search across weeks of kickoff and closeout notes to answer questions like "when did I first flag this issue?" or "what was I focused on the week of X?"
- Use the Obsidian notes as a living audit trail for 1:1s, check-ins, and manager updates — the work is already written up.

---

## When to Use It

| Scenario | Skill |
|----------|-------|
| **First thing in the morning** — orienting before the day starts | `daily-kickoff` |
| **Before a standup or 1:1** — need a quick summary of what's open | `daily-kickoff` |
| **End of workday** — wrapping up and prepping for tomorrow | `daily-closeout` |
| **Before a long meeting block** — want to know if anything urgent came in | `daily-closeout` |
| **Monday morning after a weekend** — catching up on what accumulated | `daily-kickoff` |
| **Before a week off** — closing out tasks and documenting carry-forwards | `daily-closeout` |
| **After a context switch** — returning from travel or a day of back-to-backs | `daily-kickoff` |
| **Weekly review prep** — need the last 5 days of closeout notes as source material | both |

---

## How to Use It

### Morning Kickoff

```
/daily-kickoff
```

CoCo will pull Jira, Gmail, and Slack in sequence, then write a note to:
`~/Documents/Obsidian Vault/daily-kickoff-YYYY-MM-DD.md`

**Output (example):**
```markdown
# Daily Kickoff — 2026-07-02

## Jira Tasks

### Needs Action Today
- REVOPS-412 — Quota alignment review for EMEA AFE (In Progress, due today)
- REVOPS-398 — Specialist attainment issue follow-up (In Progress, 3 days stale)

### Coming Up
- REVOPS-405 — Industry alignment review prep (Selected for Development)

### Waiting / Backlog
- REVOPS-390 — Pigment audit log (Backlog)

## Urgent Emails
- Matt Cain — "Action required: FY27 roster change by EOD"
- Adam Danna — "ASAP: Ludmila org change needs SFDC update"

## Urgent Slack
- #specialist-attainment-issues — @mention from Piotr re: EMEA closeout numbers
- DM from Madhan — follow up on quota model question

## New People to Support
- [ ] Workflow not yet defined — check manually

## Pigment Status
- [ ] Workflow not yet defined — check manually
```

### End-of-Day Closeout

```
/daily-closeout
```

CoCo identifies tasks to close, pauses for your approval, then transitions them and writes a note to:
`~/Documents/Obsidian Vault/daily-closeout-YYYY-MM-DD.md`

**The skill always stops before closing Jira tasks** — it presents the candidates and waits for you to confirm before any transitions happen.

**Output (example):**
```markdown
# Daily Closeout — 2026-07-02

## Tasks Closed Today
- REVOPS-398 — Specialist attainment follow-up → Done
- REVOPS-401 — Industry alignment data pull → Done

## Open Jira Tasks

### In Progress
- REVOPS-412 — Quota alignment review for EMEA AFE

### Selected for Development
- REVOPS-405 — Industry alignment review prep

## Urgent Emails
- No new urgent emails since this morning.

## Urgent Slack
- #cx-specialists — thread from Rishu re: FY27 Q2 deployment numbers

## Tomorrow's Outlook

### Key Tasks
- REVOPS-412 — Quota alignment review (due tomorrow)

### Meetings
- 09:00 — Weekly specialist sync (Zoom, 45 min)
- 14:00 — 1:1 with Matt Cain
```

---

## Quick Start

### Install from GitHub (recommended)

In the CoCo chat panel:

```
Install plugin from GitHub: https://github.com/sfc-gh-michael-lemke/coco-daily-routines
```

CoCo will clone the repo and register both skills automatically.

### Manual install

```bash
git clone https://github.com/sfc-gh-michael-lemke/coco-daily-routines
cp -r coco-daily-routines/skills/daily-kickoff  ~/.snowflake/cortex/skills/
cp -r coco-daily-routines/skills/daily-closeout ~/.snowflake/cortex/skills/
```

Restart CoCo after copying.

### Prerequisites

Make sure these MCP servers are connected in CoCo before running:

| MCP server | Used by | Purpose |
|------------|---------|---------|
| `atlassian-snow` | both | Read and transition Jira issues in `REVOPS` |
| `gmail-nova` | both | Search inbox for urgent/unread email |
| `slack-snow` | both | Check unread DMs and `@mentions` |
| `gcalendar-nova` | closeout only | Preview tomorrow's calendar events |

---

## Jira Configuration

```
Project:     REVOPS
Assignee ID: 712020:c1db006d-719d-48d1-abbf-0b7ce0229717
JQL (open):  project = REVOPS AND assignee = currentUser() AND statusCategory != Done ORDER BY updated DESC
```

Update the `SKILL.md` files if you need a different project or assignee.

---

## Obsidian Vault

Output notes are written to:

```
/Users/mlemke/Documents/Obsidian Vault/
```

Update `## Obsidian Vault Path` in each `SKILL.md` if your vault is in a different location.

---

## Placeholders / Known Gaps

The following workflow steps are marked `⚠️ PLACEHOLDER — Full workflow TBD` in both skills:

- **New People to Support** — logic not yet defined; currently flags in the note for manual review
- **Pigment Status** — logic not yet defined; currently flags in the note for manual review

---

## File Structure

```
coco-daily-routines/
├── README.md                          ← this file
├── .cortex-plugin/
│   └── plugin.json                    ← CoCo plugin manifest
├── skills/
│   ├── daily-kickoff/
│   │   └── SKILL.md                   ← kickoff skill (loaded by CoCo)
│   └── daily-closeout/
│       └── SKILL.md                   ← closeout skill (loaded by CoCo)
```

---

## Contributing / Updating

To update a skill, edit the relevant `SKILL.md` and push:

```bash
git add skills/daily-kickoff/SKILL.md
git commit -m "update: daily-kickoff — add new people to support workflow"
git push
```

Then re-install in CoCo or use the plugin sync button to pull the latest version.

---

## Maintainer

Michael Lemke — RevOps, Snowflake
`michael.lemke@snowflake.com`
