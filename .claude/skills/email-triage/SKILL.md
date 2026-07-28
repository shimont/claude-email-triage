---
name: email-triage
description: Triage the personal Gmail inbox (microto@gmail.com) into ready-to-archive and to-review. Use when asked to triage, sort, clean up, or do a pass over personal email.
---

# Personal inbox triage

Sort everything in the inbox into exactly one of three outcomes:
`ready-to-archive`, `to-review`, or **leave alone** (surface it in chat instead).

Never archive, delete, mark as read, or reply. This routine only adds labels.
The archiving decision stays with Shimon.

## Mailbox facts

- The connected account is **microto@gmail.com**, which also receives mail
  addressed to **shimon.tolts@gmail.com** (plus rarer variants: `shimontolts@`,
  `SHIMON.TOLTS@`). Treat all of them as the same person — the address
  something was sent to says nothing about its importance.
- `shimon@copperhelm.com` is the work address; it shows up as a co-recipient on
  family calendar invites.
- Label IDs, not display names, are what the Gmail tools accept:
  - `ready-to-archive` = `Label_69`
  - `to-review` = `Label_70`
  Re-read these with `list_labels` before a run; IDs are stable but the
  mailbox has ~80 labels and the names are easy to mistype.
- A pile of pre-existing `[Superhuman]/AI/*` labels (Pitch, Respond, Marketing,
  Meeting…) are left over from another tool. **They are unreliable** — genuine
  cold-outreach spam sits under `AI/Respond`, and real invoices sit under
  `AI/Marketing`. Ignore them when classifying.

## Procedure

1. `list_labels` to confirm `Label_69` / `Label_70` still resolve.
2. `search_threads` with `query: "in:inbox"`, `pageSize: 50`, paging until
   `nextPageToken` is absent. Roughly 170 threads, so 4 pages.
3. Classify every thread using the rules below.
4. Apply labels with `label_thread` (thread-level, so the whole conversation
   moves together). Batch the calls in parallel.
5. Verify: re-run `list_labels` and compare `threadsTotal` on both labels
   against how many you intended to tag. Do **not** trust
   `in:inbox -label:Label_69` to find stragglers — Gmail matches whole threads,
   so already-labelled threads come back anyway. Instead re-run `in:inbox` and
   read `labelIds` on each returned thread.
6. Report to Shimon: the two counts, the full `to-review` list with a one-line
   reason each, and every thread you deliberately left unlabelled.

## ready-to-archive

Machine-generated mail that is either already-known or freely re-findable by
search. Safe to bulk-tag:

- **Promotions and marketing** — htzone, El Al / matmid, Isrotel, Duty Free,
  Macy's, Glassdoor, AliExpress coupons, Wolt credit offers, Apple/Google
  product pushes, israelclouds, infinify, Tel Aviv municipality newsletters,
  anything tagged `[פרסומת]`.
- **Newsletters and digests** — Substack (whatshot, balajis, …), Crunchbase
  updates and news, Y Combinator / Bookface, AI Tinkerers, AWS re:Action,
  Google Alerts, Linux Foundation and PyTorch conference blasts.
- **Shipping and delivery** — USPS Informed Delivery, USPS tracking, AliExpress
  status.
- **Receipts, invoices, order confirmations** — Wolt, Domino's, Google Play /
  Store, Crunchbase billing, FLYSTORE, clinics, credit-card statement-ready
  notices, donation receipts. Shimon keeps these findable by search; they do
  not need inbox presence.
- **Community platform notifications** — Luma "X registered for your event" and
  "New follower", Meetup "new members joined" / "just scheduled" / "posted in".
  These arrive constantly because he runs several user groups.
- **One-time codes and account notices** — Battle.net, Nintendo, Digitel OTP,
  Netflix new-device, 2FA-enabled confirmations, Google "you allowed <app>
  access" alerts, ToS/user-agreement update mails.

## to-review

Reserve this label. It should stay small enough to clear in one sitting.

- **A real human wrote it, personally, to him.** Family and friends
  (`reutdavid2@gmail.com` is his partner — kids' events, birthdays, births;
  always to-review), forwarded threads from people he knows, Drive shares from
  named individuals.
- **Money, health, or the state, with an action attached** — brokerage
  verification, National Insurance (ביטוח לאומי) letters, IDF reserve orders
  (צו מילואים), anything with a legal or filing deadline.
- **Live threads waiting on his reply** — a vendor quote about to expire, an
  open support ticket where the last message was a question to him, a
  negotiation he is named as leading.
- **Community/program mail that names him and requires a decision** — an
  invitation addressed to him personally, an `[Action Required]` from a program
  he belongs to (AWS Heroes, CNCF Ambassadors), a domain renewal for a domain
  he owns.

Note the last two categories are the ones most likely to drift. If in doubt
about a community mail: does it ask *him specifically* to do something, or is
it a broadcast? Broadcast → archive.

## Leave unlabelled and raise in chat

Do not guess on these. Tag nothing and list them for Shimon:

- **Persistent cold sales outreach that is plausibly a real business contact** —
  repeat follow-ups from a named person at a real company about a real event of
  his. Shimon asked for *investor* mail to be flagged as important, which is
  what makes a wrong call here expensive in both directions: a fake in
  `to-review` teaches him to distrust the label, and a real one archived costs
  him a deal. Describe it and let him decide.
- **Security/permission prompts** where acting matters but the request may be
  unwanted — OAuth app permission escalations, misconfigured domains.
- **Health results or appointments** where "informational" versus "act on this"
  is genuinely ambiguous.

## Calibration log

Adjustments Shimon has confirmed. Append here as the routine is tuned; these
override the general rules above.

**2026-07-28 (first run)**

- **Receipts and invoices: archive all of them, no exceptions.** Wolt, Domino's,
  Google Play, clinic invoices, donation receipts, credit-card
  statement-ready notices, FLYSTORE / El Al points. Confirmed he finds these by
  search and does not want them split by tax or business relevance.
- **Community platform notifications: archive all of them.** Luma "X registered
  for your event" and "New follower", every Meetup notification. He reads
  registration numbers in the Luma dashboard, not by email. This is the largest
  recurring-volume bucket — he did not ask for a count in the report.
- **Fundraising spam: archive it outright, do not surface it.** The tell is a
  lookalike domain (`*foundercap.us` and friends), a subject line that is just
  his first name, a name-dropped famous fund, and near-identical copy arriving
  from two domains in the same minute. Two such threads were archived on his
  instruction after being flagged.
- Cold outreach from a *named person at a real company about a real event of
  his* is still not archived — it stays unlabelled and gets described in chat.
  The live example is `vahid@aetherex.app`, four follow-ups about an
  "ElevenLabs pilot" for his Agentic Cloud Security cocktail party.
