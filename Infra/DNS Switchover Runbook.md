# DNS Switchover Runbook — webcat.net · v4 as-built (2026-10-30)

Supersedes v2 (2026-07-13). The switchover ran on **2026-10-28** and is **complete**. Email moved to
**Microsoft 365** before the switchover (v2 assumed ImprovMX). This file is now the reference for the
current setup and for rolling back.

**Result:** www.webcat.net → company site (Azure Static Web Apps, Free) · reseller storefront →
domains.webcat.net · webcat.net (apex) → 301 to https://www.webcat.net · email never interrupted · $0/month.

**Who clicks:** GoDaddy / Azure / Microsoft 365 saves are done by a human (or by an assistant only with
explicit OK, human watching). The AI executor: never.

## Current DNS (GoDaddy, ns23/ns24.domaincontrol.com)

| Record | Value | Role |
|---|---|---|
| CNAME www | purple-meadow-0b838cc0f.7.azurestaticapps.net (1 h) | Website |
| TXT _dnsauth.www | Azure validation token | Keeps the Azure custom domain + cert valid — keep |
| A @ ×2 | GoDaddy forwarding IPs | Apex 301 → https://www.webcat.net (Forwarding tab) |
| CNAME domains | cdrapplication.secureserver.net | Reseller storefront (auto-created by GoDaddy) |
| MX @ | webcat-net.mail.protection.outlook.com | **M365 email — never edit** |
| TXT @ SPF | v=spf1 include:spf.protection.outlook.com -all | **M365 email — never edit** |
| CNAME selector1._domainkey / selector2._domainkey | selectorN-webcat-net._domainkey.<tenant>.a-v1.dkim.mail.microsoft | DKIM signing (M365) |
| TXT _dmarc | v=DMARC1; p=none; rua=mailto:info@webcat.net | DMARC, monitor-only |
| CNAME autodiscover, enterprise* · TXT MS= | Microsoft | M365 — never touch |
| NS / SOA, buzz, email, ftp, pay, _domainconnect | GoDaddy / other | leave |

Reseller Control Center: Standard Storefront Domain = **domains.webcat.net**. Always-live fallback:
www.secureserver.net?pl_id=111815.

## What was done (2026-10-28)

1. Zone + storefront snapshot (A0); email baseline in/out ✔.
2. www TTL 3600 → 600 s, waited 1 h.
3. Azure SWA custom domain www.webcat.net via TXT `_dnsauth.www` → Validated (cert auto-renews).
4. Apex forwarding webcat.net → https://www.webcat.net (301, no masking).
5. RCC: **Remove** www.webcat.net from the storefront slot (the box is locked while a domain is
   assigned; deletion took ~50 min) → add domains.webcat.net.
6. www CNAME → Azure. Verified homepage over HTTPS, old store links redirect, email in/out ✔.
7. 2026-10-30: www TTL back to 1 h; DKIM keys + CNAMEs; DMARC p=none.

## Lessons

- GoDaddy's storefront domain slot can't be edited in place — Remove, wait for "Pending deletion" to
  finish, then add the new one. Expect www to error between the two steps; flip www right after.
- The Azure TXT host is `_dnsauth.www` (the portal shows "www").
- Storefront branding can take ~1 h to appear and may briefly revert; re-check before opening a ticket.

## Rollback

| Undo | How |
|---|---|
| Site → storefront on www | www CNAME → cdrapplication.secureserver.net **and** RCC: remove domains.webcat.net, add www.webcat.net |
| Apex forwarding | Forwarding tab → delete |
| DKIM | Defender → Email authentication → DKIM → disable webcat.net, then delete the two CNAMEs |
| DMARC | delete TXT _dmarc |
| Email | MX/SPF never touched — nothing to undo |

## Next

- DMARC: after a few weeks of clean reports, move p=none → p=quarantine.
- Optional: UptimeRobot monitors for www, domains and apex.
