---
pcx_content_type: configuration
title: HTTP policies
weight: 4
---

# HTTP policies

{{<Aside type="note">}}

Install the <a href="/cloudflare-one/connections/connect-devices/warp/user-side-certificates/">Cloudflare Root Certificate</a> before creating HTTP policies.

{{</Aside>}}

HTTP policies allow you to intercept all HTTP and HTTPS requests and either block, allow, or override specific elements such as websites, IP addresses, and file types. HTTP policies operate on Layer 7 for all TCP (and [optionally UDP](/cloudflare-one/policies/gateway/initial-setup/http/#1-connect-to-gateway)) traffic sent over ports 80 and 443.

An HTTP policy consists of an **Action** as well as a logical expression that determines the scope of the policy. To build an expression, you need to choose a **Selector** and an **Operator**, and enter a value or range of values in the **Value** field. You can use **And** and **Or** logical operators to evaluate multiple conditions.

- [Actions](#actions)
- [Selectors](#selectors)
- [Comparison operators](#comparison-operators)
- [Value](#value)
- [Logical operators](#logical-operators)

{{<render file="gateway/_response.md" withParameters="query;;_Source IP_;;_Resolved IP_">}}

## Actions

Actions in HTTP policies allow you to choose what to do with a given set of elements (domains, IP addresses, file types, and so on). You can assign one action per policy.

### Allow

API value: `allow`

{{<details header="Available selectors">}}

**Traffic**

- [Application](#application)
- [Content Categories](#content-categories)
- [Destination Continent IP Geolocation](#destination-continent)
- [Destination Country IP Geolocation](#destination-country)
- [Destination IP](#destination-ip)
- [DLP Profile](#dlp-profile)
- [Domain](#domain)
- [Download File Types](#download-and-upload-file-types)
- [Download Mime Type](#download-and-upload-mime-type)
- [Host](#host)
- [HTTP Method](#http-method)
- [HTTP Response](#http-response)
- [Proxy Endpoint](#proxy-endpoint)
- [Security Risks](#security-risks)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [Source Internal IP](#source-internal-ip)
- [Source IP](#source-ip)
- [Upload File Types](#download-and-upload-file-types)
- [Upload Mime Type](#download-and-upload-mime-type)
- [URL](#url)
- [URL Path](#url-path)
- [URL Path & Query](#url-path-and-query)
- [URL Query](#url-query)
- [Virtual Network](#virtual-network)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

The Allow action allows outbound traffic to reach destinations you specify within the [Selectors](#selectors) and [Value](#value) fields. For example, the following configuration allows traffic to reach all websites we categorize as belonging to the Education content category:

| Selector           | Operator | Value       | Action |
| ------------------ | -------- | ----------- | ------ |
| Content Categories | in       | `Education` | Allow  |

#### Untrusted certificates

{{<Aside type="note">}}

To use this feature, deploy a [custom root certificate](/cloudflare-one/connections/connect-devices/warp/user-side-certificates/custom-certificate/).

{{</Aside>}}

The **Untrusted certificate action** determines how to handle insecure requests.

| Option       | Action                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Error        | Display Gateway error page. Matches the default behavior when no action is configured.                                                                                                                                                                                                                                                                                                                           |
| Block        | Display [block page](/cloudflare-one/policies/gateway/configuring-block-page/) as set in Zero Trust.                                                                                                                                                                                                                                                                                                             |
| Pass through | Bypass insecure connection warnings and seamlessly connect to the upstream. To use this feature, deploy a [custom root certificate](/cloudflare-one/connections/connect-devices/warp/user-side-certificates/custom-certificate/). For more information on what statuses are bypassed, refer to the [troubleshooting FAQ](/cloudflare-one/faq/teams-troubleshooting/#i-see-error-526-when-browsing-to-a-website). |

### Block

API value: `block`

{{<details header="Available selectors">}}

**Traffic**

- [Application](#application)
- [Content Categories](#content-categories)
- [Destination Continent IP Geolocation](#destination-continent)
- [Destination Country IP Geolocation](#destination-country)
- [Destination IP](#destination-ip)
- [DLP Profile](#dlp-profile)
- [Domain](#domain)
- [Download File Types](#download-and-upload-file-types)
- [Download Mime Type](#download-and-upload-mime-type)
- [Host](#host)
- [HTTP Method](#http-method)
- [HTTP Response](#http-response)
- [Proxy Endpoint](#proxy-endpoint)
- [Security Risks](#security-risks)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [Source Internal IP](#source-internal-ip)
- [Source IP](#source-ip)
- [Upload File Types](#download-and-upload-file-types)
- [Upload Mime Type](#download-and-upload-mime-type)
- [URL](#url)
- [URL Path](#url-path)
- [URL Path & Query](#url-path-and-query)
- [URL Query](#url-query)
- [Virtual Network](#virtual-network)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

The Block action blocks outbound traffic from reaching destinations you specify within the [Selectors](#selectors) and [Value](#value) fields. For example, the following configuration blocks users from being able to upload any file type to Google Drive:

| Selector         | Operator      | Value          | Logic | Action |
| ---------------- | ------------- | -------------- | ----- | ------ |
| Application      | in            | `Google Drive` | And   | Block  |
| Upload Mime Type | matches regex | `.*`           |       |        |

{{<heading-pill style="early-access" heading="h4">}}WARP client block notifications{{</heading-pill>}}

{{<render file="gateway/_client-notifications.md">}}

### Isolate

API value: `isolate`

{{<details header="Available selectors">}}

**Traffic**

- [Application](#application)
- [Content Categories](#content-categories)
- [Domain](#domain)
- [Host](#host)
- [HTTP Method](#http-method)
- [Security Risks](#security-risks)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [URL](#url)
- [URL Path](#url-path)
- [URL Path & Query](#url-path-and-query)
- [URL Query](#url-query)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

The Isolate action serves matched traffic to users via [Cloudflare Browser Isolation](/cloudflare-one/policies/browser-isolation/). For more information on this action, refer to [Isolation policies](/cloudflare-one/policies/browser-isolation/isolation-policies/#isolate).

### Do Not Isolate

API value: `noisolate`

{{<details header="Available selectors">}}

**Traffic**

- [Application](#application)
- [Content Categories](#content-categories)
- [Domain](#domain)
- [Host](#host)
- [HTTP Method](#http-method)
- [Security Risks](#security-risks)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [URL](#url)
- [URL Path](#url-path)
- [URL Path & Query](#url-path-and-query)
- [URL Query](#url-query)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

The Do Not Isolate action turns off browser isolation for matched traffic. For more information on this action, refer to [Isolation policies](/cloudflare-one/policies/browser-isolation/isolation-policies/#do-not-isolate).

### Do Not Inspect

API value: `off`

{{<details header="Available selectors">}}

**Traffic**

- [Application](#application)
- [Content Categories](#content-categories)
- [Destination Continent IP Geolocation](#destination-continent)
- [Destination Country IP Geolocation](#destination-country)
- [Destination IP](#destination-ip)
- [Domain](#domain)
- [Host](#host)
- [Proxy Endpoint](#proxy-endpoint)
- [Security Risks](#security-risks)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [Source Internal IP](#source-internal-ip)
- [Source IP](#source-ip)
- [Virtual Network](#virtual-network)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

{{<Aside type="warning" header="Visibility limitation">}}

When you create a Do Not Inspect policy for a given hostname, application, or app type, you will lose the ability to log or block HTTP requests, apply DLP policies, and perform AV scanning.

Information contained within HTTPS encryption, such as the full requested URL, will not be visible if it bypasses Gateway inspection. However, you can still apply [network policies](/cloudflare-one/policies/gateway/network-policies/) to this traffic. For more information, refer to [TLS decryption](/cloudflare-one/policies/gateway/http-policies/tls-decryption/).

{{</Aside>}}

Do Not Inspect lets you bypass certain elements from inspection. To prevent Gateway from decrypting and inspecting HTTPS traffic, your policy must match against the Server Name Indicator (SNI) in the TLS header. When accessing a Do Not Inspect site in the browser, your browser may display a **Your connection is not private** warning, which you can proceed through to connect. For more information about applications which may require a Do Not Inspect policy, refer to [TLS decryption limitations](/cloudflare-one/policies/gateway/http-policies/tls-decryption/#inspection-limitations).

All Do Not Inspect rules are evaluated first, before any Allow or Block rules, to determine if decryption should occur. For more information, refer to [Order of enforcement](/cloudflare-one/policies/gateway/order-of-enforcement/#http-policies).

### Do Not Scan

API value: `noscan`

{{<details header="Available selectors">}}

**Traffic**

- [Application](#application)
- [Content Categories](#content-categories)
- [Destination Continent IP Geolocation](#destination-continent)
- [Destination Country IP Geolocation](#destination-country)
- [Destination IP](#destination-ip)
- [Domain](#domain)
- [Host](#host)
- [HTTP Method](#http-method)
- [Proxy Endpoint](#proxy-endpoint)
- [Security Risks](#security-risks)
- [Source Continent IP Geolocation](#source-continent)
- [Source Country IP Geolocation](#source-country)
- [Source Internal IP](#source-internal-ip)
- [Source IP](#source-ip)
- [URL](#url)
- [URL Path](#url-path)
- [URL Path & Query](#url-path-and-query)
- [URL Query](#url-query)
- [Virtual Network](#virtual-network)

**Identity**

- [SAML Attributes](#users)
- [User Email](#users)
- [User Group Emails](#users)
- [User Group IDs](#users)
- [User Group Names](#users)
- [User Name](#users)

**Device Posture**

- [Passed Device Posture Checks](#device-posture)

{{</details>}}

When an admin enables AV scanning for uploads and/or downloads, Gateway will scan every supported file. Admins can selectively choose to disable scanning by leveraging the HTTP rules. For example, to prevent AV scanning of files uploaded to or downloaded from `example.com`, an admin would configure the following rule:

| Selector | Operator      | Value           | Action      |
| -------- | ------------- | --------------- | ----------- |
| Hostname | matches regex | `.*example.com` | Do Not Scan |

When a Do Not Scan rule matches, nothing is scanned, regardless of file size or whether the file type is supported or not.

## Selectors

{{<Aside type="note">}}

Policies created using the URL selector are case-sensitive.

{{</Aside>}}

Gateway matches HTTP traffic against the following selectors, or criteria:

### Application

{{<render file="gateway/selectors/_application.md" withParameters="HTTP">}}

{{<Aside type="warning" header="Multiple API selectors required for Terraform">}}

When using Terraform to create a policy with the [Do Not Inspect](#do-not-inspect) action, you must use the `app.hosts_ids` and `app.supports_ids` selectors. For example, to create a Do Not Inspect policy for Google Cloud Platform traffic, create a policy with both `any(app.hosts_ids[*] in {1245})` and `any(app.supports_ids[*] in {1245})`.

{{</Aside>}}

### Content Categories

| UI name            | API example                                             |
| ------------------ | ------------------------------------------------------- |
| Content Categories | `not(any(http.request.uri.content_category[*] in {1}))` |

For more information, refer to our list of [content categories](/cloudflare-one/policies/gateway/domain-categories/#content-categories).

### Destination Continent

{{<Aside type="note">}}
Only applies to traffic sent through the [WARP client](/cloudflare-one/connections/connect-devices/warp/set-up-warp/#gateway-with-warp-default).
{{</Aside>}}

{{<render file="gateway/selectors/_destination-continent.md" withParameters="http.dst_ip">}}

### Destination Country

{{<Aside type="note">}}
Only applies to traffic sent through the [WARP client](/cloudflare-one/connections/connect-devices/warp/set-up-warp/#gateway-with-warp-default).
{{</Aside>}}

{{<render file="gateway/selectors/_destination-country.md" withParameters="http.dst_ip">}}

### Destination IP

{{<Aside type="note">}}
Only applies to traffic sent through the [WARP client](/cloudflare-one/connections/connect-devices/warp/set-up-warp/#gateway-with-warp-default).
{{</Aside>}}

| UI name        | API example                   |
| -------------- | ----------------------------- |
| Destination IP | `http.dst.ip == "10.0.0.0/8"` |

### Device Posture

{{<render file="gateway/selectors/_device-posture.md">}}

### Domain

Use this selector to match against a domain and all subdomains — for example, if you want to block `example.com` and subdomains such as `www.example.com`.

| UI name | API example                                     |
| ------- | ----------------------------------------------- |
| Domain  | `any(http.request.domains[*] == "example.com")` |

### Download and Upload File Types

{{<Aside type="warning" header="Deprecated selectors">}}

The **Download File Types** and **Upload File Types** selectors supersede the **Download File Type** and **Upload File Type** selectors. Gateway will still evaluate policies with the previous selectors. However, Cloudflare recommends migrating any policies with deprecated selectors to the new corresponding selectors.

{{</Aside>}}

These selectors will scan file signatures in the HTTP body. You can select from file categories or specific file types, including executables, archives and compressed files, Microsoft 365/Office documents, and Adobe files.

| UI name             | API example                                         |
| ------------------- | --------------------------------------------------- |
| Download File Types | `any(http.download.file.types[*] in {"docx" "7z"})` |

| UI name           | API example                                        |
| ----------------- | -------------------------------------------------- |
| Upload File Types | `any(http.upload.file.types[*] in {"compressed"})` |

### Download and Upload Mime Type

These selectors depend on the `Content-Type` header being present in the request (for uploads) or response (for downloads).

| UI name            | API example                          |
| ------------------ | ------------------------------------ |
| Download Mime Type | `http.download.mime == "image/png\"` |

| UI name          | API example                        |
| ---------------- | ---------------------------------- |
| Upload Mime Type | `http.upload.mime == "image/png\"` |

### DLP Profile

Scans HTTP traffic for the presence of social security numbers and other PII. You must configure the DLP Profile before you can use this selector in your policy. For more information, refer to our [DLP Profile](/cloudflare-one/policies/data-loss-prevention/) documentation.

### Host

Use this selector to match only the hostname specified — for example, if you want to block `test.example.com` but not `example.com` or `www.test.example.com`.

| UI name | API example                               |
| ------- | ----------------------------------------- |
| Host    | `http.request.host == "test.example.com"` |

{{<Aside type="note">}}

Some hostnames (`example.com`) will invisibly redirect to the www subdomain (`www.example.com`). To match this type of website, use the [Domain](#domain) selector instead of the Host selector.

{{</Aside>}}

### HTTP Method

| UI name     | API example                    |
| ----------- | ------------------------------ |
| HTTP Method | `http.request.method == "GET"` |

### HTTP Response

| UI name | API example                          |
| ------- | ------------------------------------ |
| URL     | `http.response.status_code == "200"` |

### Proxy Endpoint

{{<render file="gateway/selectors/_proxy-endpoint.md">}}

### Security Risks

| UI name        | API example                                |
| -------------- | ------------------------------------------ |
| Security Risks | `any(http.request.uri.category[*] in {1})` |

For more information, refer to our list of [security categories](/cloudflare-one/policies/gateway/domain-categories/#security-categories).

### Source Continent

The continent of the user making the request.
{{<render file="gateway/selectors/_source-continent.md" withParameters="http.src_ip">}}

### Source Country

The country of the user making the request.
{{<render file="gateway/selectors/_source-country.md" withParameters="http.src_ip">}}

### Source Internal IP

{{<render file="gateway/selectors/_source-internal-ip.md" withParameters="HTTP;;http">}}

### Source IP

| UI name   | API example                   |
| --------- | ----------------------------- |
| Source IP | `http.src.ip == "10.0.0.0/8"` |

### URL

{{<render file="gateway/_url-slash.md">}}

| UI name | API example                                             |
| ------- | ------------------------------------------------------- |
| URL     | `not(any(http.request.uri.content_category[*] in {1}))` |

### URL Path

| UI name  | API example                             |
| -------- | --------------------------------------- |
| URL Path | `http.request.uri.path == \"/foo/bar\"` |

### URL Path and Query

| UI name            | API example                                                     |
| ------------------ | --------------------------------------------------------------- |
| URL Path and Query | `http.request.uri.path_and_query == \"/foo/bar?ab%242=%2A342\"` |

### URL Query

| UI name   | API example                    |
| --------- | ------------------------------ |
| URL Query | `not(http.request.uri in $%s)` |

### Users

{{<render file="gateway/selectors/_users.md">}}

### Virtual Network

{{<render file="gateway/selectors/_virtual-network.md" withParameters="http.conn.vnet_id">}}

## Comparison operators

{{<render file="gateway/_comparison-operators.md">}}

## Value

{{<render file="gateway/_value.md">}}

## Logical operators

{{<render file="gateway/_logical-operators.md" withParameters="**Identity** or **Device Posture**">}}

{{<render file="gateway/_response.md" withParameters="request;;_Source IP_;;a _DLP Profile_">}}