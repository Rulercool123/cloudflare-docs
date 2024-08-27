---
pcx_content_type: how-to
title: Activate Polish
weight: 1
---

# Activate Polish

Images in the [cache must be purged](/cache/how-to/purge-cache/) or expired before seeing any changes in Polish settings.

{{<Aside type="warning">}}

Do not activate Polish and [image transformations](/images/transform-images/) simultaneously. Image transformations already apply lossy compression, which makes Polish redundant.

{{</Aside>}}

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/) and select the account and domain where you want to activate Polish.
2. Go to **Speed** > **Optimization** > **Image Optimization**.
3. Under **Polish**, select _Lossy_ or _Lossless_ from the drop-down menu. [_Lossy_](/images/polish/compression/#lossy) gives greater file size savings.
4. (Optional) Select **WebP**. Enable this option if you want to further optimize PNG and JPEG images stored in the origin server, and serve them as WebP files to browsers that support this format.

To ensure WebP is not served from cache to a browser without WebP support, disable any WebP conversion utilities at your origin web server when using Polish.

{{<render file="_configuration-rule-promotion.md" productFolder="rules">}}