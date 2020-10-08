---
description: Pro and free versions are separate products.
---

# Pro vs free version

FingerprintJS Pro was started in the beginning of 2019 when the [creator](https://github.com/Valve) of the original [FingerprintJS ](https://github.com/valve/fingerprintjs2)took feedback from existing open-source users to implement a new professional version. 

Since 2012, FingerprintJS was used by thousands of companies on a traffic scale of billions of users.

**The new Pro version is a commercially developed platform for fraud detection, anonymous user identification, bot detection and traffic analysis that has numerous advantages, compared with the free version.**

The differences between the free and Pro versions are summarized in the  table below.

| **Feature** | **Free** | **PRO** |
| :--- | :--- | :--- |
| \*\*\*\*[**Browser fingerprinting**](browser-fingerprinting.md)\*\*\*\* | Basic | Advanced |
| \*\*\*\*[**CDN**](js-agent/cdn.md)\*\*\*\* | Basic | Advanced |
| \*\*\*\*[**Anonymous user identification**](user-identification.md)\*\*\*\* | No | Yes |
| \*\*\*\*[**Fraud detection**](user-identification.md) | No | Yes |
| \*\*\*\*[**Bot detection**](bot-detection.md)\*\*\*\* | No | Yes |
| \*\*\*\*[**Incognito / Private mode detection**](incognito-private-mode-detection.md)\*\*\*\* | No | Yes |
| [**Geolocation**](geolocation.md)\*\*\*\* | No | Yes |
| Security | No | Yes |
| [**API**](server-api.md)\*\*\*\* | No | Yes |
| [**Webhooks**](webhooks.md)\*\*\*\* | No | Yes |
| SLA | No | Yes |
| Support | No | Yes |

### Pro version is a server-side analysis API

The main difference between the free and the pro version is that the free version is only a JavaScript file that queries browser attributes and computes a hash from them \(performs fingerprinting\). In a situation with identical browsers, it will generate identical fingerprints. If two different users use the same browser \(same vendor and version\) on the same phone model, you will get two identical fingerprints and will not be able to tell these two users apart.

To solve this, the pro version processes the identification data on the server. In addition to regular fingerprinting, it analyzes vast amounts of auxiliary data \(IP addresses, time of visit patterns, URL changes and more\) to be able to reliably deduplicate different users that have identical devices.

**Other differences between Pro and free versions are explained below \(click on a red link in the summary table to visit a dedicated help page\).**

### **Security**

**Free:** generates the fingerprints directly in the browser, which makes it vulnerable to spoofing and reverse engineering.

**Pro:** processes all the information server-side and transmits it securely to you servers using server-to-server APIs. Information is never exposed in the browser, which makes it much harder to tamper with. All the information that is collected is never shared with 3rd parties. The security of your data is our priority.

### **SLA**

**Free:** provides no SLA or any guarantees.

**Pro:** provides a 99.9% uptime/availability monthly SLA.

### **Support**

**Free:** only GitHub issues/questions with no response time guarantee.

**Pro:** provides guaranteed same business day response.  
Enterprise support options are available [on request.](mailto:support@fingerprintjs.com)

