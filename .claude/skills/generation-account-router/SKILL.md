---
name: generation-account-router
description: Select an explicitly approved account and provider for authorized media-generation jobs, track estimated usage, and use an approved fallback when credits are definitively exhausted. Use before generation when more than one account is configured.
---

# Generation account router

This skill governs routing policy. It is not itself an API connector, login session, credential store, or guarantee that a provider supports automatic failover.

## Configuration and secrets

- Read `docs/GENERATION-ACCOUNTS.json` if present; use `docs/GENERATION-ACCOUNTS.example.json` as the schema.
- Never ask the user to paste passwords, session cookies, recovery codes, or API keys into chat, prompts, project files, or logs.
- Store API keys in Windows Credential Manager/DPAPI or another explicitly configured OS secret store. Project config may contain secret references, never secret values.
- Use only accounts marked `approved: true`; never infer approval from a browser being signed in.
- Honor account-specific/provider-wide limits, terms, regional restrictions, and model permissions. Do not switch accounts to evade provider rate limits, trial limits, identity restrictions, or enforcement.

## Routing procedure

1. Choose the highest-priority approved account with a supported connector for the requested task and model.
2. Estimate batch credits/cost and compare against configured job, daily, monthly, and per-account limits. Stop if the request exceeds a configured cap. If usage cannot be estimated, disclose that before paid work unless the user explicitly authorized unmetered use for this provider.
3. Submit each job once; record provider, account alias, model, request ID, estimate, and status without logging secrets.
4. Switch to the next approved compatible account only after a definitive quota/credit-exhausted response and only where provider terms permit. Resume only failed jobs; never duplicate successful or pending jobs.
5. Do not fail over on invalid credentials, permission denial, safety refusal, model unavailability, transient server errors, or ambiguous timeouts. Surface the error and follow the provider's documented retry behavior.
6. Record the account alias and fallback reason in the project generation ledger. Do not expose emails or secrets unnecessarily.

## Provider limits

- Browser-only services such as Google Flow need a supported, user-authorized integration or user operation. A skill cannot control a logged-in website or safely switch Google accounts by itself.
- Prefer official APIs/connectors that support the needed models, reference inputs, and usage reporting. API billing, quota, access, and terms may differ from a consumer website subscription.
- If no supported connector, usage signal, or permitted failover exists, prepare prompts and pause for the user to generate/download assets manually.
- Never promise automatic failover before verifying each account's connector, quota signals, and a low-cost request.

## Job ledger

Store `GENERATION-LOG.csv` or JSONL inside the private video project: timestamp, job ID, provider, account alias, model, asset, estimated/actual credits when known, status, and fallback reason. Never store credentials, cookies, or recovery data in the ledger.
