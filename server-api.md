---
description: An efficient way to build your workflows using FingerprintJS Pro data.
---

# Server API

### Regions

Server API is available in these regions:   
**US** - N.Virginia, USA  
**EU** - Frankfurt,  Germany

  
Use these base URLs in those regions:

| Region | Base URL |
| :--- | :--- |
| US | `https://api.fpjs.io` |
| EU | `https://eu.api.fpjs.io` |

### Authentication

There are 2 ways to authenticate with the API: auth header and token query parameter. All unauthenticated requests will return HTTP 403 \(forbidden\) response.

#### Auth header

This is the recommended way of API authentication. When making an API request, add `Auth-Token` HTTP header with your API token.

#### Token query parameter

This method of authentication is easy to use when you want to test it in the browser. However we don't recommend using it in production.

Example request with a token query parameter:

```go
// Paste this URL into your browser
// replace the token with your real API token
https://api.fpjs.io/visitors/someVisitorID?token=your-api-token
```

### API methods

{% api-method method="get" host="api-base-url" path="/visitors/:id" %}
{% api-method-summary %}
/visitors/:id - get visitor history
{% endapi-method-summary %}

{% api-method-description %}
This endpoint allows you to get a history of visits with all available information. Use the `visitorID` as a URL path parameter. This API method is scoped to a visitor, i.e. all returned information is by visitorID.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="id" type="string" required=true %}
VisitorID
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-query-parameters %}
{% api-method-parameter name="request\_id" type="string" required=false %}
Filter events by requestID, see requestID for details.
{% endapi-method-parameter %}

{% api-method-parameter name="linked\_id" type="string" required=false %}
Filter events by custom identifier.
{% endapi-method-parameter %}

{% api-method-parameter name="limit" type="number" required=false %}
Limit scanned results \(see limiting for details\).
{% endapi-method-parameter %}

{% api-method-parameter name="before" type="integer" required=false %}
Used to paginate results \(see pagination for details\).
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
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
      "requestId": "0KSh65EnVoB85JBmloQK",
      "incognito": true,
      "linkedId": "somelinkedId",
      "time": "2019-05-21T16:40:13Z",
      // timestamp of the event with millisecond precision
      "timestamp": 1582299576512,
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
        "userAgent": "Mozilla/5.0 (Windows NT 6.1; Win64; x64) ....",
        "botProbability": 0
      }
    }
  ],
  // optional, if more results are available for pagination.
  "lastTimestamp": 1582299576512
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
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

#### RequestID

Every identification request has a unique identifier, associated with it, called `requestID`. This identifier is returned back into the browser during the identification API call and is also available in API responses.   
It is a convenient way to return the exact identification record that you need. When you filter events with `requestID`, only one event will be returned, because every event has a unique `requestID`.

#### LinkedID: adding a custom identifier to events

If you need to associate multiple events with a custom identifier, use `linkedID`. You can use it in different scenarios: e.g. identify by sessionID, identify by purchaseID or by your custom transaction number. You should use `requestID` to get a single event, whereas you should use `linkedID` to retrieve several events, associated with your custom identifier.

#### Limit: limiting scanned results

For performance reasons, visitors API first performs events scanning and then filtering. Filtering always happens after the results are scanned.   
`Limit` is a parameter to specify how many events should be scanned.   
Results are always returned sorted by the timestamp in descending order \(most recent first\), so scanning the results limits how many events are scanned back in the past.   
By default, the visits API scans 100 events. However you can specify a different `limit` parameter, to scan fewer or more results. A maximum value of 500 results can be scanned and returned.

After the results were scanned, the filtering is applied to them, e.g. filtering by `RequestID` or by `LinkedID`. 

**Using limit example**: your website visitor was identified 120 times and 120 events are stored in our database. Last 10 events are associated with a sessionID = 1234ADF; You want to scan last 50 events and filter them by your sessionID = 1234ADF. You can achieve this by using this query:  
  
`/visitors/:visitorID?limit=50&linked_id=1234ADF`  
Only 10 rows from the most recent 50 will be returned.

#### Pagination

When more results are available \(e.g. you scanned 200 results using `limit` parameter, but a total of 600 results are available\), a special `lastTimestamp` top-level attribute is added to the response. You should use the value of this attribute, if you want to paginate the results further in the past, to return the events that happened before that timestamp.

Example of using `before` parameter:

```text
// 1st request, returning most recent 200 events:
GET api-base-url/visitors/:visitorID?limit=200
// Note that the response has lastTimestamp attribute in the response.
// Use that attribute to return the events that happened before, i.e
// next page of 200 events
GET api-base-url/visitors/:visitorID?limit=200&before=1582232027567
```

Note that pagination happens during scanning of the results, so if you used pagination + filtering, you can have just a few results back, with potentially more results available for pagination.  
These timestamps are stored with a millisecond precision.  
When there are no more results available for scanning, the `lastTimestamp` attribute will not be returned.

