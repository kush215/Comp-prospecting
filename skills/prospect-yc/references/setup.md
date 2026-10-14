# First-time setup

Create `~/.comp-prospecting/config.json` for the rep using this session. Fill what you can look up yourself; ask the rep only for what you can't.

| Field | How to get it |
|---|---|
| `rep_name`, `rep_email` | HubSpot `get_user_details` |
| `hubspot_owner_id` | HubSpot `search_owners` by the rep's email |
| `nooks_user_id` | Nooks `getMe` → `id` |
| `sequences.yc` | Ask the rep which Nooks sequence; resolve the id with `listSequences filter_name=…`. Confirm the rep owns it or it is team-editable. |
| `salesnav_yc_search_url` | Ask the rep to paste their Sales Nav saved-search URL (YC school filter, CEO/CTO/Founder titles, **exclude "Viewed profile"**). Strip any `page=` and `sessionId=` params. |
| `scanner_channel_id` | Slack `slack_search_channels` for `compliance-scanner` |
| `scanner_bot_id` | Read the latest messages in that channel; the bot is the user mentioned as `<@U…|Compliance Scanner>` |
| `exclude_owner_ids` | Optional extra HubSpot owner ids whose accounts are always off-limits (beyond "owned by anyone else") |
| `daily_profile_view_cap` | Default `50` |
| `last_page_done` | Ask the rep which page they're on; default `0` |

Also create `~/.comp-prospecting/usage.json`:

```json
{ "date": "YYYY-MM-DD", "profile_views": 0 }
```

Reset `profile_views` to 0 whenever `date` isn't today.

See `config.example.json` at the plugin root for the shape. Show the rep the finished config and confirm before the first run.

Before the first run, tell the rep plainly: automated browsing of LinkedIn, even slow and read-only, is against LinkedIn's terms and carries some risk to **their own** account. The caps keep it low; they should be comfortable with it.
