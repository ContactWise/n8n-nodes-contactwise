---
name: project-update
description: Draft and post a Linear project update (status update with health) for the n8n-nodes-contactwise project. Reads milestones, issues and the previous update, shows the draft, and posts only after the owner approves. Use when asked for a project update, status update, weekly update, or "where are we" to be posted in Linear.
argument-hint: "[optional focus or note to include]"
allowed-tools: mcp__claude_ai_Linear__get_project, mcp__claude_ai_Linear__list_milestones, mcp__claude_ai_Linear__list_issues, mcp__claude_ai_Linear__get_issue, mcp__claude_ai_Linear__list_comments, mcp__claude_ai_Linear__get_status_updates, mcp__claude_ai_Linear__save_status_update, Bash(git log *), Bash(gh run list *), Bash(gh pr list *), Bash(npm view *)
---

# Project update

Posts a status update to the Linear project **n8n-nodes-contactwise** (team `T-Integrations`). The conventions are in `docs/linear-conventions.md`. If the Linear tools are deferred, load them with ToolSearch first.

## 1. Gather

Run these in parallel:

- `get_status_updates` with type `project` and project `n8n-nodes-contactwise`, `limit: 1`. The **previous update's `createdAt` is the reporting window's start**. If there's no previous update, use the last 7 days.
- `list_milestones` for the project: names, target dates, progress.
- `list_issues` for the project with fields `id, title, status, statusType, priority, projectMilestone, parentId, completedAt, updatedAt`, `limit: 250`.
- Repo signals (read-only):
  - `git log --since=<window start> --oneline main`
  - `gh pr list --state all --limit 20`
  - `gh run list --limit 5` (CI health)
  - `npm view @contactwise/n8n-nodes-contactwise version` (a 404 means not published yet)

For issues that are In Progress, In Review or blocked, read their latest comments (`list_comments`) to pick up progress and blockers. Don't read every issue.

## 2. Work out

- **Done in the window:** issues with `completedAt` after the window start.
- **In flight:** issues in `In Progress` or `In Review`.
- **Blocked:** open issues with a `blockedBy` link to an open issue, plus blockers named in recent `## Progress` comments. Check with `get_issue` `includeRelations: true` for the current milestone's open issues only.
- **Per milestone:** progress %, target date, open count; say whether it's on track for the target date.
- **Health:** pick one and give the reason in one sentence.
  - `onTrack`: the current milestone's open work fits before its target date, with no open Urgent blockers.
  - `atRisk`: an Urgent or High blocker, or a missed step that the date depends on, but it's recoverable.
  - `offTrack`: the target date will be missed, or a customer is blocked with no workaround.

## 3. Draft

Keep it short: someone should be able to read it in a minute. Link issue IDs (TIN-n) and don't paste their descriptions.

```markdown
**Health: <On track | At risk | Off track>**: <one-sentence reason>

## Since last update (<window start date> → today)
- <shipped outcome, TIN-n> …

## In progress
- <TIN-n, what's happening, next step>

## Blocked / risks
- <TIN-n, blocked by what or who, what unblocks it>

## Milestones
| Milestone | Progress | Target | Status |
|---|---|---|---|
| M1 · … | 80% | 2026-09-23 | On track |

## Next
- <the 3–5 most important next steps, in order>
```

If the user gave an argument (`$ARGUMENTS`), work it into the draft where it fits.

## 4. Confirm, then post

1. Show the draft and the chosen health. Ask: **"Post this update?"** The owner can approve it, edit it, or change the health.
2. **Never post without explicit approval.** A project update is visible to the whole workspace.
3. On approval, call `save_status_update` with `type: "project"`, `project: "n8n-nodes-contactwise"`, `health`, and `body` (the approved markdown, without the "**Health:**" line, since Linear shows health separately).
4. Reply with the update's link and nothing else.
