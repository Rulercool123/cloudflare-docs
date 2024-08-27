---
title: Policy violations
pcx_content_type: concept
weight: 4
meta:
  description: Page Shield reports any violations to your custom Page Shield policies.
---

# Policy violations

{{<Aside type="note">}}
Only available to Enterprise customers with a paid add-on.
{{</Aside>}}

Shortly after you configure Page Shield policies, the Cloudflare dashboard will start displaying any violations of those policies. This information will be available for policies with any [action](/page-shield/policies/#policy-actions) (_Allow_ and _Log_).

Information about policy violations is also available via [GraphQL API](/analytics/graphql-api/) and [Logpush](/logs/about/).

## Review policy violations in the dashboard

The policy violation information is available in **Security** > **Page Shield** > **Policies**. It includes the following:

* A sparkline next to the policy name, showing policy violations in the past seven days.
* For policies with associated violations, an expandable details section for each policy, with the top resources present in policy violation events and a sparkline per top resource.

## Get policy violations via GraphQL API

Use the [Cloudflare GraphQL API](/analytics/graphql-api/) to obtain policy violation information through the following dataset:

* `pageShieldReportsAdaptiveGroups`

You can query the dataset for policy violations occurred in the past 30 days.

Use [introspection](/analytics/graphql-api/features/discovery/introspection/) to explore the available fields the GraphQL schema. For more information, refer to [Explore the GraphQL schema](/analytics/graphql-api/getting-started/explore-graphql-schema/).

For an introduction to GraphQL querying, refer to [Querying basics](/analytics/graphql-api/getting-started/querying-basics/).

### Example

```graphql
---
header: Example GraphQL query
---
query PageShieldReports($zoneTag: string, $datetimeStart: string, $datetimeEnd: string) {
  viewer {
    zones(filter: {zoneTag: $zoneTag}) {
      pageShieldReportsAdaptiveGroups(limit: 100,  orderBy: [datetime_ASC], filter: {datetime_geq:$datetimeStart, datetime_leq:$datetimeEnd}) {
        avg {
          sampleInterval
        }
        count
        dimensions {
          policyID
          datetime
          datetimeMinute
          datetimeFiveMinutes
          datetimeFifteenMinutes
          datetimeHalfOfHour
          datetimeHour
          url
          urlHost
          host
          resourceType
          pageURL
          action
        }
      }
    }
  }
}
```

{{<details header="Example curl request">}}

```bash
echo '{ "query":
  "query PageShieldReports($zoneTag: string, $datetimeStart: string, $datetimeEnd: string) {
    viewer {
      zones(filter: {zoneTag: $zoneTag}) {
        pageShieldReportsAdaptiveGroups(limit: 100,  orderBy: [datetime_ASC], filter: {datetime_geq:$datetimeStart, datetime_leq:$datetimeEnd}) {
          avg {
            sampleInterval
          }
          count
          dimensions {
            policyID
            datetime
            datetimeMinute
            datetimeFiveMinutes
            datetimeFifteenMinutes
            datetimeHalfOfHour
            datetimeHour
            url
            urlHost
            host
            resourceType
            pageURL
            action
          }
        }
      }
    }
  }",
  "variables": {
    "zoneTag": "<CLOUDFLARE_ZONE_ID>",
    "datetimeStart": "2023-04-17T11:00:00Z",
    "datetimeEnd": "2023-04-24T12:00:00Z"
  }
}' | tr -d '\n' | curl --silent \
https://api.cloudflare.com/client/v4/graphql \
--header "Authorization: Bearer <API_TOKEN>" \
--header "Content-Type: application/json" \
--data @-
```

{{</details>}}

## Get policy violations via Logpush

[Cloudflare Logpush](/logs/about/) supports pushing logs to storage services, {{<glossary-tooltip term_id="SIEM">}}SIEM systems{{</glossary-tooltip>}}, and log management providers.

Information about Page Shield policy violations is available in the [`page_shield_events` dataset](/logs/reference/log-fields/zone/page_shield_events/).

For more information on configuring Logpush jobs, refer to [Logs: Get started](/logs/get-started/).