---
sidebar_position: 1
---

# Add Custom Font from CDN to a Docusaurus Project

There are 2 methods how to add font. `<link>` or `@import`

In this tutorial, I'll use Geist and Geist Mono font from Google Fonts CDN.

## Via `<link>`

### Add `stylesheet` property

Let's get embeded code from Google Fonts

```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=Geist+Mono:wght@100..900&family=Geist:wght@100..900&display=swap" rel="stylesheet" />
```

Add `stylesheet` property with value as an array of object (`Object[]`) and add in `config` variable.

```ts title="docusaurus.config.ts"
const config: Config = {
  //...
  stylesheets: [
    {
      rel: 'preconnect',
      href: 'https://fonts.googleapis.com',
    },
    {
      rel: 'preconnect',
      href: 'https://fonts.gstatic.com',
      crossorigin: true,
    },
    {
      href: 'https://fonts.googleapis.com/css2?family=Geist+Mono:wght@100..900&family=Geist:wght@100..900&display=swap',
      rel: 'stylesheet',
    },
  ],
  //...
};
```

:::info
You need to restart the development server for any changes made in `docusaurus.config.ts` to take effect, or you can do it later once you’re done editing this file.
:::

## Via `@import`

### Add code directly in stylesheet

```css title="src/css/custom.css"
@import url('https://fonts.googleapis.com/css2?family=Geist+Mono:wght@100..900&family=Geist:wght@100..900&display=swap');
```

## Usage new font in stylesheet

You can add font family directly or replace an existing Infima CSS variables. Here is an example.

```css title="src/css/custom.css"
.link {
  font-family: 'Geist', serif;
}

/* with fallback fonts */
.card__title {
  font-family: 'Geist', Helvetica, Arial, sans-serif;
}

/* or replace Infima CSS variables */
:root {
  /* // ... */
  --ifm-font-family-base: 'Geist', serif;
  --ifm-font-family-monospace: 'Geist Mono', serif;
  /* // ... */
}
```

## References

- `stylesheets` API https://docusaurus.io/docs/api/docusaurus-config#stylesheets
