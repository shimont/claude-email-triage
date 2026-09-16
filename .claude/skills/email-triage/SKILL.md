---
name: email-triage
description: Triage the personal Gmail inbox (microto@gmail.com) into ready-to-archive and to-review. Use when asked to triage, sort, clean up, or do a pass over personal email.
---

# Personal inbox triage

Sort every inbox thread into exactly one of three outcomes:
`ready-to-archive`, `to-review`, or **leave alone** (surface it in the report
instead).

Never archive, delete, mark as read, reply, or forward. The routine adds
labels. The one label it may ever remove is `to-review`, and only on a thread
it is moving to `ready-to-archive` under the staleness rule below, with the
move listed in the report. The archiving decision stays with Shimon.

## Mailbox facts

- The connected account is **microto@gmail.com**, which also receives mail
  addressed to **shimon.tolts@gmail.com** (plus rarer variants: `shimontolts@`,
  `SHIMON.TOLTS@`). Treat all of them as the same person. The address
  something was sent to says nothing about its importance.
- `shimon@copperhelm.com` is the work address (Copperhelm, his current
  company). It shows up as a co-recipient on family calendar invites, and he
  forwards program logistics there from this mailbox.
- Other identities that appear in security alerts and threads:
  `shimon@microbright.dev` / מיקרוברייט בע"מ (Micro Bright, the company that
  pays for his community events), `shimon@datree.io` (Datree, dissolved in
  2025-26), `shimont@hatal.org.il` (Hatal, an organisation whose Google
  Workspace he administers).
- Label IDs, not display names, are what the Gmail tools accept:
  - `ready-to-archive` = `Label_69`
  - `to-review` = `Label_70`
  Re-read these with `list_labels` before a run; IDs are stable but the
  mailbox has ~80 labels and the names are easy to mistype.
- Two stale label families exist and are **unreliable**. Ignore both when
  classifying:
  - `[Superhuman]/AI/*` (Pitch, Respond, Marketing, Meeting, News, Social…)
    from a previous tool. Genuine cold-outreach spam sits under `AI/Respond`,
    real invoices under `AI/Marketing`.
  - An abandoned June–July 2026 taxonomy: `Needs reply soon`, `Urgent`,
    `Waiting`, `FYI`, `Action`, `Programs/*`, `Reading/*`, `Events/*`,
    `Muted/*`. It marked Nintendo sign-in alerts as `Urgent`. Nothing has been
    filed under it since 2026-07-17.
- If a Google Calendar connector is attached, it is the **microto** calendar.
  Reut's invitations are addressed to shimon.tolts@gmail.com and live on that
  account's calendar, which the connector cannot see. Do not try to verify
  whether an invitation was accepted; classify from the mail alone.
- Gmail search matches **whole threads**. `in:inbox -label:Label_69` still
  returns threads that already carry the label. Read `labelIds` on each
  message instead of trusting a negated query.

## People and organisations

| Address / domain | Who | Default |
| --- | --- | --- |
| `reutdavid2@gmail.com` | Reut David, his partner. Sends calendar invites for the kids (ליה, אריאל), forwards insurance and shop mail. | always to-review |
| `mtolts@gmail.com` | Mark Tolts, his father (Hebrew University). | always to-review |
| Oleg Tolts (Google Photos shares) | family | to-review |
| `zohara55@gmail.com` | the צהרון (after-school) coordinator, mass-mails all parents | to-review when it names money or dates |
| `david@baitov-gda.com` | the landlord's office (Bait Tov, שיינקין 30) | to-review |
| `reutd@copperhelm.com` | Reut Doron, finance and admin at Copperhelm. Shimon forwards invoices and debt notices to her for payment. | to-review when she writes to him; a thread where she is handling a vendor and he is only cc'd is to-review only if the last message asks him something |
| `tlv.partners`, `blumbergcapital.com`, `portfolio@angellist.com` | investors (TLV Partners at Copperhelm; Blumberg at Datree) | always to-review, never archive |
| `made.co.il`, `herzoglaw.co.il` | Datree's outsourced finance (Nofar Zaddik, Rivka Zeltser) and lawyers, still winding the company down | to-review |
| `anthropic.com` (chiara@, cmayes@, community@), `contracts@anthropic.com` via DocuSign | Claude Ambassador program, joined 2026-09-14 | personal mail to-review; `community@` broadcasts archive |
| `aws-heroes@amazon.com`, `awsheroes@its.com`, `hello-aws-emea-community@amazon.com`, `communities@builder.aws.com` | AWS Heroes program and its travel agency | see the program rule below |
| `lists.cncf.io`, `no-reply@cncf.io` | CNCF Ambassadors list and CNCF marketing | archive |
| `freemasonry.org.il` (g-treas, g-sec, gl-Israel), `elia@bencnaan.net` | a lodge website project he leads | to-review when addressed to him |
| `sales@software-sources.com`, `hatal.org.il` | Google Workspace renewal quotes he brokers for Hatal | to-review while a quote is open |
| `btl.gov.il`, `miluim.idf.il`, `taxes.gov.il`, `pniyotNoReply@mail.tel-aviv.gov.il` | the state: National Insurance, reserve orders, tax authority, municipality replies to his own inquiries | always to-review |
| `infodigitel@tel-aviv.gov.il` | municipality newsletter | archive |

