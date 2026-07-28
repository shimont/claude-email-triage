# claude-email-triage

A repeatable routine for triaging Shimon's personal Gmail inbox
(microto@gmail.com, which also collects shimon.tolts@gmail.com).

## What it does

Every thread in the inbox gets sorted into one of three outcomes:

| Outcome | Meaning |
| --- | --- |
| `ready-to-archive` | Machine-generated, already-known, or re-findable by search. Safe to clear in bulk. |
| `to-review` | A real person wrote it, or money/health/the state wants something, or a live thread is waiting on a reply. |
| *(unlabelled)* | Judgement call. Left in place and raised in chat rather than guessed at. |

The routine **only adds labels**. It never archives, deletes, marks as read, or
replies — the actual archiving stays a human decision.

## Running it

The routine lives in [`.claude/skills/email-triage/SKILL.md`](.claude/skills/email-triage/SKILL.md).
Ask Claude to triage the personal inbox, or invoke `/email-triage`.

## Tuning it

The classification rules are deliberately explicit so they can be argued with.
When a call comes out wrong, record the correction in the **Calibration log**
at the bottom of the skill file — that section overrides the general rules, so
the routine gets more accurate with each pass instead of relearning the same
mistakes.

## Gotchas worth knowing

- Gmail search matches **whole threads**, so `in:inbox -label:X` still returns
  threads that already carry label X. Verify by reading `labelIds` on the
  results of a plain `in:inbox` query instead.
- The Gmail tools take label **IDs**, not display names.
- The mailbox carries stale `[Superhuman]/AI/*` labels from a previous tool.
  They misclassify often enough to be worse than no signal; the routine ignores
  them.
