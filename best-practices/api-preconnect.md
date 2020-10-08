---
description: Performance tip
---

# API preconnect

API pre-connect is recommended for improved request timings. To enable it, place the `preconnect` directive as early as possible in your head tag:

```markup
<html>
<head>
  <link rel="preconnect" href="https://cdn.fpjs.io">
  <link rel="preconnect" href="https://api.sjpf.io">
  <link rel="preconnect" href="https://api.fpjs.io">
  ... styles, js etc
```

Note that you always need to pre-connect to `api.sjpf.io` and `api.fpjs.io`. If you're using the CDN version of the script, you also need to pre-connect to `cdn.fpjs.io`.  
  
If you're using the `EU` API, use this pre-connect URL: `https://eu.api.fpjs.io`

