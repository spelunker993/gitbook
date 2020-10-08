---
description: NPM module for flexible configuration and usage
---

# Installing from NPM

Fingerprint.js PRO agent is available as an [NPM module.](https://www.npmjs.com/package/@fp-pro/client) Install it with:

`npm install` **`@fp-pro/client`**

or

`yarn add` **`@fp-pro/client`**

NPM module comes with a full set of [TypeScript declaration files](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html) for superior development experience.

Configuring and using the agent, installed from the NPM module is slightly different from the CDN version. NPM module comes in a CommonJS format, and can be used with various module bundlers such as [Webpack](https://webpack.js.org/), [Browserify](http://browserify.org/) or [Rollup.js](https://rollupjs.org/).

A full working example of the agent, installed as an NPM module:

```typescript
import { FP } from "@fp-pro/client"

let fp = await FP.load({client: "your-token", region: "us"})
let response = await fp.send({callbackData: true})
```

This example is using `async/await` syntax, but it's also possible to use regular `promise` syntax.  
More examples about the usage are available in the JavaScript agent API documentation.

Example using NPM package with Rollup.js: [https://github.com/fpjspro/rollupjs-example](https://github.com/fpjspro/rollupjs-example)

