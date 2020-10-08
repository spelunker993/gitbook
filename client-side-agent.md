# Client-side agent

The client-side agent is a high-performance fingerprinting agent that collects multiple device and browser signals and sends it to the FPJS servers for processing and identification. In addition to fingerprinting, it also maintains a unique, persistent `visitorID`, stored in browser cookies.

### Installing the agent

Add this JS snippet as the first element inside the `<head/>`tag on every page you want to monitor for suspicious visitors. Pass configuration options to control the behavior of the data collection. 

```markup
  <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-client-id');
  </script>
  <script async src="https://cdn.fpjs.io/@1/fp.js"></script>
```

### Configuring the agent

Agent configuration is done by calling the `fp('config', 'parameter', 'value')` syntax.

**Configuration options:**

**`client`**  `(default: none)`

Authenticates the agent with the back-end.

Example usage:

```javascript
fp('config', 'client', 'dXk12f27');
```

**`autoSend`**  `(default: false)`

**I**f set to `true`  the request is made automatically on page load. 

```javascript
fp('config', 'autoSend', true);
```

If set to `false` the request is not sent automatically and you need to send it yourself in JavaScript code.

```javascript
fp('config', 'autoSend', false);
```

Since it's false by default, you don't need to add the above line to the default snippet.

**`region`**`(default: 'us')`

Available values: `us` and `eu`.

The region of the FPJS servers you want to send data to. The default is `us`

Examples:

```javascript
fp('config', 'region', 'eu');
```

### Using the agent

There are two modes of usage: automatic and manual. 

#### Automatic

```markup
  <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-client-id');
    fp('config', 'autoSend', true);
  </script>
  <script async src="https://cdn.fpjs.io/@1/fp.js"></script>
```

In most cases you will use the agent in automatic mode. Add the snippet to every page `<head/>` tag and configure it to send data automatically with: 

```markup
fp('config', 'autoSend', true);
```

You can see in the snippet example above how automatic configuration works.   
When in automatic mode, the agent will send the data on every page load. You don't need to do anything else.

#### Manual

If you plan to invoke the agent manually \(on a Ad click for example\), you need **not** include this line in the snippet:

```markup
fp('config', 'autoSend', true);
```

When you do need to send the data, use the **`FP`  object.**

#### FP  object supported methods

**`FP.send()`**

The simplest way to have visitor data sent to your servers. 

**`FP.send(tag)`**

**`tag`** is a user-provided value that will be returned back to you in a webhook message. You may want to use the `tag` value to be able to associate a server-side webhook event with a web request or your userID.

What values can be used as a `tag`? Anything that identifies a user, visitor or a request.  
You can pass the `requestId` as a `tag` and then get this `requestId`back in the webhook, associated with a fingerprint and additional information.

Example usage of the `tag` argument:  
Client-side code:

```javascript
// requestId = 2718281828
FP.send(requestId);
```

You can also use a JS object as a tag value and it will be returned back to you in the callback.

```javascript
var tag = {requestID: serverRequest.getID(), requestType: serverRequest.getType()};
FP.send(tag);
```

`FP.send` will return a `Promise`  so you can be notified when  sending is complete.

```javascript
var requestId = 2718281828;
FP.send(requestId).then(function(data) { console.log(data) });
// Promise {<pending>}
{visitorId: "nTxJ28Gt4CegeAwdWELL", tag: 2718281828}
// Note that visitorId is always available via FP.getVisitorId();
```

#### Synchronous mode

Sometimes you need to get the identifiers to be returned in-request. This can be done by using `sync: true` option. Example:

```javascript
var options = {sync: true, tag: {requestId: getRequestId()}};
FP.send(options).then(function(response) {
  // response will have the fingerprint and other interesting information
  JSON.parse(response)
});
```

For a detailed overview of the response format, go to the webhooks response example: [https://docs.fingerprintjs.com/pro/webhooks\#response-format](https://docs.fingerprintjs.com/pro/webhooks#response-format).

Be aware that by using the synchronous mode, you compromise the security of the identification process!  
All the identifiers become available in the browser and can potentially be used against you. For this reason we recommend using the default, async mode where the identifiers are passed to your server instead in a POST request.

#### Visitor ID

In addition to browser fingerprinting, FP generates and stores a conventional cookie-based unique and persistent user identifier called `visitorId`. You can use this identifier in your code to customize your cart, show different ads or in any other way where you need to keep track of the user ID. 

You can get `visitorId` any time by calling `FP.getVisitorId()` and it is available immediately after the page load. 

It's also available in the `send` promise value.

```javascript
var requestId = 2718281828;
FP.send(requestId).then(function(data) { console.log(data) });
// Promise {<pending>}
{visitorId: "nTxJ28Gt4CegeAwdWELL", tag: 2718281828}
// Note that visitorId is always available via FP.getVisitorId();
```

### Agent versioning

By using the CDN url with only a major version \(`https://cdn.fpjs.io/@1/fp.js`\) your customers will always be getting the latest version of the agent. It will automatically upgrade and cache in users' browsers.

If you need to use a specific version, specify it directly in the URL: `https://cdn.fpjs.io/@1.2.3/fp.js`

We recommend that you only specify the major version in the URL e.g.`@1` and let it automatically upgrade to the latest version when it becomes available.

