---
description: Server API is currently in development and will be updated soon.
---

# Server API

{% api-method method="get" host="https://api.fpjs.me" path="/v1/visitors/:id" %}
{% api-method-summary %}
Get fingerprinting history by visitorID
{% endapi-method-summary %}

{% api-method-description %}
This endpoint allows you to get a history of visits with all available information.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="id" type="string" required=true %}
VisitorID
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-headers %}
{% api-method-parameter name="Auth" type="string" required=true %}
Authentication token
{% endapi-method-parameter %}
{% endapi-method-headers %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
History found and successfully retrieved.
{% endapi-method-response-example-description %}

```javascript
[{
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
}]
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=404 %}
{% api-method-response-example-description %}
Could not find history by visitorID
{% endapi-method-response-example-description %}

```javascript
{
    "message": "VisitorID 42 not found"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}



