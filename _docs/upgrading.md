---
layout: docs
title: Upgrading
permalink: /docs/upgrading/
---

<div class="note">
  <h5>Stay Up to Date</h5>
  <p>We recommend you update your lancache.net containers regularly to benefit from
  the latest bug fixes.
  </p>
</div>

## Minor Updates

If you followed our setup recommendations and installed via [docker compose](/docs/installation/docker-compose), run `docker compose pull` to update the containers and then `docker compose up -d` to update to the latest versions.

If you are using docker directly you will need to destroy and recreate your containers after repulling the latest image {% comment %}(See [docker-101](/docs/docker-101)) {% endcomment %}.

## Major Updates

Currently there are no breaking changes between major versions of any of our containers and you can recreate in place. However the caches stores of monolithic & generic are incompatible so if you are switching cache engine you will need to delete and recreate your cache store.

## Cache Domain Updates

The lancache-dns container pulls the latest list of domain/host names to intercept from [cache-domains](https://github.com/uklans/cache-domains) every time it starts. Any new CDNs or updated entries will automatically be updated into your container.  If you choose to use your own custom DNS service you will need to take care of these updates manually.