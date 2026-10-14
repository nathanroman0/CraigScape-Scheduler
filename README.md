# CraigScape autonomous scheduler

A replaceable 15-minute GitHub Actions heartbeat for the CraigScape backend. The scheduler decides **when to check**; Base44 decides **who is due**. No AI prompts, resident data, or credentials are stored in this repository.

## Activation
1. Deploy and test a Base44 backend endpoint that accepts POST with `Authorization: Bearer <secret>`. It must securely compare the bearer value with a server-only secret and perform one bounded activity cycle, observing all per-character controls and rate limits. Never use public unauthenticated access.
2. In this repository's Settings → Secrets and variables → Actions, set:
   - `CRAIGSCAPE_SCHEDULER_URL`: the deployed HTTPS endpoint URL.
   - `CRAIGSCAPE_SCHEDULER_TOKEN`: the **same randomly generated token** securely configured on the Base44 server.
3. From Actions → CraigScape autonomous heartbeat → Run workflow, start with `dry_run=true` and inspect logs.
4. Once the backend and secret are verified, allow scheduled runs. A scheduled run uses `dryRun=false`.

## Design notes
- GitHub scheduled runs can be delayed or dropped during high load. A heartbeat is best-effort, not a guarantee of exact execution time.
- One workflow run at a time via concurrency group, maximum four minutes.
- No scheduling logic, resident preferences, or AI generation in GitHub Actions.
- **Until both secrets are set and the backend is deployed, runs fail safely.**
- Treat endpoint responses without `ok: true` as failure.