## Procedure

1. `list_labels` to confirm `Label_69` / `Label_70` still resolve.
2. `search_threads` with `query: "in:inbox"`, `pageSize: 50`, paging until
   `nextPageToken` is absent. The inbox runs 80–200 threads, so 2–4 pages.
3. Partition by the `labelIds` you read on each thread's messages:
   - **Already `ready-to-archive`** (`Label_69` on any message): skip.
   - **Already `to-review`** (`Label_70`): run the staleness sweep below.
   - **Neither**: classify with the rules below. This is the main work and on
     a daily run it is usually 10–30 threads.
4. Apply labels with `label_thread` (thread-level, so the whole conversation
   moves together). Batch the calls in parallel. For a staleness move, call
   `label_thread` with `Label_69` and then `unlabel_thread` with `Label_70`.
5. Verify: re-run `in:inbox` and read `labelIds` on every returned thread.
   Every thread must now carry exactly one of the two labels or appear in the
   report's leave-alone list. Do not trust `in:inbox -label:…`.
6. Report in the format at the end of this file.

## ready-to-archive

Machine-generated mail that is either already known or freely re-findable by
search. Safe to bulk-tag:

- **Promotions and marketing**: htzone (daily, `[פרסומת]`), El Al / matmid,
  Isrotel, Duty Free, Macy's, Ticketmaster, Glassdoor, AliExpress coupons,
  Wolt credit offers, Apple / Google / Claude / OpenAI product pushes,
  israelclouds, infinify, udifish, arosport, Supercell, Tel Aviv municipality
  newsletters, Linux Foundation / CNCF / KubeCon conference blasts, anything
  tagged `[פרסומת]` or `- פרסומת`.
- **Newsletters and digests**: Substack (whatshot, balajis, on+stories),
  Crunchbase updates and news, Y Combinator / Bookface, AI Tinkerers,
  AWS re:Action, Angular Ventures "The Angle", Google Alerts.
- **Mailing lists**: `devel@lists.centos.org` (he sent an unsubscribe on
  2026-07-18 that did not take), `cncf-ambassadors@lists.cncf.io`.
- **Shipping and delivery**: USPS Informed Delivery and tracking, AliExpress
  / Cainiao status, Parcel Home pickup notices.
- **Receipts and paid invoices**: Wolt, Domino's, Google Play / Store, Google
  Fi statements, Anthropic / OpenAI subscription receipts, Crunchbase billing,
  FLYSTORE, clinics, amisragas periodic invoices (חשבונית), credit-card
  "statement ready" notices (Amex, isracard, cal), Interactive Brokers monthly
  activity statements, donation receipts and donor updates (kohelet). Shimon
  finds these by search; they do not need inbox presence.
- **Community platform notifications**: Luma "X registered", "New follower",
  "Guest update"; every Meetup notification ("new members", "posted in",
  "just scheduled"). He runs several user groups and reads the numbers in the
  Luma dashboard.
- **One-time codes, sign-in and device notices**: Battle.net, Nintendo,
  Discord, Instagram, Authy, 1Password sign-in, Link, Digitel OTP, Netflix
  new-device, Google "you allowed <app> access" and "Security alert for
  <account>" copies (these arrive twice, once per account), Google Fi device
  setup, Google Play settings changes, Maps Timeline, 2FA-enabled
  confirmations, ToS / privacy-policy updates.
- **Recurring nags he has never acted on**: Vercel "1 domain needs
  configuration" (monthly since March 2026), AWS Free Tier 85% alerts (the
  1st and 15th of every month), AWS Health `[Notification]` and
  `[Action may be required]` service notices, AWS Certificate Manager
  renewals, Route 53 "renewing automatically" and "auto renewal succeeded".
- **Brokerage routine mail**: Interactive Brokers "Message Center
  Notification" (every one so far has been a dividend or corporate-action
  notice for SPY) and "Monthly Activity Statement". Only the mail listed
  under to-review below is different.
- **Calendar plumbing**: `calendar-notification@google.com` "Notification:"
  and "Reminder:" mails (the event is already on his calendar), "Accepted:" /
  "Declined:" replies, and invitations he sent himself from
  `shimon@copperhelm.com`.
