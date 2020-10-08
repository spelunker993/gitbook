---
description: Intelligent Tracking Prevention
---

# Safari ITP

ITP is [Safari Intelligent Tracking Prevention](https://webkit.org/blog/category/privacy/). 

ITP was first introduced in 2017, when Safari 11 was released. The goal of ITP is to prevent cross-site tracking by placing limitations on cookies and other website data.

Since 2017, Apple has released several updates to ITP. This document is up to date as of ITP [2.3.](https://webkit.org/blog/9521/intelligent-tracking-prevention-2-3/)  
ITP has been very effective in preventing cross-site tracking by disabling 3rd-party cookies completely and capping the lifetime of all script-writable website data.  Script-writable website data include cookies, set with `document.cookie` and local storage. The lifetime of the script-writable data is limited to seven days by default, but can be further reduced to 24 hours if ITP detects that the client-side cookie was set by a tracking domain.  
  
Apple recommends website owners use [Secure, HttpOnly cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies), because they are not affected by the lifetime cap. Apple cannot limit the lifetime of Secure, HttpOnly cookies is because these cookies are used for authentication \(log-in\).

FingerprintJS Pro and other services use both 3rd-party \(Secure, HttpOnly\)  and ****client-side cookies \(set with `document.cookie`\), which are both affected by ITP.

To avoid this, we recommend using our [CNAME](https://docs.fingerprintjs.com/pro/integrations/cname) \(preferred\) or [CloudFlare](https://docs.fingerprintjs.com/pro/integrations/cloudflare) integration.  


