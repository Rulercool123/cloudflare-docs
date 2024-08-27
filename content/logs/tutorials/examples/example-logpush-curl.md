---
pcx_content_type: how-to
title: Manage Logpush with cURL
weight: 88
---

# Manage Logpush with cURL

You can manage your Cloudflare Logpush service from the command line using cURL.

Before getting started, review the following documentation:

- [API configuration](/logs/get-started/api-configuration/)
- [Logpush job object definition](/api/operations/get-zones-zone_identifier-logpush-jobs)

{{<Aside type="note">}}

The examples below are for zone-scoped datasets. Account-scoped datasets should use `/accounts/{account_id}` instead of `/zone/{zone_id}`.

{{</Aside>}}

## Step 1 - Get ownership challenge

```bash
curl --silent --request POST \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/ownership" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" \
--header "Content-Type: application/json" \
--data '{"destination_conf":"s3://<BUCKET_PATH>?region=us-west-2"}' | jq .
```

### Parameters

- **destination_conf** - Refer to [Destination](/logs/get-started/api-configuration/#destination) for details.

### Response

A challenge file will be written to the destination, and the filename will be in the response (the filename may be expressed as a path if appropriate for your destination). For example:

```json
{
  "success": true,
  "errors": [],
  "messages": [],
  "result": {
    "filename": "burritobot/logs/ownership-challenge.txt",
    "valid": true,
    "message": ""
  }
}
```

You will need to provide the token contained in this file when creating a job in the next step.

{{<Aside type="note" header="Note">}}

When using Sumo Logic, you may find it helpful to have [Live Tail](https://help.sumologic.com/05Search/Live-Tail/About-Live-Tail) open to see the challenge file as soon as it is uploaded.

{{</Aside>}}

## Step 2 - Create a job

```bash
curl --silent --request POST \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" \
--header "Content-Type: application/json" \
--data '{
"name":"<DOMAIN_NAME>",
"destination_conf":"s3://<BUCKET_PATH>?region=us-west-2",
"dataset": "http_requests",
"output_options": {
    "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
    "timestamp_format": "rfc3339"
},
"ownership_challenge":"00000000000000000000"}' | jq .
```

### Parameters

- **name** (optional) - We suggest using your domain name as the job name; the name cannot be changed after the job is created.
- **destination_conf** - Refer to [Destination](/logs/get-started/api-configuration/#destination) for details.
- **dataset** - The category of logs you want to receive. Refer to [Log fields](/logs/reference/log-fields/) for the full list of supported datasets; this parameter cannot be changed after the job is created.
- **output_options** (optional) - Refer to [Log Output Options](/logs/reference/log-output-options/).
  - Typically includes the desired fields and timestamp format.
  - Set the timestamp format to `RFC 3339` (`&timestamps=rfc3339`) for:
    - Google BigQuery usage.
    - Automated timestamp parsing within Sumo Logic; refer to [timestamps from Sumo Logic](https://help.sumologic.com/03Send-Data/Sources/04Reference-Information-for-Sources/Timestamps%2C-Time-Zones%2C-Time-Ranges%2C-and-Date-Formats) for details.
- **ownership_challenge** - Challenge token required to prove destination ownership.
- **kind** (optional) - Used to differentiate between Logpush and Edge Log Delivery jobs. Refer to [Kind](/logs/get-started/api-configuration/#kind) for details.
- **filter** (optional) - Refer to [Filters](/logs/reference/filters/) for details.

### Response

In the response, you get a newly-created job ID. For example:

```json
{
  "errors": [],
  "messages": [],
  "result": {
    "id": 146,
    "dataset": "http_requests",
    "enabled": false,
    "name": "<DOMAIN_NAME>",
    "output_options": {
        "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
        "timestamp_format": "rfc3339"
    },
    "destination_conf": "s3://<BUCKET_PATH>?region=us-west-2",
    "last_complete": null,
    "last_error": null,
    "error_message": null
  },
  "success": true
}
```

## Step 3 - Enable (update) a job

Start by retrieving information about a specific job, using a job ID:

```bash
curl --silent --request GET \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs/146" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" | jq .
```

### Response

```json
{
  "errors": [],
  "messages": [],
  "result": {
    "id": 146,
    "dataset": "http_requests",
    "enabled": false,
    "name": "<DOMAIN_NAME>",
    "output_options": {
        "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
        "timestamp_format": "rfc3339"
    },
    "destination_conf": "s3://<BUCKET_PATH>?region=us-west-2",
    "last_complete": null,
    "last_error": null,
    "error_message": null
  },
  "success": true
}
```

Note that by default a job is not enabled (`"enabled": false`).

If you do not remember your job ID, you can retrieve it using your zone ID:

```bash
curl --silent --request GET \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" | jq .
```

Next, to enable the job, send an update request:

```bash
curl --silent --request PUT \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs/146" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" \
--header "Content-Type: application/json" \
--data '{"enabled":true}' | jq .
```

### Response

```json
{
  "errors": [],
  "messages": [],
  "result": {
    "id": 146,
    "dataset": "http_requests",
    "enabled": true,
    "name": "<DOMAIN_NAME>",
    "output_options": {
        "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
        "timestamp_format": "rfc3339"
    },
    "destination_conf": "s3://<BUCKET_PATH>?region=us-west-2",
    "last_complete": null,
    "last_error": null,
    "error_message": null
  },
  "success": true
}
```

Once the job is enabled, you will start receiving logs within a few minutes and then in batches as soon as possible until you disable the job. For zones with very high request volume, it may take several hours before you start receiving logs for the first time.

In addition to modifying `enabled`, you can also update the value for **output_options**. To modify **destination_conf**, you will need to request an ownership challenge and provide the associated token with your update request. You can also delete your current job and create a new one.

Once a job has been enabled and has started executing, the **last_complete** field will show the time when the last batch of logs was successfully sent to the destination:

### Request to get job by ID and see **last_complete** info

```bash
curl --silent --request GET \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs/146" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" | jq .
```

### Response

```json
{
  "errors": [],
  "messages": [],
  "result": {
    "id": 146,
    "dataset": "http_requests",
    "enabled": true,
    "name": "<DOMAIN_NAME>",
    "output_options": {
        "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
        "timestamp_format": "rfc3339"
    },
    "destination_conf": "s3://<BUCKET_PATH>?region=us-west-2",
    "last_complete": "2018-08-09T21:26:00Z",
    "last_error": null,
    "error_message": null
  },
  "success": true
}
```

## Optional - Delete a job

```bash
curl --silent --request DELETE \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs/146" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" | jq .
```

Be careful when deleting a job because this action cannot be reversed.

### Response

```json
{
  "errors": [],
  "messages": [],
  "result": {},
  "success": true
}
```

## Optional - Retrieve your job

Retrieve a specific job, using the job ID:

```bash
curl --silent --request GET \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs/146" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" | jq .
```

### Response

```json
{
  "errors": [],
  "messages": [],
  "result": [
    {
      "id": 146,
      "dataset": "http_requests",
      "enabled": true,
      "name": "<DOMAIN_NAME>",
      "output_options": {
          "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
          "timestamp_format": "rfc3339"
      },
      "destination_conf": "s3://<BUCKET_PATH>?region=us-west-2",
      "last_complete": null,
      "last_error": null,
      "error_message": null
    }
  ],
  "success": true
}
```

Retrieve all jobs for all datasets:

```bash
curl --silent --request GET \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" | jq .
```

### Response

```json
{
  "errors": [],
  "messages": [],
  "result": [
    {
      "id": 8029,
      "dataset": "spectrum_events",
      "enabled": true,
      "name": "<DOMAIN_NAME>",
      "output_options": {
          "field_names": ["Application", "ClientAsn", "ClientIP", "ColoCode", "Event", "OriginIP", "Status"],
      },
      "destination_conf": "s3://<BUCKET_PATH>?region=us-west-2",
      "last_complete": "2019-10-01T00:25:00Z",
      "last_error": null,
      "error_message": null
    },
    {
      "id": 146,
      "dataset": "http_requests",
      "enabled": false,
      "name": "<DOMAIN_NAME>",
      "output_options": {
          "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
          "timestamp_format": "rfc3339"
      },
      "destination_conf": "s3://<BUCKET_PATH>?region=us-west-2",
      "last_complete": "2019-09-24T21:15:00Z",
      "last_error": null,
      "error_message": null
    }
  ]
}
```

## Optional - Update **output_options**

If you want to add (or remove) fields, change the timestamp format, or enable protection against the `Log4j - CVE-2021-44228` vulnerability, first retrieve the current **output_options** for your zone.

```bash
curl --silent --request GET \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs/{job_id}" \
--header "X-Auth-Key: <API_KEY>" \
--header "X-Auth-Email: <EMAIL>" | jq .
```

### Response

```json
{
  "errors": [],
  "messages": [],
  "result": {
    "id": 146,
    "dataset": "http_requests",
    "logstream": true,
    "frequency": "high",
    "kind": "",
    "enabled": true,
    "name": "<DOMAIN_NAME>",
    "output_options": {
        "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
        "timestamp_format": "rfc3339"
    },
    "destination_conf": "s3://<BUCKET_PATH>?region=us-west-2",
    "last_complete": "2021-12-14T19:56:49Z",
    "last_error": null,
    "error_message": null
  },
  "success": true
}
```

Next, edit the **output_options** as desired and create a `PUT` request. The following example enables the **CVE-2021-44228** redaction option.

```bash
curl --silent --request PUT \
"https://api.cloudflare.com/client/v4/zones/{zone_id}/logpush/jobs/{job_id}" \
--header "X-Auth-Key: <API_KEY>" \
--header "X-Auth-Email: <EMAIL>" \
--header "Content-Type: application/json" \
--data '{
  "output_options": {
    "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
    "timestamp_format": "rfc3339"
  }
}
```

Note that at this time, the **CVE-2021-44228** option is not available through the UI, and updating your Logpush job through the UI will remove this option.

### Response

```json
{
  "errors": [],
  "messages": [],
  "result": {
    "id": 146,
    "dataset": "http_requests",
    "logstream": true,
    "frequency": "high",
    "kind": "",
    "enabled": true,
    "name": null,
    "output_options": {
      "field_names": ["ClientIP", "ClientRequestHost", "ClientRequestMethod", "ClientRequestURI", "EdgeEndTimestamp","EdgeResponseBytes", "EdgeResponseStatus", "EdgeStartTimestamp", "RayID"],
      "timestamp_format": "rfc3339"
    },
    "destination_conf": "s3://<BUCKET_PATH>?region=us-west-2",
    "last_complete": "2021-12-14T20:02:19Z",
    "last_error": null,
    "error_message": null
  },
  "success": true
}
```