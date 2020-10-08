---
description: This integration is currently in beta. Client agent 2.5.1 or later required.
---

# CloudFlare Integration 🌤️

### Why

**With CloudFlare worker integration you can greatly increase your Safari identification accuracy rates and remain undetected by ad blockers.**

It will also make it impossible to detect that your website is using FingerprintJS Pro.

### Who should use it?

If you're using CloudFlare, you should use this integration because it significantly increases the identification accuracy in Safari browsers. This is particularly important on iPhones, as manual cookie clearing is rare on mobile devices, but [ITP-based lifetime capping is prevalent](https://docs.fingerprintjs.com/pro/best-practices/safari-itp).   
**After implementing this integration, you should observe significantly better identification rates on both desktop and mobile Safari.**

You should also use it if your visitor base has a large percentage of ad blocking users.

Even if you are not using CloudFlare, the benefits this integration provides alone should make you want to start using CloudFlare with your website.

### How it works

It works by designating a path on your website that is processed by a [CloudFlare worker](https://workers.cloudflare.com/). Note that only this particular path is processed by the worker, the rest of your website is not affected. CloudFlare worker code is **100% open-source and fully open for review**. The worker code is [hosted on GitHub.](https://github.com/fingerprintjs/cloudflare-worker)

As an example, you can create a worker, that's assigned to `yourwebsite.com/`**`metrics`** path. All HTTP requests, sent to **`/metrics`**, will be passed through the worker.   
Worker code will call the Fingerprint.js PRO API and set a 1st-party cookie \(Secure and HttpOnly\) with the `VisitorID` from the API response. This will guarantee, that the cookie value is not affected by ITP and can be used for identification safely for a period of up to 1 year. All subsequent requests to **`/metrics`** will have the cookie attached, which will be read by the worker and passed along to the Fingerprint.js PRO API for identification.

### Installation \(command line\)

This command-line guide will use the official CloudFlare [Wrangler CLI.](https://developers.cloudflare.com/workers/tooling/wrangler/)

Start by cloning the project from GitHub: 

```bash
git clone git clone git@github.com:fingerprintjs/cloudflare-worker.git
```

Go into the cloned project directory: `cd cloudflare-worker`

Copy the `wrangler.toml.example` to `wrangler.toml`

```bash
cp wrangler.toml.example wrangler.toml
```

`wrangler.toml` is a file that contains all worker settings. 

Now you need to replace some values in `wrangler.toml`

* `name` the name of the worker. Give it a clear and descriptive name, e.g. `fpjs-metrics`
* `account_id` use the CloudFlare account ID found in [your dashboard](https://dash.cloudflare.com/).
* `zone_id` use the CloudFlare zone ID found in [your dashboard](https://dash.cloudflare.com/).
* `workers_dev` set to `true` if you want to have a development version of the worker.
* \[env.production\] `route` replace **example.com** and **/metrics** with your website and the route that you want to use. You can use any route name: **`/metrics`** is a good default value.

Now that you have configured the worker in `wrangler.toml`, you need to authenticate with CloudFlare.  
Head to the API tokens page in your CloudFlare dashboard and create a new token using **`Edit Cloudflare Workers`** template.

Grab the token that you just generated and authenticate wrangler with it:

```bash
wrangler config
Enter API token: 
```

Now you're ready to publish your worker:

```text
wrangler publish --env production
```

You can now go to your CloudFlare dashboard \(workers tab\) and see your worker successfully registered. All the traffic that goes to **`/metrics`** from the Fingerprint.js PRO agent will be routed to the Fingerprint.js PRO API by the worker code.

#### JavaScript agent configuration

The last step is to change your JavaScript agent configuration. You need to specify the new route that you just registered with the worker.  
  
For CDN version, add a new `endpoint` configuration parameter:

```markup
 <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-token');
    fp('config', 'endpoint', '/metrics');
  </script>
  <script async src="https://cdn.fpjs.io/@2/fp.js"></script>
```

For NPM version, specify the `endpoint` parameter:

```typescript
let fp = await FP.load({ client: "client-token", endpoint: "/metrics" });
```

That's all you need to do to implement the CloudFlare integration. After that, the requests to your new endpoint will return a `__cflvid` cookie \(HttpOnly and Secure\), which will help identification accuracy.

