# LinkedIn gate before Nooks enrollment

Step 1 of every sequence is an automatic LinkedIn connection request. Nooks runs that step the moment a prospect is enrolled. If the prospect has no LinkedIn URL at that moment, the step is marked done and **no request goes out**. This happens when a reused HubSpot contact has an empty `hs_linkedin_url`, or when Nooks enrichment fills the URL in seconds after enrollment. So the URL must already be on the Nooks prospect before `createSequenceState`.

## 1. Get a URL for every contact
- Use the LinkedIn URL from the Apollo match (`linkedin_url`) or from the play's own research (Sales Nav for YC).
- If it's missing, try Apollo `apollo_people_match` with name + company domain (1 credit, already covered by the run's enrichment approval).
- Normalize it to `https://www.linkedin.com/in/<slug>`: https, `www.`, no query string, no trailing slash. Apollo returns `http://…`. Never use a Sales Nav `/sales/lead/…` URL.

## 2. Put it on the HubSpot contact
- For new contacts, set `hs_linkedin_url` on create.
- For **existing contacts being reused**, also update `hs_linkedin_url` when it's empty or different. This is the case that silently skips the request.

## 3. Sync and verify in Nooks
1. Run `syncProspects` `{type: "hubspot", records: [...]}`.
2. Run `listProspects filter_id=<prospect ids>` and check that every `linkedInUrl` is non-null.
3. For anyone still null, wait ~15s, re-sync, and re-check. Retry up to 3 times.

## 4. Enroll only prospects that have a LinkedIn URL
- Call `createSequenceState` only for prospects whose `linkedInUrl` is set.
- Don't enroll anyone still missing one. List them in the report under **"Not enrolled: no LinkedIn URL"** so the rep can add the URL and enroll them.

## 5. Confirm step 1 ran
About 2 minutes after enrolling, call `listTasks` with `filter_sequence_id`, `filter_action=linkedin_connection_request` and `filter_updatedAt_gte=<enroll time>`. Report any task that isn't `finished`, or that finished before the prospect had a URL, as **"connection request may not have sent: check in Nooks"**.
