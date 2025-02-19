---
sidebar_position: 4
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# 4. Sign Up Page Setup

We'll create a maintainable and readable CRUD authentication.

## Install Nano ID and @node-rs/argon2

nanoid is a package for generating an ID and @node-rs/argon2 is a package for hash password.

<Tabs groupId="package-manager">
  <TabItem value="npm" label="npm" default>
    ```sh
    npm install nanoid @node-rs/argon2
    ```
  </TabItem>
  <TabItem value="pnpm" label="pnpm">
    ```sh
    pnpm add nanoid @node-rs/argon2
    ```
  </TabItem>
  <TabItem value="yarn" label="Yarn">
    ```sh
    yarn add nanoid @node-rs/argon2
    ```
  </TabItem>
</Tabs>

## Create a sign up page and form

```tsx title="src/pages/signup.astro"
---
import Layout from '@/layouts/Layout.astro';
---

<Layout>
  <form method="POST">
    <label for="username">Username</label>
    <input type="text" id="username" name="username" autocomplete="off" />
    <label for="password">Password</label>
    <input type="password" id="password" name="password" />
    <label for="confirmPassword">Confirm Password</label>
    <input type="password" id="confirmPassword" name="confirmPassword" />
    <button type="submit">Sign Up</button>
  </form>
</Layout>

```

:::danger
Do not **FORGET** to include `id` and `name` attribute in each input element.
:::

## Create types for response data.

We'll make a scalable and maintainable response with JSend format. Create new 2 types files.

```ts title="src/types/index.ts"
export type JSend<T> =
  | { status: 'success'; data: T | null; message?: string }
  | { status: 'fail'; data?: T; message?: string }
  | { status: 'error'; message?: string }
  | { status: ''; data?: T; message?: string };
```

:::info

`<T>` is a placeholder or unknown types. You can use `<Placeholder>` or any name for simplicity.
:::

```ts title="src/types/User.ts"
export interface User {
  // success handle
  user?: {
    _id?: string;
    username?: string;
    password?: string;
    createdAt?: Date;
  }[];
  // failure handle
  username?: string;
  password?: string;
  confirmPassword?: string;
}
```

## Create a new INSERT statement

Copy and paste the following snippet in insert files.

```ts title="src/libs/insert.ts"
import { db, Users } from 'astro:db';
import { nanoid } from 'nanoid/non-secure';
import { hash } from '@node-rs/argon2';
import type { JSend } from '@/types';
import type { User } from '@/types/User';

export async function createUser(username: string, password: string): Promise<JSend<User>> {
  const userIdGenerate = nanoid(10);

  try {
    await db.insert(Users).values({
      _id: userIdGenerate,
      username: username.toLowerCase(),
      password: await hash(password),
    });

    return {
      status: 'success',
      data: null,
    };
  } catch (error) {
    if (error instanceof Error) {
      return {
        status: 'error',
        message: error.message,
      };
    }

    return {
      status: 'error',
      message: 'An unknown error occurred',
    };
  }
}
```

:::info
**Why I use `nanoid/non-secure` ?**

By default, Nano ID uses hardware random bytes generation for security and low collision probability.

Non-Secure doesn't use hardware. It only generates a random string. If you're aware of collision. You may increase the size to default or higher (default is 21 characters).

**Why I use `String.toLowerCase()`**

To simplify sign-in, all usernames are converted to lowercase. This prevents confusion about uppercase or lowercase letters, making login easier and error-free.
:::

## Apply `createUser()` to sign up page with Promise

```tsx title="src/pages/signup.astro"
---
import Layout from "@/layouts/Layout.astro";
//highlight-next-line
import { createUser } from '@/libs/insert';

//highlight-start
if (Astro.request.method === 'POST') {
  try {
    // get FormData instance
    const signUpData = await Astro.request.formData();
    const username = signUpData.get('username') as string;
    const password = signUpData.get('password') as string;

    // add new user and wait for returning status
    await createUser(username, password);
  } catch (error) {
    console.error(error.message);
  }
}
//highlight-end
---
<Layout>
// ...
</Layout>

```

Let's go to sign up page on web server http://localhost:4321/signup and try to register a new account.

Then, visit https://app.turso.tech to see the result in database. You'll see that password has been hashed and salted.

| \_id       | username | password                             | created_at          | updated_at |
| ---------- | -------- | ------------------------------------ | ------------------- | ---------- |
| s2SLxym74Z | admin    | $argon2id$v=19$m=19456,t=2,p=1$eM... | 2025-02-17 03:08:16 | 1739761234 |

Let's move to the next section (optional) or next chapter.

---

## Handling duplicate usernames (optional)

Would you like to reject a username if it already exists? Add the following code snippet.

### Query handle

```ts title="src/libs/insert.ts"
try {
  // ...
} catch (error) {
  // highlight-start
  if (error.message.includes('UNIQUE constraint failed: Users.username')) {
    return {
      status: 'fail',
      data: { username: 'Username already exists' },
    };
  }
  // highlight-end
  // ...
}
```

### Server-side handle

