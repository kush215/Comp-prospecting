# AE Outbound Follow-Up Digest (weekdays, 9:00 AM ET)

Each weekday morning, post each AE a prioritized list of their open outbound deals that need follow-up, including open action items on both the prospect's side and the AE's side. Post to the AE's dedicated Slack channel (not a DM).

## Config (verified 2026-11-18)

| AE (Deal Owner) | HubSpot owner ID | Slack channel | Channel ID |
|---|---|---|---|
| Patrick Onusko | 164914382 | #pat-outbound-deal-agent | C0CKRH6TM32 |
| Allie Schneider | 160797910 | #allie-outbound-deal-agent | C0CJRTD810D |
| Aaron Lewis | 164914381 | #aaron-outbound-deal-agent | C0CJRT7AH61 |

The AE is identified by the deal's **Deal Owner** (`hubspot_owner_id`).

Sales Source property: `sales_source` (stores HubSpot owner IDs)

| Sales Source | ID |
|---|---|
| Kush Sarin | 168517645 |
| Tanish Nichanametla | 168517632 |
| Patrick Serna | 166883890 |

Excluded deal stages: Unqualified To Buy (`1955572453`), Duplicate - N/A (`3319343815`), Closed Won/Lost.

## Steps

1. `search_crm_objects` on DEAL with filters (AND):
   - `hs_is_closed` = false
   - `hubspot_owner_id` IN the 3 AE IDs
   - `sales_source` IN the 3 source IDs
   - `dealstage` NOT_IN [1955572453, 3319343815]
   Properties: dealname, dealstage, amount, sales_source, hubspot_owner_id, notes_last_contacted,
   notes_last_updated, notes_next_activity_date, hs_next_meeting_start_time, hs_next_step, closedate.
   Paginate until `total` is covered.
2. **Drop any deal with a meeting scheduled today or tomorrow** (`hs_next_meeting_start_time`, ET). The team is handling those directly; do not track or mention them.
3. Group remaining deals by `hubspot_owner_id`.
4. Flag a deal (most urgent first):
   1. **Contract Sent** and no activity in 2+ days
   2. **Nothing scheduled** (no `notes_next_activity_date`) and last contacted 3+ days ago or never
   3. **Going cold**: last contacted 5+ days ago
   Use `notes_last_contacted`, falling back to `notes_last_updated`.
5. **Action items.** For each flagged deal, read the recent engagements (last ~14 days): `search_crm_objects` for NOTE, EMAIL, CALL, MEETING, TASK with `associatedWith` the deal. Also check open TASKs. Identify:
   - **Prospect-side** action items (things the prospect said they'd do or owe us: send docs, loop in a stakeholder, get approval, decide by a date)
   - **AE-side** action items (things we promised: send pricing/contract/security docs, follow-up on a date, intro to someone)
   Only report items actually supported by the logged activity. If none are found, write "No logged action items". Never invent them. Note if an item looks overdue.
6. Post one message per AE to their channel via `slack_send_message`. Skip AEs with zero flagged deals (post nothing).
   Format: short greeting, count, then per deal:
   **Deal name** (HubSpot link) | stage | $amount | **Sourced by: X** (bold, so the source rep is easy to see) | last touch N days ago
   - Why flagged
   - Prospect action items
   - AE action items
   - Suggested next step
   Cap at 10 deals, most urgent first; end with "+N more in HubSpot" if truncated.
7. Deal link: `https://app.hubspot.com/contacts/243178497/record/0-3/{id}`

## Rules
- Read-only on HubSpot. Never modify records.
- Post only to the three channels above, only to the channel matching the deal's owner.
- Thresholds are starting values; tune after a week.
