# AE Outbound Follow-Up Digest (daily, weekdays)

Each weekday morning, DM each AE on Slack a prioritized list of their open outbound deals that need follow-up.

## Config (verified against HubSpot/Slack on 2026-11-17)

| AE | HubSpot owner ID | Slack user ID |
|---|---|---|
| Patrick Onusko | 164914382 | U0B6QS5G864 |
| Allie Schneider | 160797910 | U09SDSR13HS |
| Aaron Lewis | 164914381 | U0B5Q6QA8JX |

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
2. Group by `hubspot_owner_id`.
3. Flag a deal (highest priority first):
   1. **Contract Sent** and no activity in 2+ days
   2. **Nothing scheduled** (no `notes_next_activity_date`) and last contacted 3+ days ago or never
   3. **Going cold**: last contacted 5+ days ago
   4. **Heads up**: meeting today or tomorrow (prep note, not a nag)
   Use `notes_last_contacted`, falling back to `notes_last_updated`.
4. Send one DM per AE (channel_id = Slack user ID) via `slack_send_message`. Skip AEs with zero flagged deals.
   Format: greeting, count, then per deal: **Deal name** (link) | stage | $amount | sourced by X | last touch N days ago | suggested action.
   Cap at 10 deals, most urgent first; end with "+N more in HubSpot" if truncated.
5. Deal link: `https://app.hubspot.com/contacts/243178497/record/0-3/{id}`

## Rules
- Never write to HubSpot. Read-only.
- Do not message anyone other than the three AEs.
- Thresholds above are starting values; tune after a week.