Add a new server-side variable and we'll pass it to client-side. Declare `signUpResult` before `Astro.request.method` condition.

```tsx title="src/pages/signup.astro"
---
import Layout from '@/layouts/Layout.astro';
import { createUser } from '@/libs/insert';
//highlight-start
import type { JSend } from '@/types';
import type { User } from '@/types/User';
//highlight-end

//highlight-next-line
let signUpResult: JSend<User> = { status: '', data: {} as User };

if (Astro.request.method === 'POST') {
  try {
    // ...
  } catch {
    if (error instanceof Error) {
      console.error(error.message);
      //highlight-next-line
      signUpResult = { status: 'error', message: error.message };
    }
  }
}
```

### Client-side UI

Add `<form>` and new empty text `<p>` tag with `id`

```tsx title="src/pages/signup.astro"
---
// ...
---
<Layout>
  <form method="POST">
    <label for="username">Username</label>
    <input type="text" id="username" name="username" autocomplete="off" />
    // highlight-next-line
    {signUpResult.status === 'fail' && signUpResult?.data?.username && <p>{signUpResult.data.username}</p>}
    <label for="password">Password</label>
    <input type="password" id="password" name="password" />
    <label for="confirmPassword">Confirm Password</label>
    <input type="password" id="confirmPassword" name="confirmPassword" />
    <button type="submit">Sign Up</button>
  </form>
</Layout>
```

## Post setup `signup.astro`

```tsx title="src/pages/signup.astro"
---
import Layout from "@/layouts/Layout.astro";
import { createUser } from '@/libs/insert';
import type { JSend } from '@/types';
import type { User } from '@/types/User';

let signUpResult: JSend<User> = { status: '', data: {} as User };

if (Astro.request.method === 'POST') {
  try {
    const signUpData = await Astro.request.formData();
    const username = signUpData.get('username') as string;
    const password = signUpData.get('password') as string;

    await createUser(username, password);
  } catch (error) {
    if (error instanceof Error) {
      console.error(error.message);
      signUpResult = { status: 'error', message: error.message };
    }
  }
}
---
<Layout>
  <form method="POST">
    <label for="username">Username</label>
    <input type="text" id="password" name="username" autocomplete="off" />
    {signUpResult.status === 'fail' && signUpResult?.data?.username && <p>{signUpResult.data.username}</p>}
    <label for="password">Password</label>
    <input type="password" id="password" name="password" />
    <label for="confirmPassword">Confirm Password</label>
    <input type="password" id="confirmPassword" name="confirmPassword" />
    <button type="submit">Sign Up</button>
  </form>
</Layout>
```

## Quiz Time !

Try to handle an empty username, empty password and unmatch confirm password and return (response) with JSend format.

**Hint**

- Edit `src/pages/signup.astro` file.

<details>
  <summary>Do you give up? Click to reveal an answer.</summary>

```tsx title="src/pages/signup.astro"
---
//highlight-next-line
let signUpResult: JSend<User> = { status: '', data: {} as User };

if (Astro.request.method === 'POST') {
  try {
    const signUpData = await Astro.request.formData();
    const username = signUpData.get('username') as string;
    const password = signUpData.get('password') as string;
    //highlight-next-line
    const confirmPassword = signUpData.get('confirmPassword') as string;

    //highlight-start
    if (!username || username.trim() === '') {
      signUpResult = {
        status: 'fail',
        data: { ...signUpResult.data, username: 'Username is required' },
      };
    }
    //highlight-end

    //highlight-start
    if (!password || password.trim() === '') {
      signUpResult = {
        status: 'fail',
        data: { ...signUpResult.data, password: 'Password is required' },
      };
    }
    //highlight-end

    //highlight-start
    if (!confirmPassword || confirmPassword.trim() === '') {
      signUpResult = {
        status: 'fail',
        data: { ...signUpResult.data, confirmPassword: 'Confirm password is required' },
      };
    }
    //highlight-end

    //highlight-start
    if (password !== confirmPassword) {
      signUpResult = {
        ...signUpResult,
        status: 'fail',
        data: { ...signUpResult.data, confirmPassword: 'Confirm password does not match' },
      };
    }
    //highlight-end
    //
  } catch {}
}

---
```

:::note

We use the spread operator (...) to prevent errors from overwriting each other. If both username and password have errors, the spread operator ensures both errors are kept in signUpResult without replacing the previous one. This way, multiple errors can stack up and show together.
:::

</details>

## Summary

We created a sign up page and a sign up form for inserting new username and password into a database. We use Nano ID as an ID and @node-rs/argon2 as a hashing password. Finally, we handle the errors from server-side to show in client-side with zero javascript in browser. It likes NextJS with server actions.

## References

- Nano ID https://github.com/ai/nanoid
- node-rs https://github.com/napi-rs/node-rs
- JSend format https://github.com/omniti-labs/JSend
- Astro DB Insert https://docs.astro.build/en/guides/astro-db/#insert
- Astro request method https://docs.astro.build/en/guides/on-demand-rendering/#astrorequestmethod
- define:vars https://docs.astro.build/en/reference/directives-reference/#definevars