- **Program broadcasts**: AWS EMEA town halls and round-ups, "AWS Community
  Day Sponsorship how-to", CNCF ambassador list traffic, "Claude Community
  Roundup", AWS Heroes program surveys. These go to every member. Archive
  them, but give each a one-line summary in the report's *skimmed* section.
- **Recruiter and job-board mail**: hunted.co.il and similar postings.
- **Expert-network solicitations**: Third Bridge, AlphaSights, GLG,
  Guidepoint, Coleman, Dialectica "paid expertise request" mails, including
  every follow-up. He has never answered one; in 2018 he told Third Bridge
  not to contact him again.
- **Fundraising and M&A spam**: lookalike domains (`*foundercap.us` and
  friends), a subject line that is just his first name, a name-dropped famous
  fund, near-identical copy from two domains in the same minute, and "offer
  to purchase" or "PE firm interested in acquiring Stealth" mail. "Stealth" is
  the placeholder his LinkedIn shows, which proves the sender never looked.
- **Misdirected debt collection**: `payment@amisragas.co.il` "הודעה חשובה
  מאמישראגז" addressed to קטורזה בנימין. Shimon trashes every one of these;
  the routine cannot trash, so tag it and do not surface it.
- **Odd old accounts**: Banco del Pacífico (Ecuador) card renewals, YouTube TV
  channel updates, Airbnb / Descript "log in to keep your account" (see
  to-review for the exception).

## to-review

Reserve this label. It should stay small enough to clear in one sitting.

- **A real human wrote it, personally, to him.** Family and friends (see the
  table), forwarded threads from people he knows, Drive and Photos shares from
  named individuals, a vendor or organiser writing about one of his events
  (e.g. the automat-it event manager about the AWS Community IL party).
- **Reut's calendar invitations** ("Invitation:" from `reutdavid2@gmail.com`).
  Always. Put the event date in the report so he can accept from his phone.
- **Money, health, or the state, with an action attached**: brokerage
  identity verification ("Please Verify Info", login or funding problems),
  National Insurance letters, IDF reserve orders (צו מילואים), tax-authority
  identity checks, municipality replies to his inquiries, pension-fund debt
  notices (מיטב "הודעה על חוב"), chargeback inquiries (isracard),
  anything with a legal or filing deadline.
- **Payment requests and failed payments**: "חשבון עסקה" / "קיבלת חשבון
  עסקה" (a request for payment, not a receipt; he forwards these to Reut
  Doron), "payment failed", "card declined", "order cancelled due to failed
  payment" (1Password, Canva, Stripe, Amazon, Lime). Note in the report
  whether it looks like a Micro Bright expense he would forward.
- **Live threads waiting on his reply**: a vendor quote about to expire, an
  open support ticket where the last message was a question to him, a
  negotiation he is named as leading, a GitHub org join request he must
  approve.
- **Program mail that names him and requires a decision**: an invitation
  addressed to him personally (a named person, "Hello Shimon", a seat or slot
  held for him), an `[Action Required]` or `[Action needed by <date>]` from a
  program he belongs to (AWS Heroes, CNCF Ambassadors, Claude Ambassadors), a
  DocuSign to sign, a credit or gift code to redeem, a domain contact-info
  confirmation for a domain he owns (`[Action May Be Required]` from
  Route 53), travel-agency threads for a trip.
- **Account-deletion warnings with a date**: "log in within 30 days to keep
  your account" (Airbnb), "scheduled for deletion on <date>" (Descript). He
  may want the account; the date makes it a decision.

If in doubt about program or community mail: does it ask *him specifically* to
do something by a date, or is it a broadcast? Broadcast → archive with a
one-line summary. Personal ask → to-review.

## Staleness sweep (threads already labelled to-review)

`to-review` was accumulating: at the 2026-09-16 review 34 threads carried it
in the inbox and roughly half were finished. For each thread that already has
`Label_70`, move it to `ready-to-archive` **only** when one of these is true
and say which in the report:

- The event it was about is in the past (a calendar invitation or reminder
  whose date has passed, a party or meeting that already happened).
- The deadline it named has passed (`[Action needed by August 25th]` on
  September 16).
- A later message in the mailbox closes it: DocuSign "Completed", a
  "payment succeeded" after a "payment failed", his own reply that ends the
  exchange ("It was a user error on my side, it works now"), a "Declined:" or
  "Accepted:" calendar reply.

Anything else stays `to-review` even if it is weeks old. Never move a thread
from a human, an investor, or the state on age alone.

## Leave unlabelled and raise in the report

Do not guess on these. Tag nothing and describe them:

- **Persistent cold sales outreach from a named person at a real company
  about a real event of his** (the live example was `vahid@aetherex.app`,
  four follow-ups about an "ElevenLabs pilot" for his cocktail party).
  Shimon asked for *investor* mail to be flagged, which is what makes a
  wrong call here expensive in both directions.
