# MTA-STS policy for caithomas.com

Serves https://mta-sts.caithomas.com/.well-known/mta-sts.txt, the MTA-STS policy for mail to
caithomas.com (RFC 8461), from GitHub Pages. Created 23 October 2026. The website itself is
`caithomas44/caithomas-website`.

## How it fits together

| Record (Cloudflare DNS, DNS only) | Value |
|---|---|
| `mta-sts` CNAME | `caithomas44.github.io` (this repo's custom domain, the `CNAME` file) |
| `_mta-sts` TXT | `v=STSv1; id=<policy id>` |
| `_smtp._tls` TXT | `v=TLSRPTv1; rua=mailto:mail@caithomas.com` (daily TLS reports) |

The policy lists the Google Workspace MX hosts (`aspmx.l.google.com`, `alt1`/`alt2` through
`*.aspmx.l.google.com`, `aspmx2.googlemail.com`, `aspmx3.googlemail.com`). It starts in
`mode: testing` with `max_age: 86400`: senders report failures but still deliver.

## Changing the policy

1. Edit `.well-known/mta-sts.txt` (keep the CRLF line endings; `.gitattributes` stops git
   changing them) and push to `main`.
2. Change the `id` in the `_mta-sts` TXT record to something new (for example today's date and
   time, letters and digits only). Senders only re-fetch the policy when the id changes.

To move to enforcement once the TLS reports are clean for a few weeks: set `mode: enforce` and
`max_age: 604800`, then update the id. If the MX records ever change (for example to Google's
newer `smtp.google.com`), update the `mx:` lines first, or mail from enforcing senders will fail.

To switch MTA-STS off: set `mode: none`, update the id, wait for the old `max_age` to pass, then
remove the records.
