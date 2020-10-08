---
description: Content delivery network for faster agent delivery
---

# Installing from CDN

Fingerprint.js PRO can also use  AWS CloudFront for JavaScript agent delivery. It ensures wide geographic availability and excellent performance as the code is hosted at edge locations around the world.

To start with the CDN version, add this JavaScript snippet as the first element inside the `<head/>`tag on every page where you want to identify visitors. Pass configuration options to control the behavior of data collection. 

```markup
<head>
<script>
  // this code will configure the agent
  // but will not perform actual identification requests.
  window.fpLayer = window.fpLayer || [];
  function fp() { fpLayer.push(arguments); }
  fp('config', 'client', 'your-token');
</script>
<script async src="https://cdn.fpjs.io/@2/fp.js"></script>
</head>
```

### Configuring the agent

Agent configuration is done using the `fp('config', 'parameter', 'value')` syntax inside the snippet.  
In the example above, you can see how this agent was configured to specify the client token on line 7. Other options are also available.

{% hint style="danger" %}
Current version of the CDN script exposes a single **`FP`** global object. This global object is deprecated and will be removed in v3 of the agent. The recommended way to get hold of the **FP** object is via the **`loaded`** callback.
{% endhint %}

### CDN versioning and URL format

Fingerprint.js PRO CDN follows this URL format:

`https://cdn.fpjs.io/`**`:version`**`/fp.js`

**`:version`** is a special directive that can match a **literal** version or a **major** version.  
  
**Literal version**  
If you place a literal version in the URL, this specific version will be returned. 

Example:

`https://cdn.fpjs.io/`**`@2.4.9`**`/fp.js`

**Major version**  
If you use a major version in the URL, the latest actual version will be returned. This actual version will match the major version that you provided. 

Example:

`https://cdn.fpjs.io/`**`@2`**`/fp.js`

This will return the latest actual version based on the major version, e.g. @2.4.9.

{% hint style="info" %}
We recommend using a major version, because it provides an "auto-upgrade" functionality. By using a major version, you don't need to worry about upgrading your script as the upgrade will be performed automatically.
{% endhint %}

