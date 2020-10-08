---
description: >-
  This page provides an overview of server-to-server network communication known
  as "webhooks."
---

# Webhooks

### What is a webhook?

A webhook is the act of FPJS making an HTTP POST request to _your_ application’s API when an event occurs. This way FPJS is able to immediately notify you when an event occurs and your application doesn’t have to perform complicated polling to determine if something new occurred.

### Protecting your webhook

An easy way to protect your API is through basic HTTP authentication. Almost all web servers can be configured to require a user name and password for access to a URL. You can configure your webhook URL with basic HTTP authentication by adding the user name and password to the URL `https://example.com/webhook` in the following format and setting the result as the webhook URL:

```text
https://<username>:<password>@example.com/webhook
```

To ensure your data is encrypted, we require using HTTPS \(ssl\) for your webhook url.

### Fingerprinting webhook

The Fingerprinting webhook is a key part of FPJS webhook system. It occurs every time your webpage sends a `pageview` event automatically using the agent or whenever  you trigger this event with `FP.send()` method call.

As soon as the `pageview` happens, the `FPJS` server processes the received information and makes an HTTP POST request to your webhook URL.

**Registering the fingerprinting webhook**

Send us your preferred webhook settings via email to our dev team to register and test it for you.

#### JSON style guide and formatting

{% hint style="warning" %}
FPJS uses Google JSON style guide: [https://google.github.io/styleguide/jsoncstyleguide.xml](https://google.github.io/styleguide/jsoncstyleguide.xml)
{% endhint %}

**Example JSON webhook data for a legitimate user**

```javascript
{
  //user-provided data, for example requestID
  "tag": 2718281828,
  "visitorId": "3HNey93AkBW6CRbxV6xP",
  "fingerprint": "e0aef38e47d1e088692faba62c9c3781",
  // ISO 8601 timestamp in UTC
  "timestamp": "2019-02-28T16:48:37.447832Z",
  // URL where the fingerprinting occurred in the browser
  "url": "https://banking.example.com/signup",
  "ip": "216.3.128.12",
  "ipLocation": {
    // miles
    "accuracyRadius": 1,
    "city": {
      "name": "Bolingbrook"
    },
    "continent": {
      "code": "NA",
      "name": "North America"
    },
    "country": {
      "isoCode": "US",
      "name": "United States"
    },
    "latitude": 41.12933,
    "longitude": -88.9954,
    "organization": {
      "domain": "comcast.net",
      "isp": "Comcast Cable",
      "legalName": "Comcast Cable Communications, LLC",
      "name": "Comcast Cable"
    },
    "postalCode": "60547",
    "subdivisions": [
      {
        "isoCode": "IL",
        "name": "Illinois"
      }
    ],
    "timeZone": "America/Chicago"
  },
  "visitorDetails": {
    "browserFullVersion": "72.0.3626.119",
    "browserSaysItsBot": false,
    "browserMajorVersion": "72",
    "browserName": "Chrome",
    "browserPlatformName": "Macintosh",
    "browserPlatformVersion": "10.14.3",
    "browserWebkitFullVersion": "537.36",
    "browserHeadless": false,
    // experimental feature
    "botProbability": 0.02
  }
}
```

**Example JSON webhook data for a fraudulent bot**

```javascript
{
  //user-provided data, for example requestID
  "tag": 2718281828,
  "visitorId": "3HNey93AkBW6CRbxV6xP",
  "fingerprint": "d12423928f0fb79b5c498d1df6507d68",
  "timestamp": "2019-02-28 16:48:37.447",
  "url": "https://banking.example.com/signup",
  "ip": "50.19.26.15",
  "ipLocation": {
    "accuracyRadius": 1000,
    "city": {
      "name": "Ashburn"
    },
    "continent": {
      "code": "NA",
      "name": "North America"
    },
    "country": {
      "isoCode": "US",
      "name": "United States"
    },
    "latitude": 39.0481,
    "longitude": -77.4728,
    "organization": {
      "domain": "amazonaws.com",
      "isp": "Amazon.com",
      "legalName": "Amazon.com",
      "name": "Amazon.com"
    },
    "postalCode": "20149",
    "subdivisions": [
      {
        "isoCode": "VA",
        "name": "Virginia"
      }
    ],
    "timeZone": "America/New York"
  },
  "visitorDetails": {
    "browserFullVersion": "63.0.3626.119",
    "browserSaysItsBot": false,
    "browserMajorVersion": "63",
    "browserName": "Chrome",
    "browserPlatformName": "Linux",
    "browserPlatformVersion": "unknown",
    "browserWebkitFullVersion": "516.36",
    "browserHeadless": true,
    // experimental feature
    "botProbability": .96
  }
}
```

#### How to test webhooks with CURL

```bash
curl <your-webhook-url> \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{ "tag": 2718281828, "visitorId": "3HNey93AkBW6CRbxV6xP", /* remaining fields here */}'
```

### Webhook errors and retries

When a URL returns a non-200 code, we will schedule the JSON POST for a retry. A total of 10 retries will be made, with growing intervals from 1 minute to 12 hours. After 12 hours the webhook will fail.



