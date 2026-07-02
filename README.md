# coco-daily-routines

A [Cortex Code (CoCo)](https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code) plugin containing two daily workflow skills for RevOps / SE leadership use:

| Skill | Trigger phrases | When to run |
|-------|----------------|-------------|
| `daily-kickoff` | "start my day", "morning kickoff", "daily kickoff" | First thing in the morning |
| `daily-closeout` | "end of day", "daily closeout", "EOD", "wrap up" | End of workday |

---

## Prerequisites

The skills use the following MCP servers. Make sure they are connected in CoCo before running:

| MCP server | Used by | Purpose |
|------------|---------|---------|
| `atlassian-snow` | both | Read and transition Jira issues in the `REVOPS` project |
| `gmail-nova` | both | Search inbox for urgent/unread email |
| `slack-snow` | both | Check unread DMs and channel mentions |
| `gcalendar-nova` | closeout only | Preview tomorrow's calendar |

---

## Installation

### Option A — Install via CoCo GitHub Plugin Installer

In the CoCo chat panel, type:

```
Install plugin from GitHub: https://github.com/sfc-gh-michael-lemke/coco-daily-routines
```

CoCo will clone the repo and register the skills automatically.

### Option B — Manual install

```bash
git clone https://github.com/sfc-gh-michael-lemke/coco-daily-routines
cp -r coco-daily-routines/skills/daily-kickoff  ~/.snowflake/cortex/skills/
cp -r coco-daily-routines/skills/daily-closeout ~/.snowflake/cortex/skills/
```

Restart CoCo after copying.

---

## Skills

### `daily-kickoff`

**Trigger:** "start my day", "daily kickoff", "morning routine"

Runs every morning to orient for the day. Steps:

1. Pull open Jira tasks (`REVOPS` project, assigned to you) and categorize by urgency
2. Scan Gmail for unread/urgent emails from the past 24 hours
3. Check Slack for unread DMs and `@mentions`
4. Note new people to support (placeholder — workflow TBD)
5. Note Pigment status (placeholder — workflow TBD)
6. Write a structured kickoff note to your Obsidian vault at:
   `~/Documents/Obsidian Vault/daily-kickoff-YYYY-MM-DD.md`

**Output file template:** see [`skills/daily-kickoff/SKILL.md`](skills/daily-kickoff/SKILL.md)

---

### `daily-closeout`

**Trigger:** "end of day", "daily closeout", "EOD", "wrap up the day"

Runs at end of workday to wrap up and prep for tomorrow. Steps:

1. Identify Jira tasks that may be ready to close — **pauses for your confirmation before transitioning anything**
2. Transition approved tasks to Done in Jira
3. Re-pull updated open task list
4. Scan Gmail for end-of-day urgent emails
5. Check Slack for unread items needing a response today
6. Note new people to support (placeholder — workflow TBD)
7. Note Pigment status (placeholder — workflow TBD)
8. Preview tomorrow: open tasks + Google Calendar events
9. Write a structured closeout note to your Obsidian vault at:
   `~/Documents/Obsidian Vault/daily-closeout-YYYY-MM-DD.md`

**Output file template:** see [`skills/daily-closeout/SKILL.md`](skills/daily-closeout/SKILL.md)

---

## Jira Configuration

Both skills target the `REVOPS` Jira project with the following defaults:

```
Project:     REVOPS
Assignee ID: 712020:c1db006d-719d-48d1-abbf-0b7ce0229717
JQL (open):  project = REVOPS AND assignee = currentUser() AND statusCategory != Done ORDER BY updated DESC
```

---

## Obsidian Vault

Output notes are written to:

```
/Users/mlemke/Documents/Obsidian Vault/
```

Adjust the path in each `SKILL.md` if your vault is in a different location.

---

## Placeholders / Known Gaps

The following workflow steps are marked `⚠️ PLACEHOLDER — Full workflow TBD` in both skills:

- **New People to Support** — logic not yet defined; currently just flags it in the note
- **Pigment Status** — logic not yet defined; currently just flags it in the note

These will be implemented in future iterations.

---

## Contributing / Updating

To update a skill, edit the relevant `SKILL.md` and push:

```bash
git add skills/daily-kickoff/SKILL.md
git commit -m "update: daily-kickoff — add pigment workflow"
git push
```

Then re-install in CoCo or pull the latest version via the plugin sync button.

---

## License

Internal use — Snowflake employees only.
