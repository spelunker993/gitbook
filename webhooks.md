---
description: >-
  This page provides an overview of server-to-server network communication known
  as "webhooks."
---

# Webhooks

### What is a webhook?

A webhook is the act of FPJS making an HTTP POST request to _your_ application’s API when an event occurs. This way FPJS is able to immediately notify you when an event occurs and your application doesn’t have to perform complicated polling to determine if something new occurred.  
Webhooks are asynchronous and don't incur performance overhead when enabled.

### Protecting your webhook

There are two ways of protecting your webhook: IP address whitelist and basic HTTP authentication.

#### IP address whitelist

Webhook requests are made from static IP addresses that you can add to your firewall rules.

| REGION | IP |
| :--- | :--- |
| US east \(N. Virginia\) | **`3.217.157.129`** |
| EU central \(Frankfurt\) | **`18.196.47.40`** |

#### HTTP basic authentication

An easy way to protect your API is through basic HTTP authentication. Almost all web servers can be configured to require a user name and password to access a URL. When you have basic HTTP authentication in place, the URL to access your API endpoint will look like follows:

```markup
https://<username>:<password>@example.com/webhook
```

To ensure your data is encrypted, we require using HTTPS \(TLS\) for all webhook communication.

### Identification webhook

This webhook is a key part of FPJS webhook system. It occurs every time your webpage sends data for identification.

As soon as the page is loaded and the data is sent, our servers process the received information and makes an HTTP POST request to your webhook URL.

**Registering the identification webhook**

Send us your preferred webhook settings via email and our dev team will register and test it for you.

#### JSON style guide and formatting

{% hint style="warning" %}
FPJS uses Google JSON style guide: [https://google.github.io/styleguide/jsoncstyleguide.xml](https://google.github.io/styleguide/jsoncstyleguide.xml)
{% endhint %}

### Identification webhook object format

**Example webhook object for a legitimate user**

```javascript
{
  // user-provided data, for example requestID
  // can also be an object with values
  "tag": 2718281828,
  // persistent cookie identifier
  // helpful to detect incognito mode users
  "visitorId": "3HNey93AkBW6CRbxV6xP",
  // timestamp in milliseconds (since unix epoch)
  "timestamp": 1554910997788,
  // URL where the fingerprinting occurred in the browser
  "url": "https://banking.example.com/signup",
  // The URL of a client-side referrer
  "clientReferrer": "https://google.com?search=banking+services",
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
  "browserDetails": {
    "browserName": "Chrome",
    "browserFullVersion": "72.0.3626.119",
    "browserMajorVersion": "72",
    "os": "Mac OS X",
    "osVersion": "10.14.3",
    "device": "Other",
    "userAgent": "(Macintosh; Intel Mac OS X 10_14_3) Chrome/73.0.3683.86",
    // experimental feature
    // if the value is > 0.9 it's a bot
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
  "timestamp": 1554910997788,
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
  "browserDetails": {
    "browserFullVersion": "72.0.3626.119",
    "browserMajorVersion": "72",
    "browserName": "Chrome",
    "os": "Linux",
    "osVersion": "4.15",
    "device": "Other",
    "userAgent": "(ChromeHeadless; Linux Debian 9.1/4.15) Chrome/72.0.3626.119",
    // experimental feature
    // It's a range of values from 0 to 1.
    // if the value is > 0.9, it's a bot.
    "botProbability": .96
  }
}
```

#### How to test your webhooks with CURL

Once you added the webhook to your system, you can test it with:

```bash
curl <your-webhook-url> \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{ "tag": 2718281828, "visitorId": "3HNey93AkBW6CRbxV6xP", /* remaining fields here */}'
```

### Webhook errors and retries

When a URL returns a non-200 code, we will schedule the JSON POST for a retry. A total of 10 retries will be made, with growing intervals from 1 minute to 12 hours. After 12 hours the webhook will fail.