- **Security or permission prompts** where acting matters but the request may
  be unwanted: OAuth escalations he did not initiate, a "misconfigured
  domain" for a domain he did not know he had.
- **Health results or appointments** where "informational" versus "act on
  this" is genuinely ambiguous.

Raise each of these **once**. On later runs, list it under "still
unlabelled" with the date it was first raised, without re-describing it. If it
is still unlabelled and unread 14 days after it was first raised and he has
not replied to it, tag it `ready-to-archive` and say so: his silence is the
decision. (The four aetherex follow-ups sat unlabelled and unread from July to
September; that is the case this rule is for.)

## Report format

Keep it short. Sections, in this order, each omitted when empty:

1. **To review** (new this run): sender, subject, one line on what he has to
   do and any date. Family invites first, then money and the state, then the
   rest.
2. **Moved out of to-review**: subject and the staleness reason.
3. **Left unlabelled**: new ones described, older ones as one line with the
   first-raised date.
4. **Skimmed for you**: one line each for program broadcasts and community
   newsletters that were archived (what the AWS town hall was about, what the
   Claude roundup announced).
5. **Counts**: threads seen, tagged archive, tagged review, moved, left.
6. **Noise worth a filter** (only when a sender crossed 5 threads in the last
   7 days, or a sender he unsubscribed from is still arriving): sender and
   count. Known repeat offenders: Crunchbase (arrives twice a day, once per
   address), htzone (unsubscribed 2026-07-18, still daily), CentOS-devel
   (unsubscribed 2026-07-18, still arriving), Google security-alert copies.

## Calibration log

Adjustments Shimon has confirmed, and (marked) adjustments inferred from how he
actually handled mail. These override the general rules above. Confirmed
entries win over inferred ones.

**2026-07-28 (first run, confirmed in chat)**

- **Receipts and invoices: archive all of them, no exceptions.** Wolt,
  Domino's, Google Play, clinic invoices, donation receipts, credit-card
  statement-ready notices, FLYSTORE / El Al points. Confirmed he finds these
  by search and does not want them split by tax or business relevance.
- **Community platform notifications: archive all of them.** Luma "X
  registered for your event" and "New follower", every Meetup notification.
  He reads registration numbers in the Luma dashboard, not by email. This is
  the largest recurring-volume bucket; he did not ask for a count in the
  report.
- **Fundraising spam: archive it outright, do not surface it.** The tell is a
  lookalike domain (`*foundercap.us` and friends), a subject line that is just
  his first name, a name-dropped famous fund, and near-identical copy arriving
  from two domains in the same minute. Two such threads were archived on his
  instruction after being flagged.
- Cold outreach from a *named person at a real company about a real event of
  his* is still not archived; it stays unlabelled and gets described. The
  live example is `vahid@aetherex.app`, four follow-ups about an "ElevenLabs
  pilot" for his Agentic Cloud Security cocktail party.

**2026-09-16 (mailbox review, inferred from behaviour, pending his veto)**

Evidence: 768 threads had been tagged `ready-to-archive` and all but the last
run's were archived, so he acts on that label in bulk. 76 threads had been
tagged `to-review`; the ones he handled were archived, the rest sat in the
inbox, many past their date. Every rule below came from a pattern in what he
opened, replied to, forwarded, trashed, or ignored.

- **Interactive Brokers "Message Center Notification" → archive.** Six in six
  months, every one a SPY dividend notice, none opened. The July "Please
  Verify Info" was handled within days, so verification mail stays to-review.
- **Calendar "Notification:" and "Reminder:" mails → archive.** The routine
  had been splitting them between the two labels. The invitation itself is
  the thing to review; the reminder is plumbing.
- **Recurring nags → archive**: Vercel domain misconfiguration (eight since
  March, never acted on), AWS Free Tier alerts (twice monthly, never opened),
  AWS Health notices. Payment-failure mail is *not* in this bucket.
- **Expert-network solicitations → archive.** Five Third Bridge follow-ups
  sat unlabelled and unread from July 30 to September 16; AlphaSights has
  written six times since 2020 without an answer.
- **"Offer to purchase Stealth" M&A spam → archive**, same class as the
  fundraising spam above.
- **Program broadcasts → archive with a one-line summary.** The AWS Heroes
  survey and EMEA town halls had been landing in to-review; they are sent to
  every member.
- **Payment requests (חשבון עסקה) are not receipts.** The morning.co
  invoice for the AWS community photographer was tagged archive, but he had to
  forward it to Reut Doron to get it paid. Requests for payment are to-review.
- **Misdirected amisragas debt notices → archive silently.** He trashed all
  seven.
- **Staleness sweep added.** Finished to-review threads were piling up
  (event passed, DocuSign completed, backpack deadline three weeks gone).
- **Unlabelled items expire after 14 days.** Raising something once and
  leaving it forever just recreates the pile the routine exists to remove.
