# CDN

The client-side agent code is hosted at edge locations around the world using CloudFront CDN.  
The URL to get the script is:

```text
https://cdn.fpjs.io/:version/fp.js
```

`:version` is a special directive that can match a **literal** version or a **major** version.  
  
**Literal version**  
If you place a literal version in the URL, this specific version will be returned.   


Example:

```text
https://cdn.fpjs.io/@2.4.9/fp.js
```

**Major version**  
If you use a major version in the URL, the latest actual version will be returned. This actual version will match the major version that you provided. 

Example:

```text
https://cdn.fpjs.io/@2/fp.js
```

This will return the latest actual version based on the major version, e.g. @2.4.9.

{% hint style="info" %}
We recommend using a major version, because it provides an "auto-upgrade" functionality. By using a major version, you don't need to worry about upgrading your script as the upgrade will be performed automatically.
{% endhint %}

