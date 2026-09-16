# claude-email-triage

A repeatable routine for triaging Shimon's personal Gmail inbox
(microto@gmail.com, which also collects shimon.tolts@gmail.com).

## What it does

Every thread in the inbox gets sorted into one of three outcomes:

| Outcome | Meaning |
| --- | --- |
| `ready-to-archive` | Machine-generated, already-known, or re-findable by search. Safe to clear in bulk. |
| `to-review` | A real person wrote it, or money/health/the state wants something, or a live thread is waiting on a reply. |
| *(unlabelled)* | Judgement call. Left in place and described in the run report rather than guessed at. |

The routine **only adds labels**. It never archives, deletes, marks as read,
replies, or forwards. The one label it may remove is `to-review` on a thread
the staleness sweep has shown to be finished (event passed, opt-in deadline
passed, closed by a later message, or reclassified under a newer rule), and
it says so in the report. The actual archiving stays a human decision.

## Running it

The rules live in [`.claude/skills/email-triage/SKILL.md`](.claude/skills/email-triage/SKILL.md).

- **On a schedule:** a Claude Code Routine named "Personal email triage"
  fires daily at 07:00 Israel time (04:00 UTC; it drifts to 06:00 local when
  daylight saving ends in late October). It fires *into the Claude Code
  session that created it*, because that session holds the Gmail and Google
  Calendar connectors and a checkout of this branch. Routines created from
  inside a session cannot store connectors of their own, so a
  fresh-session-per-run Routine made this way would have no Gmail access; if
  a fresh session per run is wanted, create the Routine from the claude.ai
  Routines UI, where the Gmail connector can be attached to it, and give it
  the same prompt (it is recorded in `list_triggers`). Each run pulls this
  branch, reads `SKILL.md`, and follows it. The run report is that session's
  reply. `list_triggers` from any Claude Code session shows the Routine and
  its last run.
- **By hand:** in a session with the Gmail connector attached, ask Claude to
  triage the personal inbox, or invoke `/email-triage`.

The Routine's prompt is short on purpose: it points at `SKILL.md` instead of
duplicating it, so editing the file and pushing is the only step needed to
change the routine's behaviour. If the skill moves to another branch, update
the branch name in the Routine's prompt (`update_trigger`).

## Tuning it

The classification rules are deliberately explicit so they can be argued with.
When a call comes out wrong, record the correction in the **Calibration log**
at the bottom of the skill file. That section overrides the general rules, so
the routine gets more accurate with each pass instead of relearning the same
mistakes.

Entries in the log are marked as either *confirmed in chat* or *inferred from
behaviour*. The 2026-09-16 review added a block of inferred rules (what got
opened, replied to, forwarded, trashed, or ignored over the previous seven
weeks); any of them can be struck out by saying so.

## Gotchas worth knowing

- Gmail search matches **whole threads**, so `in:inbox -label:X` still returns
  threads that already carry label X. Verify by reading `labelIds` on the
  results of a plain `in:inbox` query instead.
- The Gmail tools take label **IDs**, not display names (`ready-to-archive` is
  `Label_69`, `to-review` is `Label_70`).
- The mailbox carries stale `[Superhuman]/AI/*` labels from a previous tool
  and an abandoned `Urgent` / `Needs reply soon` / `FYI` taxonomy from
  mid-2026. Both misclassify often enough to be worse than no signal; the
  routine ignores them.
- The Google Calendar connector signs in as microto, but `list_calendars`
  also exposes the shared `shimon.tolts@gmail.com` calendar, which is where
  Reut's invitations land. The skill reads Shimon's `responseStatus` there to
  tell answered invitations from unanswered ones. The Routine only gets that
  check if "Google Calendar" is among its connectors; with Gmail alone it
  treats every invitation as to-review, as before.
