---
description: >-
  Recommendations and usage patterns that help the performance of your web
  applications.
---

# Best practices

## API pre-connect

API pre-connect is recommended for improved request timings. To enable it, place the `preconnect` directive as early as possible in your head tag:

```markup
<html>
<head>
  <link rel="preconnect" href="https://cdn.fpjs.io">
  <link rel="preconnect" href="https://api.fpjs.io">
  ... styles, js etc
```

Note that you need to pre-connect to both CDN and API.  
  
If you're using the `EU` API, use this pre-connect URL: `https://eu.api.fpjs.io`

