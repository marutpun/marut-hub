---
sidebar_position: 2
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Adding Tailwind CSS v4 to a Docusaurus Project

Step-by-Step Guide to Integrating Tailwind CSS v4 with Docusaurus

## Install Docusaurus package

<Tabs groupId="package-manager">
  <TabItem value="npm" label="npm" default>
    ```sh
    npx create-docusaurus@latest docusaurus-tailwind-v4 classic --typescript
    ```
  </TabItem>
  <TabItem value="pnpm" label="pnpm">
    ```sh
    pnpm dlx create-docusaurus@latest docusaurus-tailwind-v4 classic --typescript
    ```
  </TabItem>
  <TabItem value="yarn" label="Yarn">
    ```sh
    yarn dlx create-docusaurus@latest docusaurus-tailwind-v4 classic --typescript
    ```
  </TabItem>
</Tabs>

## Install TailwindCSS

Tailwind CSS v4 no longer uses Autoprefixer because CSS prefixes will be handled automatically.

<Tabs groupId="package-manager">
  <TabItem value="npm" label="npm" default>
    ```sh
    npm install tailwindcss @tailwindcss/postcss postcss
    ```
  </TabItem>
  <TabItem value="pnpm" label="pnpm">
    ```sh
    pnpm add tailwindcss @tailwindcss/postcss postcss
    ```
  </TabItem>
  <TabItem value="yarn" label="Yarn">
    ```sh
    yarn add tailwindcss @tailwindcss/postcss postcss
    ```
  </TabItem>
</Tabs>

## Add TailwindCSS to `docusaurus.config.ts`

Add the following snippet.

```ts title="docusaurus.config.ts"
//highlight-next-line
import type { Config, Plugin, PostCssOptions } from '@docusaurus/types';

const config: Config = {
  //...
  //highlight-start
  plugins: [
    function docusaurusTailwindCss(): Plugin<void> {
      return {
        name: 'docusaurus-tailwindcss',
        configurePostCss(postCssOptions: PostCssOptions): PostCssOptions {
          postCssOptions.plugins.push(require('@tailwindcss/postcss'));
          return postCssOptions;
        },
      };
    },
  ],
  //highlight-end
  //...
};
```

:::info
You need to restart the development server for any changes made in `docusaurus.config.ts` to take effect, or you can do it later once you’re done editing this file.
:::

## Import Tailwind CSS

Import stylesheet into our local CSS stylesheet. There are 2 methods to apply. All-in-one or partial style.

### All-in-one styles

```css title="src/css/custom.css"
@import 'tailwindcss';

@layer base {
  body {
    @apply bg-amber-300;
  }
}
```

### Partial style

```css title="src/css/custom.css"
@layer theme, base, components, utilities;

@import 'tailwindcss/theme.css' layer(theme);
@import 'tailwindcss/preflight.css' layer(base);
@import 'tailwindcss/utilities.css' layer(utilities);

@layer base {
  body {
    @apply bg-amber-300;
  }
}
```

Let's run web server and see the amber background.

:::tip
I recommend using the partial style method and removing **Preflight (base)**. Docusaurus already includes typography and CSS reset via Infima, so there's no need for the Tailwind typography package. Adding it may cause conflicts and increase file size.

Remember, Docusaurus is designed for content, so focus on simplicity and clarity.

```css title="src/css/custom.css"
@layer theme, base, components, utilities;

@import 'tailwindcss/theme.css' layer(theme);
@import 'tailwindcss/utilities.css' layer(utilities);

@layer base {
  body {
    @apply bg-amber-300;
  }
}
```

:::

## Add custom font into Tailwind CSS v4 (optional)

Please follow this step first if you want to include external font [Add custom fonts from CDN to a Docsusaurus project](add-custom-font-from-cdn-to-docusaurus). Both CDN or local font.

After you're done, add the following snippets.

```css title="src/css/custom.css"
@theme {
  --font-oswald: 'Oswald', 'sans-serif';
}
```

Then you can use it anywhere

```jsx
<h1 className="font-oswald">Hello world!</h1>
```

You can replace Infima font base.

```css title="src/css/custom.css"
:root {
  /* ... */
  --font-oswald: 'Oswald', 'sans-serif';
  --ifm-heading-font-family: var(--font-oswald);
  /* ... */
}
```

## References

- Install new Docusaurus project https://docusaurus.io/docs/installation#scaffold-project-website
- Install TailwindCSS v4 with PostCSS https://tailwindcss.com/docs/installation/using-postcss
- Customize font in TalwindCSS https://tailwindcss.com/docs/font-family#customizing-your-theme
- `stylesheets` API https://docusaurus.io/docs/api/docusaurus-config#stylesheets
