# Scheduling the morning funding scan

Run this on **one** computer only (Kush's). The dashboard dedupes by domain, but two machines scanning the same feeds just doubles the browsing and the HubSpot lookups for no gain.

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

## The scheduled task

In the Claude desktop app, create **one** scheduled task in this plugin's folder (so the skills and `funding/*.json` are available):

- **When:** weekdays only (Monday–Friday), 8:45am Eastern
- **Prompt:** `Run /funding-to-dashboard, then run /roe-for-dashboard.`

Running both in one task keeps the order right: the funding companies arrive first, then the ROE check covers them along with closed-lost deals and anything else waiting. When the ROE results reach the dashboard, it assigns that day's prospects right away, so lists are usually ready by about 9:15.

`/roe-for-dashboard` is only needed until the dashboard has its own HubSpot token. Once `HUBSPOT_TOKEN` is set in Vercel, it notices (`mode: internal`) and does nothing; change the prompt to just `Run /funding-to-dashboard.`

## What has to be true at run time

- The computer is awake, the Claude desktop app is open, and Claude in Chrome is connected.
- The HubSpot and Nooks connectors are signed in (for `/roe-for-dashboard`).
- Chrome is signed in to Fundup. If the session has expired, the run reports Fundup as `blocked`, and the dashboard shows a banner until you sign in again.
- FinSMEs sometimes shows a Cloudflare check. If it does, the run marks FinSMEs as `blocked`; loading finsmes.com once yourself usually clears it.

## Terms of use

This is your own logged-in account, read at a human pace, and nothing is written back to either site. Fundup's and FinSMEs' terms may still restrict automated access. Check them before relying on this.
