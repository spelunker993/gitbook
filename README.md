---
description: Fingerprint.js PRO introduction and quick start examples.
---

# Introduction

## Fingerprint.js PRO is the ultimate fraud detection API for your business

Fingerprint.js PRO is a combination of a JavaScript agent that runs in the browser and a server-side storage and API system that securely identifies visitors and stores all the information you need to detect fraud.

### How it works

To integrate Fingerprint.js PRO with your website, you need to add a JavaScript snippet to your webpages.   
Once the snippet is added, you will start receiving the identification information via webhooks \(on your servers\) or callbacks \(in the browser\). The format of identification messages is explained in [webhooks section.](webhooks.md) Webhooks are delivered to your servers immediately after an identification event occurs. It's also possible to query the events later using our [API](server-api.md). 

### JavaScript agent

Fingerprint.js PRO does not calculate a fingerprint in the browser. Instead, it uses a lightweight JavaScript agent that collects multiple device signals and sends them to our servers. This helps prevent reverse engineering and spoofing of an identifier by advanced bots. The agent is hosted at edge locations around the world. It is only 8KB in size and always 20 ms away from your users.

### Server-side identification system

Server-side identification system provides a platform that  processes and stores page views and events   to identify your website  visitors. It is currently available in 2 regions: US east \(N.Virginia\) an EU central \(Frankfurt\). It also provides many helpful features that are explained in more detail on dedicated documentation pages.

### Business scenarios where Fingerprint.js PRO can help

**Catch bots trying to automatically input many usernames and passwords into your login forms.**  
Every visitor gets their unique `visitorID` value, which is calculated from multiple browser and device signals, including fingerprints. You can use that value to see if the current visitor is a bot and if it had visited your website before.

**Confirm that someone buying an item is not the same user as someone a week ago that purchased an item with a stolen credit card. This time they think that with a different email address and IP you won’t catch them, but we know they have the same visitorID.**  
You can tag every visitor with custom data to be able to identify them later. You can also tell us \(through an API call\) that this visitor is a bad actor and be alerted when this visitor opens your website in the future.

**Catch users submitting fake forms and fake clicks.**  
Identify such users with a `visitorID`. Tag them and be alerted when they visit you again. Make sure you're protected from click farms and bots.

#### Build a metered paywall for premium content.

Make your premium content protected with a metered paywall. Allow only X number of reads per a period of time. A paywall demo can be seen here: [http://paywall.fpjs.pro/](http://paywall.fpjs.pro/) \(GitHub repository: [https://github.com/fpjspro/paywall-demo](https://github.com/fpjspro/paywall-demo)\).

### Quick start JavaScript examples

{% hint style="info" %}
These examples can help you get started quickly. For full information, visit the [JS agent documentation.](js-agent.md) If you would like a free 14 day trial, send an email to `support@fingerprintjs.com`
{% endhint %}

You should start by adding the JavaScript snippet to your `<head/>` tag and configuring the agent with `fp('config'...` calls.

```markup
<head>
 <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-token');
    fp('config', 'autoSend', true);
  </script>
  <script async src="https://cdn.fpjs.io/@2/fp.js"></script>
</head>
<body>
..
</body>
```

{% hint style="info" %}
If you're testing from EU, add this configuration line:
{% endhint %}

```javascript
// this will use the EU central (Frankfurt) region
fp('config', 'region', 'eu');
```

### Sending data on user actions

```markup
<head>
  <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-token');
  </script>
  <script async src="https://cdn.fpjs.io/@2/fp.js"></script>
</head>


<body>
  <button id="login-btn">Sign up</button>
  <script>
    document.getElementById("login-btn").addEventListener("click", function () {
      FP.send().then(function (res) {
        console.log(res);
      });
    });
  </script>
</body>
```

### Sending data on page load and getting results back

```markup
<head>
  <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-token');
    fp('config', 'loaded', function (fp) {
      fp.send().then(function (data) {
        console.log(data);
      });
    });
  </script>
  <script async src="https://cdn.fpjs.io/@2/fp.js"></script>
</head>
```

### Sending data and getting the results back on user actions

```markup
<head>
  <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-token');
  </script>
  <script async src="https://cdn.fpjs.io/@2/fp.js"></script>
</head>

<body>
  <button id="login-btn">Sign up</button>
  <script>
    document.getElementById("login-btn").addEventListener("click", function () {
      FP.send().then(function (res) {
        console.log(res);
      });
    });
  </script>
</body>
```

