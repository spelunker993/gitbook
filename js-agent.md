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

API methods

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

Example usage of the `tag` argument:  
Client-side code:

```javascript
// requestId = 2718281828
FP.send(requestId);
```

You can also use a JavaScript object as a tag value and it will be returned back to you in the callback or in a webhook.

```javascript
var tag = {
  requestID: serverRequest.getID(), 
  requestType: serverRequest.getType()
};
FP.send(tag);
```

`send` will return a `Promise`  so you can be notified when  sending is complete.

```javascript
var requestId = 2718281828;
FP.send(requestId).then(function(data) { console.log(data) });
// Promise {<pending>}
{visitorId: "nTxJ28Gt4CegeAwdWELL", tag: 2718281828}
```
{% endtab %}

{% tab title="ip" %}
`ip` is an option that controls the [geolocation accuracy](geolocation.md). 
{% endtab %}

{% tab title="linkedId" %}
`linkedId` is a way of linking current page view with a custom identifier. This can be helpful to be able to filter [API](server-api.md) visit information later.

Some examples of how you can use the `linkedId:`

1. Link your website `userID` with a FPJS `visitorID` to be able to analyze this data.
2. Link your e-commerce `orderID` with a FPJS `visitorID` to be able to see the orders this visitor made.
3. Link a survey ID with a `visitorID` to ensure that the same user does not participate in a survey multiple times.
4. Limit 1 visitor per item or per discount coupon.
{% endtab %}
{% endtabs %}

