---
sidebar_position: 2
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Adding Linaria to an Astro Project

Step-by-Step Guide to Integrating CSS-in-JS Linaria with Astro.

## Install packages

We'll install 3 packages. They're `@linaria/core`, `@wyw-in-js/babel-preset` and `@wyw-in-js/vite`

<Tabs groupId="package-manager">
  <TabItem value="npm" label="npm" default>
    ```sh
    npm install @linaria/react
    ```
  </TabItem>
  <TabItem value="pnpm" label="pnpm">
    ```sh
    pnpm add @linaria/core inpnpm add -D @wyw-in-js/babel-preset @wyw-in-js/vite
    ```
  </TabItem>
  <TabItem value="yarn" label="Yarn">
    ```sh
    yarn add @linaria/core yarn add -D @wyw-in-js/babel-preset @wyw-in-js/vite
    ```
  </TabItem>
</Tabs>

## Config `astro.config.mjs` file

Add Linaria plugin in Astro configuration file (via Vite).

```js title="astro.config.mjs"
import { defineConfig } from 'astro/config';
import wyw from '@wyw-in-js/vite';

export default defineConfig({
  // ...
  vite: {
    plugins: [
      // ...
      //highlight-start
      wyw({
        include: ['**/*.{ts}'],
        babelOptions: {
          presets: ['@babel/preset-typescript', '@wyw-in-js/babel-preset'],
        },
      }),
      //highlight-end
      // ...
    ],
  },
  // ...
});
```

## Usage

### Basic usage

Create a new `.ts` or `.js` file depends on your project. Then, import `css` function.

The syntax use template literals like styled-components.

```ts title="src/styles/myStyle.ts"
import { css } from '@linaria/core';

export const title = css`
  font-size: 3rem;
  background: salmon;
`;

export const subtitle = css`
  color: #333;
  line-height: 2;
`;
```

Then import to `.astro` file and use as style

```tsx title="src/styles/myStyle.ts"
---
import Layout from '@/layouts/Layout.astro';
//highlight-next-line
import { title } from '@/styles/myStyle';
---

<Layout title="Astro + Linaria">
  //highlight-next-line
  <h1 class={title}>Welcome to Astro + Linaria</h1>
</Layout>
```

It will be rendered as H1 element with **hashed className**. You could inspect with dev tools.

:::note
You cannot pass `props` to Linaria styles. Only React.
:::

:::danger
Do **NOT** import or use `css` function in `.astro` directly. It will **BREAK** your dev server.

You **MUST** write your styles in `.ts` or `.js` file separately.
:::

:::tip
If you have many exported styles. You could use **namespace import** to import all styles all in one.

```tsx title="src/pages/index.astro"
---
import Layout from '@/layouts/Layout.astro';
// DO
//highlight-next-line
import * as styles from '@/styles/myStyle';
// DON'T
// import styles from '@/styles/global';
---
<Layout title="Astro + Linaria">
  //highlight-start
  <h1 class={styles.title}>Welcome to Astro + Linaria</h1>
  <p class={styles.subtitle}>A step-by-step guide how to integrated CSS-in-JS.</p>
  //highlight-end
</Layout>
```

:::

### With helper

You can use clsx like with `cx()`. Learn more on an official docs.
_**Note**: This guide focus only integration and implementation._

## Linaria with ReactJS (optional)

If you have plan to use with React. You have to install additional packages.

### Install @linaria/react

:::warning
Do **NOT** forget to add an integration with ReactJS.
:::

<Tabs groupId="package-manager">
  <TabItem value="npm" label="npm" default>
    ```sh
    npm install @linaria/react
    npm install -D @babel/preset-react
    ```
  </TabItem>
  <TabItem value="pnpm" label="pnpm">
    ```sh
    pnpm add @linaria/react
    pnpm add -D @babel/preset-react
    ```
  </TabItem>
  <TabItem value="yarn" label="Yarn">
    ```sh
    yarn add @linaria/react
    yarn add -D @babel/preset-react
    ```
  </TabItem>
</Tabs>

### Config `astro.config.mjs`

Add babel-preset into configuration file.

```js title="astro.config.mjs"
import { defineConfig } from 'astro/config';
import wyw from '@wyw-in-js/vite';

export default defineConfig({
  // ...
  vite: {
    plugins: [
      // ...
      wyw({
        //highlight-next-line
        include: ['**/*.{ts,jsx,tsx}'],
        babelOptions: {
          //highlight-next-line
          presets: ['@babel/preset-typescript', '@wyw-in-js/babel-preset', '@babel/preset-react'],
        },
      }),
      // ...
    ],
  },
  // ...
});
```

### Usage

You can use Linaria within React file. The syntax are the same like Astro.

Also you can use props here.

```tsx title="src/components/Button.tsx"
import { styled } from '@linaria/react';

const Title = styled.h1`
  font-size: 2.5rem;
`;

const Description = styled.p`
  color: ${(props) => props.color};
`;

function Comp() {
  return (
    <>
      <Title>Welcome to Astro + Linaria with React</Title>
      <Description color="brown">A step-by-step CSS-in-JS guide.</Description>
    </>
  );
}
```

## CSS variables (optional)

:::note
This is a personal styling method. It wouldn't guarantee the best at all. I just like it.
:::

You can add CSS variables into Linaria styles.

### Usage of CSS variables as Component

```ts title="src/styles/myStyle.ts"
import { css } from '@linaria/core';

export const footerStyle = css`
  padding: 2rem;
  background: var(--footer-background);
  color: var(--footer-text);
`;
```

```tsx title="src/pages/index.astro"
---
import Layout from '@/layouts/Layout.astro';
import * as styles from '@/styles/myStyle';
---
<Layout title="Astro + Linaria">
  <footer
    class={styles.footerStyle}
    style="--footer-background: #fff; --footer-text: #000"
  >This is a footer</footer>
  <footer
    class={styles.footerStyle}
    style="--footer-background: #333; --footer-text: #ccc"
  >This is a footer dark mode</footer>
</Layout>
```

It's reusable !

### Usage of CSS variables as Theme

```ts title="src/styles/myStyle.ts"
import { css } from '@linaria/core';

// Theme color
export const blueTheme = css`
  --color-primary: blue;
`;

export const greenTheme = css`
  --color-primary: green;
`;

// Component
export const buttonPrimary = css`
  background: var(--color-primary);
  border: 1px solid var(--color-primary);
`;
```

```tsx title="src/pages/index.astro"
---
import * as styles from '@/styles/myStyle';
---
// highlight-next-line
<div class={styles.blueTheme}>
  <button class={styles.buttonPrimary}>This is a blue (primary) button</button>
</div>
// highlight-next-line
<div class={styles.greenTheme}>
  <button class={styles.buttonPrimary}>This is a green (primary) button</button>
</div>
```

:::info
Do you know? CSS variables is an informal name of CSS custom properties.
:::

## References

- Linaria Github https://github.com/callstack/linaria
- Installation https://github.com/callstack/linaria?tab=readme-ov-file#installation
- Linaria Basic usage https://github.com/callstack/linaria/blob/master/docs/BASICS.md
- JavaScrpt Namespace import https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import#namespace_import
- Linaria API https://github.com/callstack/linaria/blob/master/docs/API.md
- Using CSS custom properties https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_cascading_variables/Using_CSS_custom_properties
