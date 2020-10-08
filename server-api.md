---
description: Server API is currently in development.
---

# Server API

### Authentication

There are 2 ways to authenticate with the API: auth header and token query parameter. All unauthenticated requests will return HTTP 403 \(forbidden\) response.

#### Auth header

This is the recommended way of API authentication. When making an API request, add `Auth-Token` HTTP header with your API token.

#### Token query parameter

This method of authentication is easy to use when you want to test it in the browser. However we don't recommend using it in production.

Example request with a token query parameter:

```text
// Paste this URL into your browser
// replace the token with your real API token
https://api.fpjs.io/visitors/someVisitorID?token=your-api-token
```

You can obtain your free API token by emailing us at `pro@fingerprintjs.com`

### API methods

{% api-method method="get" host="https://api.fpjs.io" path="/visitors/:id" %}
{% api-method-summary %}
Get visitor history
{% endapi-method-summary %}

{% api-method-description %}
This endpoint allows you to get a history of visits with all available information. Use the `visitorID` as a URL path parameter. 
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="id" type="string" required=true %}
VisitorID
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Visitor found.  
{% endapi-method-response-example-description %}

{% tabs %}
{% tab title="visitor-found" %}
```javascript
// visitor found and recent visits history is available
{
  "visitorId": "Ibk1527CUFmcnjLwIs4A9",
  "visits": [
    {
      "incognito": true,
      "linkedId": "somelinkedId",
      "time": "2019-05-21T16:40:13Z",
      "url": "https://www.example.com/login",
      "ip": "61.127.217.15",
      "ipLocation": {
        "accuracyRadius": 10,
        "latitude": 49.982,
        "longitude": 36.2566,
        "postalCode": "61202",
        "timezone": "Europe/Dusseldorf",
        "city": {
          "name": "Dusseldorf"
        },
        "continent": {
          "code": "EU",
          "name": "Europe"
        },
        "country": {
          "code": "DE",
          "name": "Germany"
        },
        "subdivisions": [
          {
            "isoCode": "63",
            "name": "North Rhine-Westphalia"
          }
        ],
      },
      "browserDetails": {
        "browserName": "Chrome",
        "browserMajorVersion": "74",
        "browserFullVersion": "74.0.3729",
        "os": "Windows",
        "osVersion": "7",
        "device": "Other",
        "userAgent": "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.157 Safari/537.36",
        "botProbability": 0
      }
    }
  ]
}
```
{% endtab %}

{% tab title="visitor-found-no-visits" %}
```javascript
// visitor found, but has no recent visits
// by default, visits are returned from last 10 days
{
  "visitorId": "Ibk1527CUFmcnjLwIs4A9",
  "visits": []
}
```
{% endtab %}
{% endtabs %}
{% endapi-method-response-example %}

{% api-method-response-example httpCode=404 %}
{% api-method-response-example-description %}
Visitor not found
{% endapi-method-response-example-description %}

```javascript
{
  "visitorId": "Ibk1527CUFmcnjLwIs4A9"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}



