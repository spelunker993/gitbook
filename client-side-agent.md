# Client-side agent

The client-side agent is a high-performance fingerprinting agent that collects multiple device and browser signals and sends it to the FPJS servers for processing and identification.

### Installing the agent

Just add this snippet as the first element inside the `<head/>`tag on every page you want to monitor for suspicious visitors. Pass configuration options to control the behavior of the data collection. 

```markup
  <script async src="https://cdn.fpjs.me/fp-1.2.1.js"></script>
  <script>
    window.fpLayer = window.fpLayer || [];
    function fp() { fpLayer.push(arguments); }
    fp('config', 'client', 'your-client-id');
  </script>
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

In most cases the only thing you need to do is to add the snippet to all pages where you plan to use it.

If you plan to invoke the agent manually \(on a button click for example\), you need to interact with **`FP` global object.**

#### FP global object supported methods

**`FP.send(tag, callback)`**

**`tag`** is a user-provided value that serves as a way to associate the server-side identification event with user-provided value. 

What values can be used as a `tag`?  
`request_id` is a good candidate. You can pass the `request_id` as a `tag` and then get this `request_id`back in the webhook, associated with a fingerprint and additional information.

Example usage of the `tag` argument:  
Client-side code:

```javascript
// requestId = 2718281828
FP.send(requestId, function(response){console.log("sending complete")});
```

Server-side code \(runs on your server\)

```javascript
// NodeJS example
import { Request, Response } from "express"

export let fpjsWebhook = (req: Request, res: Response) => {
  console.log(req.body)
  // {
  //   "tag": 2718281828,
  //   "fingerprint": "e0aef38e47d1e088692faba62c9c3781",
  //   "timestamp": "2019-02-28 16:48:37.447",
  //   "visitorId": "3HNey93AkBW6CRbxV6xP"
  //   ... other supplied values
  // }
  res.sendStatus(200)
}
```

{% hint style="info" %}
What if you don't supply a `tag` ? 
{% endhint %}

Regardless if you supply a user-defined `tag` value or not, `FP` agent will always generate a unique `visitorId` value and set it in a `_fpjs` cookie on your domain. You will be able to examine the `_fpjs` value and associate the `visitorId` from the web hook with it.

**`callback`** is called when `send` method is done. It's optional and is only necessary when you want to know when sending is complete.

