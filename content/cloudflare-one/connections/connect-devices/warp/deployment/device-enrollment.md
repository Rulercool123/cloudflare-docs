---
pcx_content_type: how-to
title: Device enrollment permissions
weight: 3
---

# Device enrollment permissions

Device enrollment permissions determine which users can connect new devices to your organization's Cloudflare Zero Trust instance.

## Set device enrollment permissions

{{<render file="warp/_device-enrollment.md">}}

Users can now [enroll their device](/cloudflare-one/connections/connect-devices/warp/deployment/manual-deployment/) by logging in to your identity provider. To prevent users from logging out of your organization after they enroll, disable [Allow devices to leave organization](/cloudflare-one/connections/connect-devices/warp/configure-warp/warp-settings/#allow-device-to-leave-organization) in your WARP client settings.

## Example policies

### Check for service token

Instead of requiring users to authenticate with their credentials, you can use a [service token](/cloudflare-one/identity/service-tokens/) to enroll devices without any user interaction. Because users are not required to log in to an identity provider, identity-based policies cannot be enforced on these devices.

To enroll devices using a service token:

{{<render file="warp/_service-token-enrollment.md">}}

You can verify which devices have enrolled by going to **My Team** > **Devices**. Devices that enrolled using a service token (or any other Service Auth policy) will have the **Email** field show as `non_identity@<team-name>.cloudflareaccess.com`.

### Check for mTLS certificate

Enterprise customers can enforce [mutual TLS authentication](/cloudflare-one/identity/devices/access-integrations/mutual-tls-authentication/) during device enrollment.

{{<render file="warp/_device-enrollment-mtls.md">}}

When users [log in to your Zero Trust organization](/cloudflare-one/connections/connect-devices/warp/deployment/manual-deployment/) from the WARP client, their device must present a valid client certificate in order to connect.