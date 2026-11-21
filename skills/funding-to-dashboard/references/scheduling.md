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

## The scheduled task

In the Claude desktop app, create a scheduled task:

- **When:** weekdays, about one hour before the dashboard's daily assignment job (e.g. 7:10am if assignment runs at 8:15am).
- **Prompt:** `Run /funding-to-dashboard.`
- **Folder:** this plugin's folder, so the skill and `funding/*.json` are available.

## What has to be true at run time

- The computer is awake, the Claude desktop app is open, and Claude in Chrome is connected.
- Chrome is signed in to Fundup. If the session has expired, the run reports Fundup as `blocked`, and the dashboard shows a banner until you sign in again.
- FinSMEs sometimes shows a Cloudflare check. If it does, the run marks FinSMEs as `blocked`; loading finsmes.com once yourself usually clears it.

## Terms of use

This is your own logged-in account, read at a human pace, and nothing is written back to either site. Fundup's and FinSMEs' terms may still restrict automated access. Check them before relying on this.
