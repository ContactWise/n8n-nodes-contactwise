# Linear conventions

How issues are managed in the Linear project **n8n-nodes-contactwise** (team `T-Integrations`, key `TIN`). The project has one owner, who is a solo maintainer, and AI agents do much of the work, so these rules are strict on purpose. Agree on any exception with the owner first.

## Every issue has

| Field | Rule |
|---|---|
| Assignee | The owner (Lucky). Always, including API-team reference issues. Reassign when someone else picks one up. |
| Priority | Always set when the issue is created; never "No priority". See the scale below. |
| Labels | Exactly **one type label**: `Feature` (new capability), `Improvement` (change to something that exists, including docs, tests, CI and process) or `Bug` (defect). Add **1–2 area labels**, e.g. `ci`, `infra`, `documentation`, `testing`, `security`, `auth`, `ux`, `ops`, `integration`, `foundation`, `audit`, `ratelimit`, `open-question`. |
| Milestone | Always. Unplanned work goes in **Backlog · Unscheduled**. Sub-issues share their parent's milestone. |
| Relations | **Blocked by / Blocks** only for hard dependencies: the work can't start or can't finish without the other issue. **Related** for "see also". Epics group their work as parent and sub-issues, not with blocking links. |
| Title | Starts with a verb and says what changes ("Update repo URLs in the code…"). API-team reference issues start with `API team:`. |

### Priority scale

| Priority | Use when |
|---|---|
| Urgent | It blocks the current milestone's target date or a customer. |
| High | The current milestone needs it. |
| Medium | A later milestone needs it. |
| Low | Nice to have, or unscheduled. |

API-team reference issues (under TIN-20) take the priority of the node work they block, so the API team can see what matters most to us.

### Milestones

| Milestone | Contents |
|---|---|
| M1 · SMS on npm for self-hosted n8n | The SMS node published to npm for self-hosted customers |
| M2 · n8n verification (n8n Cloud) | Provenance, trusted publishing, the n8n scan, the Creator Portal submission |
| M3 · WhatsApp nodes | The WhatsApp action and trigger nodes (epic TIN-30) |
| Backlog · Unscheduled | API-team reference issues (under TIN-20), the SMS Trigger, Phase 2 dropdowns, anything not yet planned |

## Description vs. comments

The **description is the spec**: the problem, requirements, scope and "done when" checklist. It must stay true.

- If scope changes, edit the description **and** add a comment that says what changed and why.
- Everything else goes in **comments**, each starting with one of these headings:

| Heading | Holds |
|---|---|
| `## Design` | The approach, alternatives considered, the interfaces |
| `## Decision` | What was decided, why, and the date |
| `## Progress` | What's done, what's next, blockers, the date |
| `## Implementation` | The PR link, what changed, the `verify` result, anything left for later |

**Epics** are the exception. An epic's description is the requirements document for its sub-issues, so it may keep a decision log and a context snapshot. Its type label is the main type of its sub-issues.

An `## Implementation` comment is **required before an issue is marked Done**. Issues that are closed without code (ops, research, API-team) use it to record the outcome instead. **Canceled** and **Duplicate** always get a comment with the reason.

## Status flow

`Backlog` → `Todo` (planned next) → `In Progress` (branch created) → `In Review` (PR open) → `Done` (merged, with the `## Implementation` comment).

## When an agent creates or changes issues

1. Set every field in the table above in the same call that creates the issue.
2. For an epic, create the epic first, then the sub-issues, then add blocking links once all the IDs exist. Finally, update the epic's sub-issue table.
3. **Read the issue back afterwards** (`get_issue` with relations) and check that the milestone, labels and relations were saved.
4. If a Linear call errors, list the issues before retrying, so a partial create doesn't leave a duplicate.

## Project updates

Post project updates with `/project-update`. It drafts the update from the milestones and issues, shows the draft, and only posts after the owner approves it.
