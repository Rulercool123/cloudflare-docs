---
title: Best practices
pcx_content_type: reference
weight: 7
meta:
  title: IRR entry updates best practices
---

# Best practices for IRR entries

An Internet Routing Registry (IRR) record is what notifies internet service providers (ISPs) of how you are allowing your resources to be used. It is necessary to keep your IRR entries up to date so that it is public information that Cloudflare has permission to advertise your prefix or prefixes and to ensure that your traffic can be properly routed on the internet.

The American Registry for Internet Numbers (ARIN) maintains an IRR that allows registrants of AS numbers and IP addresses to publish that information so that ISPs can make appropriate routing decisions. This helps ensure ISPs will recognize your routes as legitimate and enables them to ignore unauthorized routes published by someone else.

## Configure an IRR entry

You can add or update an IRR entry by following the directions within any of the recommended internet registries listed in the [Internet Routing Registry](https://www.irr.net/index.html). 

If you own your own subnet, use the RIPE and APNIC routing registries. These registries allow you to verify subnet ownership.

If you lease your subnet, follow these guidelines:
  - When you do not need ownership verification, use the AFRINIC or NTT routing registry.
  - When you submit a route object via email, use the ARIN registry. Address blocks owned by others do not appear in the ARIN interface.

The recommended registries are AFRINIC, APNIC, ARIN, NTT, RADB, and RIPE.

Each routing registry has its own set of instructions to configure an IRR entry. Refer to the table below for more information.

{{<table-wrap>}}<table>

<thead>
    <tr>
      <th>Route registry</th>
      <th>URL</th>
     </tr>
  </thead>
  <tbody>
    <tr>
      <td>AFRINIC</td>
      <td><a href="https://afrinic.net/internet-routing-registry#guide">https://afrinic.net/internet-routing-registry#guide</a></td>
    </tr>
    <tr>
      <td>APNIC</td>
      <td><a href="https://www.apnic.net/manage-ip/apnic-services/routing-registry/">https://www.apnic.net/manage-ip/apnic-services/routing-registry/</a></td>
    </tr>
    <tr>
      <td>ARIN</td>
      <td><a href="https://www.arin.net/resources/manage/irr/quickstart/">https://www.arin.net/resources/manage/irr/quickstart/</a></td>
    </tr>
    <tr>
      <td>NTT</td>
      <td><a href="https://www.gin.ntt.net/support-center/policies-procedures/routing-registry/">https://www.gin.ntt.net/support-center/policies-procedures/routing-registry/</a></td>
    </tr>
    <tr>
      <td>RADB</td>
      <td><a href="https://www.radb.net/support/">https://www.radb.net/support/</a></td>
    </tr>
    <tr>
      <td>RIPE</td>
      <td><a href="https://www.ripe.net/manage-ips-and-asns/db/support/managing-route-objects-in-the-irr">https://www.ripe.net/manage-ips-and-asns/db/support/managing-route-objects-in-the-irr</a></td>
    </tr>
  </tbody>
</table>{{</table-wrap>}}

## Verify an IRR entry

Verify your Internet Routing Registry (IRR) entries to ensure that the IP prefixes Cloudflare advertises for you match the correct {{<glossary-tooltip term_id="autonomous system numbers (ASNs)">}}autonomous system numbers (ASNs){{</glossary-tooltip>}}.

Each IRR entry record must include the following information:

- **Route**: Each IP prefix Cloudflare advertises for you.
- **Origin ASN**: Your ASN, or if you do not have your own ASN, the Cloudflare ASN (`AS209242`).
- **Source**: The name of the routing registry, for example, AFRINIC, APNIC, ARIN, RADB, RIPE, or NTT.

Add or update IRR entries when they meet any of these criteria:

- The entry is missing.
- The entry is incomplete or inaccurate — for example, when the route object does not show the correct origin.
- The entry is complete but requires updating — for example, when they correspond to supernets but need to correspond to subnets used in Magic Transit.

You are strongly encouraged to verify IRR entries for the exact prefixes you will use to onboard with Cloudflare. 

IRR entries for less specific prefixes are acceptable as long as you understand and accept the following risk: if you modify your IRR entries in the future (for example, by changing your ASN) and the IRR entry for the supernet no longer matches the prefix or origin mapping in your Magic Transit configuration, the prefix will have reduced reachability due to networks Cloudflare peers with automatically filtering the prefix. Having specific IRR entries helps minimize (but not entirely remove) this risk.

### IRR entry verification methods

To verify your prefix and ASN route, use the tools and methods outlined on the table below:

{{<table-wrap>}}<table>

  <thead>
    <tr>
      <th>Data to verify</th>
      <th>Tool</th>
      <th>Method</th>
      <th>Output</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Subnet prefix IP<br/>for the ASN</td>
      <td><a href=" https://irrexplorer.nlnog.net">IRR Explorer</a></td>
      <td>Search for the subnet prefix IP, for example, <code>162.211.156.0/24</code>.</td>
      <td>List of ASN numbers, source (route registry), and any associated errors.</td>
    </tr>
    <tr>
      <td>ASN for the<br/>subnet prefix</td>
      <td><span style="white-space: nowrap"><a href=" https://irrexplorer.nlnog.net">IRR Explorer</a></span></td>
      <td><span style="white-space: nowrap">Search for the ASN, for example <code>AS209242</code>.</span></td>
      <td><span style="white-space: nowrap">List of prefixes, source, and any associated errors.</span></td>
    </tr>
    <tr>
      <td>Your origin ASN<br/>and routing data</td>
      <td>WHOIS lookup</td>
      <td>
        <p>In a terminal, use this {{<code>}}whois{{</code>}} command, substituting your network prefix for <em>network-prefix</em>:</p>
        <p>{{<code>}}whois -h rr.ntt.net network-prefix{{</code>}}</p>
        <p>The host {{<code>}}rr.ntt.net{{</code>}} is the primary server for the Global IP network.</p>
      </td>
      <td>IRR route, origin, and source information.</td>
    </tr>
  </tbody>
</table>{{</table-wrap>}}

#### WHOIS output

The `<IRR entry section>` in the WHOIS output shows the correct IRR entry information for the specified network. In this example, the network prefix is `1.1.1.0/24`, and the output includes the route, origin ASN, and route registry, which in this example is APNIC:

```txt
---
header: Example
---
user@xxt32z conduit-qs-config % whois -h rr.ntt.net 1.1.1.0/24
route:          1.1.1.0/24
<RPKI section>
descr:          RPKI ROA for 1.1.1.0/24
remarks:        This route object represents routing data retrieved from the RPKI
remarks:        The original data can be found here: https://rpki.gin.ntt.net/r/AS13335/1.1.1.0/24
remarks:        This route object is the result of an automated RPKI-to-IRR conversion process.
remarks:        maxLength 24
origin:         AS13335
mnt-by:         MAINT-NTTCOM-RPKI
changed:        job@ntt.net 20200913
source:         RPKI  # Trust Anchor: apnic

<IRR entry section>
route:          1.1.1.0/24
origin:         AS13335
descr:          APNIC Research and Development
                6 Cordelia St
mnt-by:         MAINT-AU-APNIC-GM85-AP
last-modified:  2018-03-16T16:58:06Z
source:         APNIC
```

{{<Aside type="note">}}
WHOIS output also shows the RPKI entry information for prefix IP addresses. When your WHOIS output only contains an RPKI entry, you must add the IRR entry.
{{</Aside>}}