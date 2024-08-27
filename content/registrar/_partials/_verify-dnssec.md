---
_build:
  publishResources: false
  render: never
  list: never
---

When DNSSEC has been successfully applied to your domain, Cloudflare shows you a confirmed status. Go to [**DNS** > **Settings**](https://dash.cloudflare.com/?to=/:account/:zone/dns/settings) in the Cloudflare dashboard, and scroll down to **DNSSEC**.

You can also confirm this by reviewing the [WHOIS information](https://lookup.icann.org/) for your domain. Domains with DNSSEC will read `signedDelegation` in the DNSSEC field.