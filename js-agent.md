# JS agent

The client-side agent is a high-performance fingerprinting agent that collects multiple device and browser signals and sends them to the FPJS servers for processing and identification. In addition to fingerprinting, it also maintains a unique and persistent `visitorID` value.

### Installing the agent

Add this JS snippet as the first element inside the `<head/>`tag on every page where you want to identify visitors. Pass configuration options to control the behavior of data collection. 

```markup
<script>
  window.fpLayer = window.fpLayer || [];
  function fp() { fpLayer.push(arguments); }
  fp('config', 'client', 'your-token');
</script>
<script async src="https://cdn.fpjs.io/@2/fp.js"></script>
```

### Configuring the agent

Agent configuration is done using the `fp('config', 'parameter', 'value')` syntax.

**Configuration options:**

**`client`**  `(default: none)`

Authenticates the agent with the back-end.

Example usage:

```javascript
fp('config', 'client', 'dXk12f27');
```

**`autoSend`**  `(default: false)`

**I**f set to `true`  the sending of data is made automatically on page load. 

```javascript
fp('config', 'autoSend', true);
```

If set to `false` the data is not sent automatically and you need to send it yourself in JavaScript code.

```javascript
fp('config', 'autoSend', false);
```

Since it's `false` by default, you don't need to add the above line to the snippet.

**`region`**`(default: 'us')`

Available values: `us` and `eu`.

The region of the FPJS servers you want to send data to. The default is `us` which sends data to the N. Virginia server location.

Examples:

```javascript
fp('config', 'region', 'eu');
```

**`loaded`** `(no default)`

Use this configuration value to be notified when the JS agent is fully initialized. You can use the `fp` callback argument to get hold of the `FP` object and interact with it.

Example:

```javascript
fp('config', 'loaded', function(fp){ fp.send() }));
// note that the callback will be executed as soon as 
// the agent is ready to process requests.
// This is the recommended way to start working with the FP object 
// as soon as possible.
```

### Sending data

There are two modes of sending data: automatic and manual. 

#### Automatic

```markup
// quick example
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

If you plan to send the data manually \(on a Ad click for example\), you **don't need to** to include this line in the snippet:

```javascript
fp('config', 'autoSend', true);
```

When you actually need to send the data, use the **`FP`  object.**

### Receiving data

You can configure FPJS PRO to receive data in the browser \(via a callback\), in the server \(via a [webhook](webhooks.md)\) or both.

#### Receiving via a callback

JS agent has a promise-based API that you can use to chain callbacks. Note that the promise implementation is polyfilled in older browsers so that the call API is consistent everywhere.

```javascript
// Receive data via a callback
FP.send().then(function(response){
  console.log(response.visitorId);
});
```

Two types of responses are supported:  
`default` and `callbackData`. You don't need to pass any parameters to get the `default` response. `callbackData` is an extended response format and can be requested using the \``callbackData: true`\` parameter.

Response object format

{% tabs %}
{% tab title="default" %}
```javascript
{
  "visitorId":"2JGu1Z4d2J4IqiyzO3i4",
  "visitorFound":true
}
```
{% endtab %}

{% tab title="callbackData" %}
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
{% endtabs %}

#### Callback error handling

JS agent `send` method returns a promise which can be rejected in case of a server-side error.  
Below table summarizes possible types of errors that can lead to response promise rejections.

| Reason | Response example | HTTP Status |
| :--- | :--- | :--- |
| Token missing | `{"visitorId":"n/a","reason":"Token required"}` | 401 |
| Token invalid | `{"visitorId":"n/a","reason":"Token not found"}` | 403 |
| Bad JSON | `{"visitorId":"n/a", "reason": "Request cannot be parsed"}` | 422 |
|  |  |  |

Other types of requests are possible, but they don't lead to the promise rejections, instead, they resolve the promise, but contain a `n/a` visitorId and a reason why.

| Reason | Response example | HTTP Status |
| :--- | :--- | :--- |
| Trial expired | `{"visitorId":"n/a","reason":"Token expired"}` | 200 |
| Request failure | `{"visitorId":"n/a","reason":"Request failed"}` | 200 |
| Request times out | `{"visitorId":"n/a","reason":"Request failed to process"}` | 200 |

_Other types of response bodies are possible, but they're specific to certain types of bots and usually not visible to normal consumers._

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

### API reference

Generally, you need to use the `FP` global object to interact with the JS agent API.

Example of using the `FP` global object:

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

Alternatively, you can use the `loaded` configuration callback to get hold of the `fp` object via a callback parameter when the agent is ready.

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

#### **`send(options)`**

{% hint style="info" %}
Use **`send`** to send data. All options are optional.

A summary of options is provided below.
{% endhint %}

{% tabs %}
{% tab title="tag" %}
**`tag`** is a user-provided value or object that will be returned back to you in a callback or in a webhook message. You may want to use the `tag` value to be able to associate a server-side webhook event with a web request of your current visitor.

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
FP.send({tag: requestType});
```
{% endcode %}

{% code title="objects" %}
```javascript
// You can also use a JavaScript object as a tag value 
// and it will be returned back to you 
// in the callback or in a webhook.
var tag = {
  requestID: 2718281828, 
  requestType: "signup"
};
FP.send({tag: tag});
// NOTE: nested objects are not supported in tag objects.
```
{% endcode %}

`send` will return a [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)  so you can be notified when  sending is complete.

```javascript
var requestId = 2718281828;
FP.send({tag: requestId}).then(function(data) { 
  console.log(data);
});
// Promise {<pending>}
{visitorId: "nTxJ28Gt4CegeAwdWELL", tag: 2718281828}
```
{% endtab %}

{% tab title="linkedId" %}
`linkedId` is a way of linking current page view with a custom identifier. This can be helpful to be able to filter [API](server-api.md) visit information later.

Some examples of how you can use the `linkedId:`

1. Link your website `userID` with a FPJS `visitorID` to be able to analyze this data.
2. Link your e-commerce `orderID` with a FPJS `visitorID` to be able to see the orders a visitor made.
3. Link a survey ID with a `visitorID` to ensure that the same visitor does not participate in a single survey multiple times.
4. Limit one visitor per item or one visitor per discount coupon.

A `linkedId` looks similar to `tag`. When should you use each?  
A `tag` is a value or an object that is "passed-through" and sent back to your webhook endpoint. A tag can contain an arbitrary number of attributes of any type \(except for nested objects\). You can use tags to store them in your database and use for any purpose. `Tag` objects are meant to be consumed and processed by you eventually.  
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
{% endtabs %}

