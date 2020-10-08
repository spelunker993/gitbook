---
description: 'https://en.wikipedia.org/wiki/CNAME_record'
---

# CNAME

**With our CNAME  integration you can greatly improve the accuracy and reliability of your tracking.**

{% hint style="info" %}
CNAME integration is a paid option. For details, contact support@fingerprintjs.com
{% endhint %}

**CNAME integration offers following advantages:**

* Improves Safari identification accuracy
* Extends the identification lifetime in mobile browsers up to one year
* Your tracking is not stopped by Ad blockers
* Impossible to detect that your website is using FingerprintJS Pro

### Who should use it?

We recommend enabling CNAME integration for all our clients, because it significantly increases identification accuracy in Safari browsers. This is particularly important on iPhones, as manual cookie clearing is rare on mobile devices, but [ITP-based lifetime capping is prevalent](https://docs.fingerprintjs.com/pro/best-practices/safari-itp).   
**After implementing this integration, you should observe significantly better identification rates on both desktop and mobile Safari.**

We also recommend CNAME integration if your visitor base has a large percentage of Ad blocking users.

### How it works

It works by adding a subdomain on your website that resolves to a FingerprintJS Pro server. 

As an example, you can create  `metrics.yourwebsite.com` subdomain, that will resolve to one of our servers.  
By adding a DNS record into your DNS configuration, you can continue using our API endpoint, but expose it under your own subdomain.

Example CNAME DNS record:

### `metrics.example.com CNAME example.us-east-1.amazonaws.com`

{% hint style="info" %}
**Note that this server name is an example**. Every actual CNAME integration will have its own, unique target URL.
{% endhint %}

After your dedicated CNAME is created,  you will need to specify your new endpoint in the configuration:

```markup
 <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'token');
    fp('config', 'endpoint', 'https://metrics.example.com');
  </script>
  <script async src="https://cdn.fpjs.io/@2/fp.js"></script>
```

or, if you're using the NPM version:

```typescript
// FP agent configuration step
let fp = await FP.load({ 
  client: "client-token", 
  endpoint: "https://metrics.example.com" 
});
```

When this configuration change is implemented, the FingerprintJS agent will make all requests to `https://metrics.example.com` which will resolve to a FingerprintJS Pro server IP address on a DNS level.

{% hint style="success" %}
CNAME integration is the recommended method of high-performance FingerprintJS Pro integration, as it is not tied to a particular hosting provider or web framework.   
Another option is the CloudFlare integration, which comes next.
{% endhint %}

