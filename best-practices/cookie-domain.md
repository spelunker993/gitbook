---
description: configuration parameter in detail
---

# Cookie Domain

{% hint style="info" %}
This configuration option should only be used if you're running FingerprintJS Pro on multiple subdomains and want the identification process to span all of them.
{% endhint %}

When you're using FingerprintJS Pro on multiple subdomains, you need to configure the `cookieDomain` .

Example: 

```javascript
import { FP } from "@fp-pro/client"

let fp = await FP.load({
  region: "us", 
  client: "token", 
  cookieDomain: "example.com"})
```

For instance, you're running the PRO version on `foo.example.com` and `bar.example.com`. In order for the identification cookie to work on both subdomains, configure the `cookieDomain` to be `example.com`.  
This will set the cookie for `.example.com` top-level domain and all subdomains. 

{% hint style="info" %}
Note that the cookie domain shouldn't include the preceding dot.  
**Incorrect**:  
`cookieDomain: ".example.com"`  
**Correct**:  
`cookieDomain: "example.com"`
{% endhint %}

