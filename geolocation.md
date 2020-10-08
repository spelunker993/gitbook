# Geolocation

Every identification request can also include an option to provide the geolocation of the current visitor.  
The geolocation options are passed in [`FP.send()`](https://app.gitbook.com/@fpjs/s/pro/v/2.2.0/js-agent#send-options) method.

Two types of geolocation are supported: `city` and `full`. They are requested using the `ip` option, e.g. 

```javascript
FP.send({ip: 'city'})
// OR
FP.send({ip: 'full'})
```

### CITY

City-level geolocation provides the location of a visitor on a city level. It is the default geo-location accuracy and does not need to be explicitly specified when calling the [`FP.send()`](https://app.gitbook.com/@fpjs/s/pro/v/2.2.0/js-agent#send-options) method.  
Example:

```javascript
// equivalent of FP.send({ip: 'city'})
FP.send().then(function(response){..}
// webhook data will contain this information:
{
  "ip": "89.112.151.14",
  "ipLocation": {
    "accuracyRadius": 5,
    "latitude": 37.987,
    "longitude": -1.13,
    "postalCode": "30006",
    "timezone": "Europe/Madrid",
    "city": {
      "name": "Murcia"
    },
    "continent": {
      "code": "EU",
      "name": "Europe"
    },
    "country": {
      "code": "ES",
      "name": "Spain"
    },
    "subdivisions": [
      {
        "isoCode": "MC",
        "name": "Murcia"
      },
      {
        "isoCode": "MU",
        "name": "Murcia"
      }
    ]
  }
}
```

Calling `FP.send()` with no geolocation accuracy specified is equivalent to calling `FP.send({ip: 'city'})`

### FULL

Full level performs a live request to an IP intelligence service and retrieves most up-to-date IP location information.

Example:

```javascript
FP.send({ip: 'full'}).then(function(response){..}
// webhook data will contain this information:
{
  "ip": "216.3.128.12",
  "ipLocation": {
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
  }
}
```

Note that `full` accuracy retrieves fresh results from the online service and provides `organization`, `domain` and `isp` information in addition to `city` accuracy.

