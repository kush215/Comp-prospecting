# Scheduling the morning funding scan

Run this on **one** computer only (Kush's). The dashboard dedupes by domain, but two machines scanning the same feeds just doubles the browsing for no gain.

## One-time setup

1. Install or update the plugin so `/funding-to-dashboard` is available:
   ```
   /plugin marketplace update comp-ai-sales
   /plugin install comp-prospecting@comp-ai-sales
   ```
2. Add two fields to `~/.comp-prospecting/config.json` (never commit them; this repo is public):
   ```json
   "dashboard_url": "https://<your-dashboard>.vercel.app",
   "dashboard_ingest_token": "<the INGEST_TOKEN value set in Vercel>"
   ```
3. Make sure `jq` is installed (`brew install jq`).
4. Do one supervised run: say **"funding to dashboard"** in the Claude desktop app with Claude in Chrome connected, and approve the Chrome site permissions for `fundup.ai` and `finsmes.com` when asked. Unattended runs can't answer permission prompts, so this step matters.

## The scheduled tasks

In the Claude desktop app, create two scheduled tasks, both in this plugin's folder (so the skills and `funding/*.json` are available). The dashboard assigns prospects at about 8:15am ET; both must finish before then.

| Time (ET, weekdays) | Prompt | What it does |
|---|---|---|
| 7:10am | `Run /funding-to-dashboard.` | Reads Fundup + FinSMEs and sends qualified companies |
| 7:40am | `Run /roe-for-dashboard.` | Sends closed-lost deals, then runs ROE on everything waiting (including the funding companies from 7:10) through your HubSpot and Nooks connectors |

`/roe-for-dashboard` is only needed until the dashboard has its own HubSpot token. Once `HUBSPOT_TOKEN` is set in Vercel, it notices (`mode: internal`) and does nothing, and you can delete that task.

## What has to be true at run time

- The computer is awake, the Claude desktop app is open, and Claude in Chrome is connected.
- The HubSpot and Nooks connectors are signed in (for `/roe-for-dashboard`).
- Chrome is signed in to Fundup. If the session has expired, the run reports Fundup as `blocked`, and the dashboard shows a banner until you sign in again.
- FinSMEs sometimes shows a Cloudflare check. If it does, the run marks FinSMEs as `blocked`; loading finsmes.com once yourself usually clears it.

## Terms of use

This is your own logged-in account, read at a human pace, and nothing is written back to either site. Fundup's and FinSMEs' terms may still restrict automated access. Check them before relying on this.
