---
description: Fingerprint.js PRO JavaScript API. Not supported in Node.js
---

# JavaScript agent

The client-side agent is a high-performance fingerprinting agent that collects multiple device and browser signals and sends them to the Fingerprint.js PRO servers for processing and identification. In addition to fingerprinting, it also maintains a unique and persistent `visitorID` value.

### Installing the agent

Add this JavaScript snippet as the first element inside the `<head/>`tag on every page where you want to identify visitors. Pass configuration options to control the behavior of data collection. 

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

Agent configuration is done using the `fp('config', 'parameter', 'value')` syntax.

**Configuration parameters**

A summary of all the configuration parameters is provided below. You can find detailed examples below.

| **Parameter** | Description |
| :--- | :--- |
| **`client`** | Your JS token  that authenticates the agent with the back-end |
| **`region`** | Which [region](https://docs.fingerprintjs.com/pro/regions) to use |
| **`autoSend`** | When set to true, sends identification events automatically on page load |
| **`loaded`** | Callback function called when the agent is fully initialized |

**Configuration parameters in more detail**

**`client`**  `(default: none)`

Authenticates the agent with the back-end.

Example usage:

```javascript
fp('config', 'client', 'dXk12f27');
```

**`autoSend`**  `(default: false)`

Controls the [mode of data sending](https://docs.fingerprintjs.com/pro/js-agent#sending-data). If set to `true`, the sending of the data is made automatically on page load. 

```javascript
fp('config', 'autoSend', true);
```

If set to `false`,  the data is not sent automatically; you need to send it yourself in your JavaScript code.

```javascript
fp('config', 'autoSend', false);
```

Since it's `false` by default, you don't need to add the above line to the agent configuration.

**`region`**`(default: 'us')`

Available values: `us` and `eu`

The region of the Fingerprint.js PRO  servers you want to send data to. The default is `us`, which sends data to the N. Virginia server location.

Examples:

```javascript
fp('config', 'region', 'eu');
```

**`loaded`** `(no default)`

Use this configuration parameter to be notified when the JavaScript agent is fully initialized. You can use the callback parameter to get hold of the **`FP`** object instance and interact with it.

Example:

```javascript
fp('config', 'loaded', function(fp){ 
  fp.send();
}));
// note that the callback will be executed as soon as 
// the agent is ready to process requests.
// This is the recommended way to start working with the FP object 
// as soon as possible.
```

### Sending data

There are two modes of sending data: automatic and manual. 

#### Automatic

```markup
<script>
  window.fpLayer = window.fpLayer || [];
  function fp() { fpLayer.push(arguments); }
  fp('config', 'client', 'your-token');
  fp('config', 'autoSend', true);
</script>
<script async src="https://cdn.fpjs.io/@2/fp.js"></script>
```

In many cases you will use the automatic mode. Add the snippet to every page's `<head/>` tag and configure it to send data automatically with: 

```javascript
fp('config', 'autoSend', true);
```

You can see in the snippet example above how automatic configuration works.   
When in automatic mode, the agent will send the data on every page load. You don't need to do anything else.

#### Manual

Manual mode provides more granular control over how the identification requests are processed. If you plan to send the data manually \(on an Ad click for example\), you **don't need** to include the below line in the snippet:

```javascript
fp('config', 'autoSend', true);
```

When you actually need to send the data, use the **`FP`**  object instance that is passed to you in the **`loaded`** callback. Once you obtain the `fp` object, you can call the `send` method with optional parameters to send the identification events.

### Receiving data

You can configure Fingerprint.js PRO to receive the data in the browser \(via a callback\), in the server \(via a [webhook](webhooks.md)\) or both.

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

Response object format

{% tabs %}
{% tab title="default" %}
```javascript
FP.send()...
// response:
{
  "visitorId":"2JGu1Z4d2J4IqiyzO3i4",
  "visitorFound":true
}
```
{% endtab %}

{% tab title="callbackData" %}
```javascript
FP.send({callbackData: true})...
// response:
{
  "visitorId": "2JGu1Z4d2J4IqiyzO3i4",
  "visitorFound": true,
  "fingerprint": "20190501.7adfeac68fcacb524dc0a69a49f2c325",
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

#### Callback error handling

JavaScript agent `send` method returns a promise which may be rejected in case of a server-side error.  
Below table summarizes possible types of errors that can lead to response promise rejections.

| Reason | Response example | HTTP Status |
| :--- | :--- | :--- |
| Token missing | `{"visitorId":"n/a","reason":"Token required"}` | 401 |
| Token invalid | `{"visitorId":"n/a","reason":"Token not found"}` | 403 |
| Bad JSON | `{"visitorId":"n/a", "reason": "Request cannot be parsed"}` | 422 |
|  |  |  |

Other types of response errors are possible, but they don't lead to the promise rejections, instead, they resolve the promise, but contain a `n/a` visitorId and a reason why the `visitorId` value is not available.

| Reason | Response example | HTTP Status |
| :--- | :--- | :--- |
| Trial expired | `{"visitorId":"n/a","reason":"Token expired"}` | 200 |
| General request failure | `{"visitorId":"n/a","reason":"Request failed"}` | 200 |
| Server request times out | `{"visitorId":"n/a","reason":"Request failed to process"}` | 200 |

```javascript
// example of a rejected promise.
FP.send().then(function(goodResponse){...}).catch(function(badResponse){
  console.log(badResponse.reason);
});

// Note that you can use `finally` to be notified in case of
// both resolved or rejected promise.
FP.send().finally(function(res){
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

Client timeout controls total time \(both client-side and server-side\) that any identification event is allowed to run. By default it's 5 seconds. Note that even if the client-side timeout is exceeded, the server-side request can still be running, its results will just be discarded.

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

Every account has a default rate limit of 10 requests / second. If you need to extend that value, email as at `support@fingerprintjs.com`. Whenever that rate limit is exceeded, a request is throttled and a special response is returned.   
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

Generally, you need to use the **`FP`** global object to interact with the JS agent API. 

Example of using the **`FP`** global object:

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
      // FP global object only becomes available
      // when the JS agent is fully initialized
      FP.send().then(function (res) {
        console.log(res);
      });
    });
  </script>
</body>
```

Alternatively, you can use the `loaded` configuration callback to get hold of the **`FP`** object instance via a callback parameter when the agent is ready.

Example of using the `loaded` callback:

```markup
<head>
  <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-token');
    fp('config', 'loaded', function (fp) {
      // fp is an FP object you should interact with
      fp.send().then(function (data) {
        console.log(data);
      })
    });
  </script>
  <script async src="https://cdn.fpjs.io/@2/fp.js"></script>
</head>
```

**API methods**

Currently, the only way you can interact with the client-side agent is via **FP** object. You can obtain this object from the `loaded` configuration callback parameter. It is also avaialble globally as `FP` after the agent is fully loaded.   
**FP** object has only one method: `send`.

#### **`send(options)`**

{% hint style="info" %}
Use **`send`** to send data. All options are optional.

A summary of options is provided below.
{% endhint %}

{% tabs %}
{% tab title="tag" %}
**`tag`** is a user-provided value or an object that will be returned back to you in a callback or in a webhook message. You may want to use the `tag` value to be able to associate a server-side webhook event with a web request of your current visitor.

What values can be used as a `tag`?   
Anything that identifies a visitor or a request.

You can pass the `requestId` as a `tag` and then get this `requestId`back in the webhook, associated with a `visitorID`.

Examples of using the `tag` option:

{% code title="values" %}
```javascript
// simple, scalar values
var requestId = 2718281828;
FP.send({tag: requestId});
// OR, a string value
var requestType = "signup";

// Example of sending and receiving the tag value
FP.send({tag: requestId}).then(function(data) { 
  console.log(data);
});
// Promise {<pending>}
{visitorId: "nTxJ28Gt4CegeAwdWELL", tag: {tag: 2718281828}}
// Note that tags are always objects, even when you use
// a regular scalar value. 
// If you provid a scalar value, it will converted to an object
// with `tag` attribute.
```
{% endcode %}

{% code title="objects" %}
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
FP.send({tag: requestMetadata}).then(function(data) { 
  console.log(data);
});
// Promise {<pending>}
{
  visitorId: "nTxJ28Gt4CegeAwdWELL", 
  tag: {requestID: 2718281828, requestType: "signup"}
}
```
{% endcode %}

`FP.send` will return a [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)  so you can be notified when  sending is complete.
{% endtab %}

{% tab title="linkedId" %}
`linkedId` is a way of linking current identification event with a custom identifier. This can be helpful to be able to filter [API](server-api.md) visit information later.

Some examples of how you can use the `linkedId:`

1. Link your website `userID` with a FPJS `visitorID` to be able to analyze this data.
2. Link your e-commerce `orderID` with a FPJS `visitorID` to be able to see the orders a visitor made.
3. Link a survey ID with a `visitorID` to ensure that the same visitor does not participate in a single survey multiple times.
4. Limit one visitor per item or one visitor per discount coupon.

```javascript
// Making an API call and receiving a response via a promise
// Sending a linkedId value is helpful to filter by this value via server API
var orderId = 3936532456;
FP.send({linkedId: orderId}).then(function(data) { 
  console.log(data);
});
// linkedId will be saved with this event and will be available to be 
// queried later.
```

A `linkedId` looks similar to `tag`. When should you use each?  
A `tag` is an object that is "passed-through" and sent back to your webhook endpoint. A tag can contain an arbitrary number of attributes of any type \(except for nested objects\). You can use tags to store them in your database and use for any purpose. `Tag` objects are meant to be consumed and processed by you eventually. You can think of a `tag` object as metadata.  
A `linkedId` is a scalar, string identifier that gets indexed by FPJS platform and can be used to filter visits efficiently. It's a way to create a custom index on your data and use that index to efficiently filter visits when querying them through the [API](server-api.md). Objects or non-string values are not supported.
{% endtab %}

{% tab title="ip" %}
`ip` is an option that controls the [geolocation accuracy](geolocation.md). 
{% endtab %}

{% tab title="callbackData" %}
Usage example:  
  
`FP.send({callbackData: true})`  
  
This parameter is used to request extended information in a JS callback response.  
  
Example of such response:

```javascript
{
  "visitorId": "2JGu1Z4d2J4IqiyzO3i4",
  "visitorFound": true,
  "fingerprint": "20190501.7adfeac68fcacb524dc0a69a49f2c325",
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
Controls client-side timeout. Client timeout controls total time \(both client-side and server-side\) that any identification event is allowed to run. By default it's 5 seconds. You can control the client-side timeout using `timeout` configuration key. The `timeout` option is in milliseconds.  Example usage:

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



