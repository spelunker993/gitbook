---
description: A brief overview of what browser fingerprinting is and how it's helpful.
---

# Browser fingerprinting

A **browser fingerprint** is a set of information related to a user’s device from the hardware to the operating system to the browser and its configuration. Browser fingerprinting refers to the process of collecting information through a web browser to build a fingerprint of a device. Via a  script running inside a browser, a server can collect a wide variety of information from public interfaces called Application Programming Interface \(API\) and HTTP headers. An API is an interface that provides an entry point to specific objects and functions. While some APIs require a permission to be accessed like the microphone or the camera, most of them are freely accessible from any JavaScript script making the information collection possible. Contrarily to other identification techniques like cookies that rely on a unique identifier \(ID\) directly stored inside the browser, browser fingerprinting is qualified as completely stateless. **It does not leave any trace as it does not require the storage of information inside the browser.  
  
FingerprintJS Pro** uses browser fingerprinting internally as one of the many identification techniques. The implementation of fingerprinting is the most advanced in the market and employs many cutting-edge methods of browser identification.   
  
However we don't expose the browser fingerprint values; instead we offer a `visitorID` value that provides a useful and persistent abstraction over volatile fingerprints. This approach has many advantages over traditional fingerprinting, because it is not linked to a particular browser or device configuration and can handle browser upgrades and device configuration changes.

