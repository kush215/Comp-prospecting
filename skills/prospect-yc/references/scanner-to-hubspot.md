# Compliance scanner → HubSpot company fields

## Running the scanner
- Post one message per domain in the scanner channel: `<@{{scanner_bot_id}}> https://{domain}`.
- The bot replies in-thread, usually within 1–3 minutes. Read with `slack_read_thread(channel, message_ts)`.
- Reuse an existing thread if the domain was scanned in the last 14 days (`slack_search_public_and_private`, `in:<#channel>`, keyword = company name).

## `compliance_tool` (enum on the company)
Allowed values: `Vanta`, `Drata`, `Oneleet`, `Sprinto`, `Secureframe`, `Probo`, `Delve`, `Other (Unavailable)`, `None`.

| Scanner result | Value |
|---|---|
| Trust center hosted on a known vendor (CNAME / "Powered by …") | that vendor |
| Delve trust page (live, or indexed but now offline) and no newer vendor | `Delve` |
| Claims SOC 2/ISO but no vendor visible, or a vendor is only weakly hinted | `Other (Unavailable)` |
| No certifications and no platform found | `None` |

Re-fetch the enum options with `get_properties` if a write fails — new options may have been added (e.g. an `Unknown` value).

## `compliance_scanner_summary` (text on the company)
Exactly one short line in this shape:

`Frameworks: <every framework found, incl. GDPR/CCPA, marked self-declared / in progress / claimed as appropriate>. Angle: <incumbent + the one pitch that matters>.`

Examples:
- `Frameworks: SOC 2 Type II, ISO 27001, GDPR, CCPA (GDPR/CCPA self-declared). Angle: On Vanta - pitch replacing Vanta at renewal; SOC 2 scope is Security only (site claims more), and ISO 42001 is a natural add for an AI product sold to banks/PE.`
- `Frameworks: None (no SOC 2, ISO 27001 or trust center). Angle: Greenfield - live with a large US insurer, so pitch a fast first SOC 2 before insurer security reviews stall deals.`
- `Frameworks: SOC 2 Type II (claimed, but Delve trust center showed 'In Observation'), GDPR (self-declared). Angle: Delve customer - trust center now offline; pitch a credible re-audit with an independent auditor on Comp AI.`

Do not include security posture details, subprocessors, contacts, or sources. Never claim renewal dates, auditors, or dissatisfaction that the scanner didn't confirm.

## Never
- Never edit the `frameworks` property.
- Never write scanner output to contacts — company only.
