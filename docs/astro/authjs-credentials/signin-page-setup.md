---
sidebar_position: 5
unlisted: true
---

# 5. Sign In Page Setup

Let's make a login form

## Create a sign in page and form

```tsx title="src/pages/signin.astro"
---
import Layout from '@/layouts/Layout.astro';
---
<Layout title="Sign In">
  <form method="POST">
    <label for="password">Username</label>
    <input type="text" id="password" name="username" autocomplete="off" />
    <label for="password">Password</label>
    <input type="password" id="password" name="password" />
    <button type="submit">Sign In</button>
  </form>
</Layout>
```

## Create a new SELECT statement

Find and select a user from username and validate the password.

```ts title="src/libs/select.ts"
import { db, Users, eq } from 'astro:db';
import { verify } from '@node-rs/argon2';

interface User {
  status: 'success' | 'fail' | 'error';
  data?: {
    user?: {
      id: string;
      username: string;
    };
    credential?: string;
  };
  message?: string;
}

export async function getUserFromDb(username: string, password: string): Promise<User> {
  try {
    const response = await db
      .select({
        id: Users._id,
        username: Users.username,
        password: Users.password,
      })
      .from(Users)
      .where(eq(Users.username, username.toLowerCase()));

    const result = response[0];
    const validPassword = await verify(result.password, password);

    // password is wrong
    if (!validPassword) {
      return {
        status: 'fail',
        data: {
          credential: 'Invalid credentials',
        },
      };
    } else {
      return {
        status: 'success',
        data: {
          user: {
            id: result.id,
            username: result.username,
          },
        },
      };
    }
  } catch (error) {
    return {
      status: 'error',
      message: 'Unexpected error occurred',
    };
  }
}
```

:::info
**Why I deflare `User` interface ?**

This is a sign in page. We will return a status `**success**', '**fail**' or '**error**'. Unlike sign up form which is POST method
:::
