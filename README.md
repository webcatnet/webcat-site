# webcat-site

The website repository for **Webcat.net Corp** (webcat.net) — and **trial #1 of the AI Brain
headless-executor loop** (see `Infra/AI Executor Charter.md`; the charter is binding once merged).

## Live

| Address | What it serves |
|---|---|
| https://www.webcat.net | Company homepage — `Site/holding-page/index.html` (Azure Static Web Apps, Free plan) |
| https://webcat.net | 301 → https://www.webcat.net (GoDaddy domain forwarding) |
| https://domains.webcat.net | GoDaddy reseller domain store (plid 111815) |
| info@webcat.net | Public contact address (Microsoft 365 alias) |

## What's here

```
Site/holding-page/     ← the live site (Azure Static Web Apps deploys this on merge to main)
  index.html           ← homepage v2 (Domains band + info@webcat.net)
  staticwebapp.config.json  ← unknown paths redirect to domains.webcat.net (old storefront bookmarks)
Infra/
  AI Executor Charter.md    ← the executor's binding guardrails (one page, forever)
  DNS Switchover Runbook.md ← v4 as-built: what was changed, current DNS, rollback, do-not-touch
  dns-snapshot-2026-07-13.md ← original pre-change baseline (historical)
handoff/               ← the AI Brain mailbox (tasks in, results back as PRs)
.github/workflows/gitleaks.yml ← secret scan on every push/PR (gitleaks CLI, no license needed)
```

## Rules of the repo

- **Public-destined content only.** No secrets, no WebLab material, no client names until approved.
- **Merge to `main` = production deploy.** Humans merge; nothing else does. PRs get an Azure preview URL first.
- **The AI executor** works via `handoff/` tasks → branch → PR only. It never holds GoDaddy/Azure credentials.
- `main` is protected (1 approval). Solo merges use the admin bypass after checking the preview.

## Status

- 2026-07-13 — repo created; holding page ready; cutover pending (see runbook Phase 0–B).
- 2026-07-14 — relay loop wired (driver, mailbox rules, results contract, cage verified); repo public; main branch protected.
- 2026-10-28 — chosen homepage design published (PR #20). **DNS switchover complete:** www → Azure SWA, storefront → domains.webcat.net, apex 301 → www. Email (M365) unaffected.
- 2026-10-30 — gitleaks switched to the CLI (PR #21). Homepage v2 live (PR #22): Domains section + search into the store, public contact info@webcat.net. www TTL restored to 1 h. DKIM + DMARC (monitor-only) added for webcat.net.
