---
description: >-
  FingerprintJS Pro JavaScript API. Not supported in Node.js, requires Internet
  browser to work.
---

# JavaScript agent

The client-side agent is a high-performance JavaScript agent that collects multiple device and browser signals and sends them to the FingerprintJS Pro API for processing and identification.

### Installing the agent

It's possible to install the agent as an [**NPM**](https://docs.fingerprintjs.com/pro/js-agent/npm) module or load it from the [**CDN**](https://docs.fingerprintjs.com/pro/js-agent/cdn). The installation and configuration steps are slightly different in each case, please refer to the respective documentation pages for more details.

### Configuration parameters

A summary of all the configuration parameters is provided below. You can also find detailed parameter information below.

| **Parameter** | Description |
| :--- | :--- |
| **`client`** | Your browser token that authenticates the agent with the API. |
| **`region`** | Which [region](https://docs.fingerprintjs.com/pro/regions) to use. This parameter is ignored, when`endpoint` parameter is used. |
| **`endpoint`** | Server API URL. Should be only used with [CNAME](https://docs.fingerprintjs.com/pro/integrations/cname)  or [CloudFlare](https://docs.fingerprintjs.com/pro/integrations/cloudflare) integrations. |
| **`cookieDomain`** | Sets the cookie domain. [Should only be used when you use the agent on multiple subdomains.](https://docs.fingerprintjs.com/pro/best-practices/cookie-domain) |

**These configuration parameters are only available in the CDN version:**

| Parameter | Description |
| :--- | :--- |
| **`autoSend`** | When set to `true`, sends identification events automatically on every page load.  |
| **`loaded`** | Called when the agent is fully initialized. Recommended way to get hold of the **`FP`** object. |

**Configuration parameters in more detail**

In general, you should configure the agent, which was loaded from the **CDN,** with the JS snippet. When using the NPM module,  pass configuration parameters directly into the **`FP.load`** method as a plain JavaScript object.

Full list of parameters.

**`client`**  `default: no, required: yes, available: CDN, NPM`

Browser token to authenticate the agent with the server API.

Example usage:

```javascript
// When used in the CDN configuration snippet
fp('config', 'client', 'dXk12f271339d9ag93d');

// When used as an NPM module (ES6)
let fp = await FP.load({ client: "dXk12f271339d9ag93d" })
```

**`autoSend`**  `default: false, available: CDN`

Controls the [mode of data sending](https://docs.fingerprintjs.com/pro/js-agent#sending-data). If set to `true`, the sending of the data is made automatically on every page load. Only available in the CDN version of the agent.

```javascript
// CDN configuration snippet
fp('config', 'autoSend', true);
```

If set to `false`,  the data is not sent automatically; you need to send it yourself in your JavaScript code.

```javascript
fp('config', 'autoSend', false);
```

Since it's `false` by default, you don't need to add the above line to the agent configuration.

**`region`**`default: 'us' (for CDN), required: true (for NPM), available: CDN, NPM`

Available values: **`us`** and **`eu`**

The [region](https://docs.fingerprintjs.com/pro/regions) of the FingerprintJS Pro  servers you want to send data to. The default is `us`, which sends data to the N. Virginia server location. This parameter is ignored when `endpoint` parameter is used.

Examples:

```javascript
// When used in the CDN configuration snippet
fp('config', 'region', 'eu');

// When used in the NPM Module (ES6)
let fp = await FP.load({region: "us"})
```

**`endpoint`**`default: no, requied: false, available: CDN, NPM`

This parameter should only be used with the [CNAME](https://docs.fingerprintjs.com/pro/integrations/cname) or [CloudFlare](https://docs.fingerprintjs.com/pro/integrations/cloudflare) integration. Specify your custom endpoint here. 

**`loaded`** `default: no, required: false, available: CDN`

Use this configuration parameter to be notified when the JavaScript agent is fully loaded from the CDN and initialized. You can use the callback parameter to get hold of the **`FP`** object and interact with it.

Example:

```javascript
// Only available in the CDN version
fp('config', 'loaded', function(fp){ 
  fp.send().then(function(response) { /* do something */ });
}));
// note that the `loaded` callback will be executed as soon as 
// the agent is ready to process requests.
// This is the recommended way to start working with the FP object 
// as soon as possible, when you loaded the script from the CDN.
```

### Sending data

There are two modes of sending data: automatic and manual. 

#### Automatic mode \(available only in CDN version\).

Full JS snippet example.

```markup
<script>
  window.fpLayer = window.fpLayer || [];
  function fp() { fpLayer.push(arguments); }
  fp('config', 'client', 'your-browser-token');
  // This configuration parameter will send the data automatically
  // on every page view.
  fp('config', 'autoSend', true);
</script>
<script async src="https://cdn.fpjs.io/@2/fp.js"></script>
```

In many cases you will use the automatic mode, because it's the simplest way to get started. Add the snippet to every page's `<head/>` tag and configure it to send data automatically with: 

```javascript
fp('config', 'autoSend', true);
```

You can see in the snippet example above how automatic configuration works.   
When in automatic mode, the agent will send the data on every page load. You don't need to do anything else.

#### Manual \(available in both CDN and NPM versions\).

Manual mode provides more granular control over how the identification requests are processed. If you plan to send the data manually \(on an Ad click for example\), you **don't need** to include the below line in the snippet:

```javascript
fp('config', 'autoSend', true);
```

When you actually need to send the data, use the `FP` **** object to send the data. Once you obtain the `FP` object, you can call the `send` method with optional parameters to send the identification events.  
More example are available below in API reference section.

You can use both `async/await` and promises to send the data.    
Example of `async/await`

```typescript
// NPM version: sending with async/await
import { FP } from "@fp-pro/client"
let fp = await FP.load({client: "your-browser-token", region: "us"})
let result = await fp.send()
```

Example of sending with promises.

```javascript
// NPM version: sending with async/await
import { FP } from "@fp-pro/client"
FP.load({client: "your-browser-token", region: "us"})
  .then(fp => {
    fp.send().then(res => console.log(res));
  })

```

### Receiving data

You can configure FingerprintJS Pro to receive the data in the browser \(via a callback\), in the server \(via a [webhook](../webhooks.md)\) or both.

#### Receiving via a callback

JavaScript agent has a promise-based API that you can use to chain callbacks. Note that the promise implementation is `polyfilled` in older browsers so that the call API is consistent everywhere.

```javascript
// Receive data via a callback
FP.send().then(function(response){
  console.log(response.visitorId);
});
```

Two types of responses are supported:  
`default` and `callbackData`. You don't need to pass any parameters to get the `default` response. `callbackData` is an extended response format and can be requested using the `callbackData: true`parameter.

Response object format is provided below. Every request has a unique `requestID` that is returned in the response and is also available in API to query later. It is a convenient way to get specific identification events in the API.

{% tabs %}
{% tab title="default" %}
```javascript
fp.send()...
// response:
{
  // requestId is different for every request
  // it's a request identifier
  "requestId": "8nbmT18x79m54PQ0GvPq",
  // visitorId is the main browser identifier
  "visitorId": "2JGu1Z4d2J4IqiyzO3i4",
  // if true, this visitor was found and visited before
  // if false, this visitor wasn't found and probably didn't visit before
  "visitorFound": true
}
```
{% endtab %}

{% tab title="callbackData" %}
```javascript
fp.send({callbackData: true})...
// response:
{
  "requestId": "8nbmT18x79m54PQ0GvPq",
  "visitorId": "2JGu1Z4d2J4IqiyzO3i4",
  "visitorFound": true,
  "ip": "185.230.125.20",
  "ipLocation": {
    "accuracyRadius": 10,
    "latitude": 47.3925,
    "longitude": 8.4546,
    "postalCode": "8010",
    "timezone": "Europe/Zurich",
    "city": {
      "name": "Zurich"
    },
    "continent": {
      "code": "EU",
      "name": "Europe"
    },
    "country": {
      "code": "CH",
      "name": "Switzerland"
    },
    "subdivisions": [
      {
        "isoCode": "ZH",
        "name": "Zurich"
      }
    ]
  },
  "browserName": "Chrome",
  "browserVersion": "75.0.3770",
  "os": "Mac OS X",
  "osVersion": "10.14.5",
  "device": "Other",
  "botProbability": 0
}
```
{% endtab %}
{% endtabs %}

#### Send error handling

JavaScript agent `send` method returns a promise which will be rejected in case of an error.  
Below table summarizes possible types of errors that can lead to response promise rejections.

| Reason | Response example | HTTP Status |
| :--- | :--- | :--- |
| Connection error | `{"error": "Connection error"}` | -- |
| Token missing | `{"visitorId":"n/a","reason":"Token required"}` | 401 |
| Token invalid | `{"visitorId":"n/a","reason":"Token not found"}` | 403 |
| Bad JSON | `{"visitorId":"n/a", "reason": "Request cannot be parsed"}` | 422 |
|  |  |  |

The connection error occurs when the agent fails to connect to the server API. This can happen during a network outage or if a browser plugin blocks networking requests \(e.g. AdBlock\).

Other types of response errors are possible, but they don't lead to the promise rejections, instead, they resolve the promise, but contain a `n/a` visitorId and a reason why the `visitorId` value is not available.

| Reason | Response example | HTTP Status |
| :--- | :--- | :--- |
| Trial expired | `{"visitorId":"n/a","reason":"Token expired"}` | 200 |
| General request failure | `{"visitorId":"n/a","reason":"Request failed"}` | 200 |
| Server request times out | `{"visitorId":"n/a","reason":"Request failed to process"}` | 200 |

```javascript
// example of a rejected promise.
fp.send().then(function(goodResponse){...}).catch(function(badResponse){
  console.log(badResponse.reason);
});

// Note that you can use `finally` to be notified in case of
// both resolved or rejected promise.
fp.send().finally(function(res){
  console.log("Will be called in any case, response can be valid or missing");
});
```

### **Timeouts**

Two types of timeouts are possible: a **server** timeout and a **client** timeout.

#### Server timeout

The server timeout is fixed at 10 seconds of server-side processing time. If server-side processing exceeds 10 seconds for any reason, you will receive this response:

`{"visitorId":"n/a","reason":"Request failed to process"}`

The promise will not be rejected.

#### Client timeout

Client timeout controls total time \(both client-side and server-side\) that any identification event is allowed to run. By default it's 10 seconds. Note that even if the client-side timeout is exceeded, the server-side request can still be running, its results will just be discarded.

### Search bots

The PRO API recognizes most well-known search bots \(Google, Bing, Yahoo, Baidu, DuckDuckGo, and others\) and treats them differently.   
Whenever a page is loaded by a search bot, an identification event is processed server-side, like it would  with a normal visitor. The response will look as follows:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Response Example</th>
      <th style="text-align:left">HTTP Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">
        <p><code>{</code>
        </p>
        <p><code>  &quot;visitorId&quot;: &quot;n/a&quot;,</code>
        </p>
        <p><code>  &quot;reason&quot;: &quot;Not available for crawl bots&quot;</code>
        </p>
        <p><code>}</code>
        </p>
      </td>
      <td style="text-align:left">403</td>
    </tr>
  </tbody>
</table>

Note that the response promise will be rejected for a search bot.

### Missing User-Agent header

Missing `User-Agent` header will lead to a rejected response promise. The response format is:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Response Example</th>
      <th style="text-align:left">HTTP Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">
        <p><code>{</code>
        </p>
        <p><code>  &quot;visitorId&quot;: &quot;n/a&quot;,</code>
        </p>
        <p><code>  &quot;reason&quot;: &quot;Not available when User-Agent is unspecified&quot;</code>
        </p>
        <p><code>}</code>
        </p>
      </td>
      <td style="text-align:left">403</td>
    </tr>
  </tbody>
</table>

### Rate limiting

Every account has a default rate limit of 10 requests / second. If you need to increase that value, email as at `support@fingerprintjs.com`. Whenever that rate limit is exceeded, a request is throttled and a special response is returned.   
Throttled response example:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Response Example</th>
      <th style="text-align:left">HTTP Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">
        <p><code>{</code>
        </p>
        <p><code>  &quot;visitorId&quot;: &quot;n/a&quot;,</code>
        </p>
        <p><code>  &quot;reason&quot;: &quot;Too many requests, rate limit exceeded&quot;</code>
        </p>
        <p><code>}</code>
        </p>
      </td>
      <td style="text-align:left">429</td>
    </tr>
  </tbody>
</table>

### API reference

Generally, you need to use the **`FP`**  object to interact with the JS agent API. 

Example of using the FP object \(NPM version\): 

```javascript
import { FP } from "@fp-pro/client"
let fp = await FP.load({client: "your-token", region: "us"})
let result = await fp.send()
```

Example of using the **`FP`** object \(CDN version\):

```markup
<head>
  <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-token');
    fp('config', 'loaded', function(fp) {
       fp.send().then(function (res) {
        console.log(res);
      });
    });
  </script>
  <script async src="https://cdn.fpjs.io/@2/fp.js"></script>
</head>

<body>
  ...
</body>
```

**API methods**

The only way you can interact with the client-side agent is via **FP** object. You can obtain this object from the `loaded` configuration callback parameter in [CDN version](https://docs.fingerprintjs.com/pro/js-agent/cdn) or import it if you're using the [NPM version](https://docs.fingerprintjs.com/pro/js-agent/npm).  
**FP** object has only one method: **`send`**.

#### **`send(options)`**

{% hint style="info" %}
Use **`send`** to send data. All options are optional. Available in CDN and NPM versions. Can be used with promises or `async/await`.

A summary of options is provided below.
{% endhint %}

{% tabs %}
{% tab title="tag" %}
**`tag`** is a user-provided value or an object that will be returned back to you in a callback or in a webhook message. You may want to use the `tag` value to be able to associate a server-side webhook event with a web request of your current visitor.

What values can be used as a `tag`?   
Anything that identifies a visitor or a request.

You can pass the `requestId` as a `tag` and then get this `requestId`back in the webhook, associated with a `visitorID`.

Examples of using the `tag` option:

```javascript
// simple, scalar values
var requestId = 2718281828;
fp.send({tag: requestId});
// or a string value
var requestType = "signup";

// Example of sending and receiving the tag value
fp.send({tag: requestId}).then(function(data) { 
  console.log(data);
});
// Promise {<pending>}
{visitorId: "nTxJ28Gt4CegeAwdWELL", tag: {tag: 2718281828}}
// Note that tags are always objects, even when you use
// a regular scalar value. 
// If you provid a scalar value, it will converted to an object
// with `tag` attribute.
```

```javascript
// You can also use a JavaScript object as a tag value 
// and it will be returned back to you 
// in the callback or in a webhook.
// 
// NOTE: nested objects are not supported in tag objects.
var requestMetadata = {
  requestID: 2718281828, 
  requestType: "signup"
};
// Making an API call and receiving a response via a promise
fp.send({tag: requestMetadata}).then(function(data) { 
  console.log(data);
});
// Promise {<pending>}
{
  visitorId: "nTxJ28Gt4CegeAwdWELL", 
  tag: {requestID: 2718281828, requestType: "signup"}
}
```

`FP.send` will return a [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)  so you can be notified when  sending is complete.
{% endtab %}

{% tab title="linkedId" %}
`linkedId` is a way of linking current identification event with a custom identifier. This can be helpful to be able to filter [API](../server-api.md) visit information later.

Some examples of how you can use the `linkedId:`

1. Link your website `userID` with a FPJS `visitorID` to be able to analyze this data.
2. Link your e-commerce `orderID` with a FPJS `visitorID` to be able to see the orders a visitor made.
3. Link a survey ID with a `visitorID` to ensure that the same visitor does not participate in a single survey multiple times.
4. Limit one visitor per item or one visitor per discount coupon.

```javascript
// Making an API call and receiving a response via a promise
// Sending a linkedId value is helpful to filter by this value via server API
var orderId = 3936532456;
fp.send({linkedId: orderId}).then(function(data) { 
  console.log(data);
});
// linkedId will be saved with this event and will be available to be 
// queried later.
```

A `linkedId` looks similar to `tag`. When should you use each?  
A `tag` is an object that is "passed-through" and sent back to your webhook endpoint. A tag can contain an arbitrary number of attributes of any type \(except for nested objects\). You can use tags to store them in your database and use for any purpose. `Tag` objects are meant to be consumed and processed by you eventually. You can think of a `tag` object as metadata.  
A `linkedId` is a scalar, string identifier that gets indexed by FPJS platform and can be used to filter visits efficiently. It's a way to create a custom index on your data and use that index to efficiently filter visits when querying them through the [API](../server-api.md). Objects or non-string values are not supported.
{% endtab %}

{% tab title="ip" %}
`ip` is an option that controls the [geolocation accuracy](../geolocation.md). 
{% endtab %}

{% tab title="callbackData" %}
Usage example:  
  
`fp.send({callbackData: true})`  
  
This parameter is used to request extended information in a JS callback response.  
  
Example of such response:

```javascript
{
  "visitorId": "2JGu1Z4d2J4IqiyzO3i4",
  "visitorFound": true,
  "ip": "185.230.125.20",
  "ipLocation": {
    "accuracyRadius": 10,
    "latitude": 47.3925,
    "longitude": 8.4546,
    "postalCode": "8010",
    "timezone": "Europe/Zurich",
    "city": {
      "name": "Zurich"
    },
    "continent": {
      "code": "EU",
      "name": "Europe"
    },
    "country": {
      "code": "CH",
      "name": "Switzerland"
    },
    "subdivisions": [
      {
        "isoCode": "ZH",
        "name": "Zurich"
      }
    ]
  },
  "browserName": "Chrome",
  "browserVersion": "75.0.3770",
  "os": "Mac OS X",
  "osVersion": "10.14.5",
  "device": "Other",
  "botProbability": 0
}
```
{% endtab %}

{% tab title="timeout" %}
Controls client-side timeout. Client timeout controls total time \(both client-side and server-side\) that any identification event is allowed to run. By default it's 10 seconds. You can control the client-side timeout using `timeout` configuration key. The `timeout` option is in milliseconds.  Example usage:

```javascript
// A timeout of 20 seconds
// An example of the client-side timeout handling
fp.send({ timeout: 20000 })
  .then(function(){...})
  .catch(function(err){
    if (err.error === FP.TimeoutError) {
      console.log("A timeout of 20 seconds exceeded");
    }
  });
```
{% endtab %}
{% endtabs %}

#### Supported browsers

* **IE** 10, 11
* **Edge**  last 3 major versions
* **Firefox** last 3 major versions
* **Chrome** last 3 major versions
* **Safari** last 3 major versions
* **Samsung Internet** last 3 major versions
* **Brave**

