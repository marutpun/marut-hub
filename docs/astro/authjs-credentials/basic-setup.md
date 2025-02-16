---
sidebar_position: 1
description: Prepare Your First Step of Authentication
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Basic Setup

## Install Astro

You may skip this section if you already have an existing Astro project.

<Tabs groupId="package-manager">
  <TabItem value="npm" label="npm" default>
    ```sh
    npm create astro@latest
    ```
  </TabItem>
  <TabItem value="pnpm" label="pnpm">
    ```sh
    pnpm create astro@latest
    ```
  </TabItem>
  <TabItem value="yarn" label="Yarn">
    ```sh
    yarn create astro@latest
    ```
  </TabItem>
</Tabs>

## Install Astro DB

We'll create our own database.

<Tabs groupId="package-manager">
  <TabItem value="npm" label="npm" default>
    ```sh
    npx astro add db
    ```
  </TabItem>
  <TabItem value="pnpm" label="pnpm">
    ```sh
    pnpm astro add db
    ```
  </TabItem>
  <TabItem value="yarn" label="Yarn">
    ```sh
    yarn astro add db
    ```
  </TabItem>
</Tabs>

## Install Auth.js

Just install package. We'll setup configuration later.

<Tabs groupId="package-manager">
  <TabItem value="npm" label="npm" default>
    ```sh
    npx astro add auth-astro
    ```
  </TabItem>
  <TabItem value="pnpm" label="pnpm">
    ```sh
    pnpm astro add auth-astro
    ```
  </TabItem>
  <TabItem value="yarn" label="Yarn">
    ```sh
    yarn astro add auth-astro
    ```
  </TabItem>
</Tabs>

## Use server-side rendering (SSR)

Add `output` property.

```js title="astro.config.mjs"
export default defineConfig({
  // ...
  output: 'server',
  // ...
});
```

## Use path aliases (optional)

We will use path aliases to import file easily.

```json title="tsconfig.json"
{
  //highlight-start
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"]
    }
  }
  //highlight-end
}
```

## Post `astro.config.mjs`

```js title="astr.config.mjs"
// @ts-check
import { defineConfig } from 'astro/config';
import db from '@astrojs/db';
import auth from 'auth-astro';

// https://astro.build/config
export default defineConfig({
  integrations: [db(), auth()],
  output: 'server',
});
```

## References

- Install Astro https://docs.astro.build/en/install-and-setup/#install-from-the-cli-wizard
- Install SSR Adapter https://docs.astro.build/en/guides/on-demand-rendering/#server-adapters
- Instal Astro DB https://docs.astro.build/en/guides/astro-db/#installation
- Server Side Rendering https://docs.astro.build/en/reference/configuration-reference/#output
- TSConfig References: Paths https://www.typescriptlang.org/tsconfig/#paths
