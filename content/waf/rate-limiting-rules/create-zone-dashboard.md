---
pcx_content_type: how-to
type: overview
title: Create in the dashboard for a zone
weight: 14
meta:
  title: Create a rate limiting rule in the dashboard for a zone
---

# Create a rate limiting rule in the dashboard for a zone

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/), and select your account and domain.

2. Go to **Security** > **WAF** > **Rate limiting rules**.

3. To create a new empty rule, select **Create rule**. To duplicate an existing rule, select the three dots next to it > **Duplicate**.

    ![The Create rate limiting rule page in the Cloudflare dashboard](/images/waf/custom-rules/rate-limiting-create.png)

4. Enter a descriptive name for the rule in **Rule name**.

5. Under **If incoming requests match**, use the **Field** drop-down list to choose an HTTP property. For each request, the value of the property you choose for **Field** is compared to the value you specify for **Value** using the operator selected in **Operator**.

6. (Optional) Under **Cache status**, disable **Also apply rate limiting to cached assets** to consider only the requests that reach the origin when determining the rate.

7. Under **With the same characteristics**, add one or more characteristics that will define the request counters for rate limiting purposes. Each value combination will have its own counter to determine the rate. Refer to [How Cloudflare determines the request rate](/waf/rate-limiting-rules/request-rate/) for more information.

8. (Optional) To define an expression that specifies the conditions for incrementing the rate counter, enable **Use custom counting expression** and set the expression. By default, the counting expression is the same as the rule expression. The counting expression can include [response fields](/ruleset-engine/rules-language/fields/#http-response-fields).

9. Under **When rate exceeds**, define the maximum number of requests and the time period to consider when determining the rate.

10. Under **Then take action**, select the rule action from the **Choose action** drop-down list. For example, selecting _Block_ tells Cloudflare to refuse requests in the conditions you specified when the request limit is reached.

11. (Optional) If you selected the _Block_ action, you can [configure a custom response](#configure-a-custom-response-for-blocked-requests) for requests exceeding the configured rate limit.

12. Select the mitigation timeout in the **Duration** dropdown. This is the time period during which Cloudflare applies the select action once the rate is reached.

    Enterprise customers with a paid add-on can [throttle requests](/waf/rate-limiting-rules/parameters/#with-the-following-behavior) instead of applying the configured action for a selected duration. To throttle requests, under **With the following behavior** select _Throttle requests over the maximum configured rate_.

13. To save and deploy your rule, select **Deploy**. If you are not ready to deploy your rule, select **Save as Draft**.

## Configure a custom response for blocked requests

{{<render file="_custom-response-blocked-requests.md" withParameters="rate limiting;;429">}}