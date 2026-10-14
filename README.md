# comp-prospecting

A Claude Code plugin that runs Comp AI's prospecting workflow for **one rep at a time, on that rep's own accounts**:

Sales Navigator page → YC + fit check (one slow, read-only research agent) → Apollo email → HubSpot/Nooks dedupe (person level) → compliance scanner → HubSpot company fields → HubSpot contact → Nooks sequence (with email + phone enrichment).

## Commands

| Command | Status |
|---|---|
| `/prospect-yc` | ✅ v0.1 |
| `/prospect-newhires` | planned |
| `/prospect-funding` | planned (no LinkedIn needed) |

## What each rep needs
- Claude Code (desktop app or CLI) with these connectors signed in **as themselves**: HubSpot, Nooks, Apollo, Slack, and Claude in Chrome.
- Chrome logged into their own LinkedIn Sales Navigator.
- Membership in `#compliance-scanner`.
- A YC saved search in Sales Nav (YC school filter, Founder/Co-Founder/CTO titles, excluding "Viewed profile").

## Install
From this folder or the team's Git repo:

```
/plugin marketplace add <path-or-git-url-to-this-repo>
/plugin install comp-prospecting@comp-ai-sales
```

Then say **"prospect YC"**. The first run walks through setup and writes `~/.comp-prospecting/config.json` (see `config.example.json`).

## Talking to it
Your config sets defaults; just say what you want differently for a run:
- "prospect YC page 30 and put them in **YC Fintech Q4**"
- "next page, dry run — show me who you'd enroll first"
- "only the first 10 leads, include Medium fits"
- "skip Acme, start everyone on step 2, schedule for Monday 9am"
- "make **YC V2** my default sequence"

## Safety rules (enforced by the skill)
- LinkedIn is read-only; one tab; 15–25s between profile loads; one Sales Nav page per run; daily cap on profile views (default 50).
- Stops on any CAPTCHA / checkpoint / unusual-activity page.
- YC directory is checked first, so most leads never need a LinkedIn profile visit.
- Never enrolls anyone owned by another rep, at a customer, at a company with an open deal, or already contacted.
- Writes only `compliance_tool` and `compliance_scanner_summary` on companies; never edits `frameworks`.

**Risk note:** any automated LinkedIn browsing, even slow and read-only, is against LinkedIn's terms. Each rep is using their own account and should be comfortable with that.
