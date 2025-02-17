---
sidebar_position: 1
---

# Guide

Let's discover **Docusaurus in less than 5 minutes**.

## Admonition

Let's understand all admonitions.

:::note
This is simply information. It doesn’t change anything, affect you in any way, or require action. Feel free to ignore it.
:::

:::tip
You **SHOULD** follow these instructions to improve efficiency, save time, and make things easier. It's a good practice that benefits you in the long run.
:::

:::info
This is something you **SHOULD** know. It won’t benefit you directly, change your life, or prevent harm—but it’s still good information to have.
:::

:::warning
You **SHOULD** follow these instructions for the best experience. Ignoring them won’t be dangerous, but it may cause unnecessary difficulty, wasted time, or inconvenience
:::

:::danger
You **MUST** follow these instructions carefully. Failure to do so may result in serious danger, injury, or damage. Stay alert and proceed with caution.
:::

Get started by **creating a new site**.

Or **try Docusaurus immediately** with **[docusaurus.new](https://docusaurus.new)**.

### What you'll need

- [Node.js](https://nodejs.org/en/download/) version 18.0 or above:
  - When installing Node.js, you are recommended to check all checkboxes related to dependencies.

## Generate a new site

Generate a new Docusaurus site using the **classic template**.

The classic template will automatically be added to your project after you run the command:

```bash
npm init docusaurus@latest my-website classic
```

You can type this command into Command Prompt, Powershell, Terminal, or any other integrated terminal of your code editor.

The command also installs all necessary dependencies you need to run Docusaurus.

## Start your site

Run the development server:

```bash
cd my-website
npm run start
```

The `cd` command changes the directory you're working with. In order to work with your newly created Docusaurus site, you'll need to navigate the terminal there.

The `npm run start` command builds your website locally and serves it through a development server, ready for you to view at http://localhost:3000/.

Open `docs/intro.md` (this page) and edit some lines: the site **reloads automatically** and displays your changes.
