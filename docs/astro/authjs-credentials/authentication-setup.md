---
sidebar_position: 5
---

# 5. Authentication Setup

Now we're going to build a core of Auth.js, an authenticatino

## Add environment variables

Let's add Auth.js secret key. A key for decoding JWT.

Run this script to get Auth secret.

```ini
openssl rand -base64 32
```

And put in `.env` file.

```ini title=".env"
AUTH_TRUST_HOST=true
AUTH_SECRET=PUT_YOUR_AUTH_SECRET_HERE
```

:::danger
Do **NOT** randomly type Auth secret from your keyboard.

Do **NOT** share Auth secret to anyone.
:::

:::tip
You don't know how to use `openssl` command? Try this web-based generator https://generate-secret.vercel.app/32
:::

## Create user session Types

Copy and paste the following Types in `src/types/User.ts`

```ts title="src/types/index.ts"
import type { User as AuthUser } from '@auth/core/types';

// ...
export interface ExtendUser extends AuthUser {
  username?: string;
}
// ...
```

## Auth.js configuration file

Create an `auth.config.ts` file in your project’s root directory with general configuration.

```ts title="auth.config.ts"
import { defineConfig } from 'auth-astro';

export default defineConfig({});
```

## Add Credentials provider in config file

Make a new variable to store data and use it in `defineConfig`

```ts title="auth.config.ts"
import { defineConfig } from 'auth-astro';
//highlight-start
import Credentials from '@auth/core/providers/credentials';
import type { AuthConfig } from '@auth/core';
//highlight-end

//highlight-start
const authConfig: AuthConfig = {
  providers: [Credentials({})],
};
//highlight-end

//highlight-next-line
export default defineConfig(authConfig);
```

## Add sign in form

We'll make a basic sign in form. If you prefer custom sign in page, We'll do it later.

Add a basic sign in input fied and attribute as much as you can.

Ps. You can add `name` property and it will render in `<button>` element in sign in page.

```ts title="auth.config.ts"
// ...
const authConfig: AuthConfig = {
  providers: [
    Credentials({
      // Render as <button type="submit">Sign in with Love</button>
      // remove to use default <button type="submit">Sign in with Credentials</button>'
      // highlight-next-line
      name: 'Love',

      //highlight-start
      credentials: {
        username: { label: 'Username', name: 'username', autoComplete: 'off' },
        password: { label: 'Password', name: 'password', type: 'password' },
      },
      //highlight-end
    }),
  ],
};
// ...
```

View your sign in page at http://localhost:4321/api/auth/signin and **Sign in with Love**.

:::note

**Why you always be authorized automatically?**

Because we haven't implement authorization yet. Auth.js doesn't know your username neither password.
:::

## Add `authorize()` function

This is the crucial part. Auth.js will do the authentication in background.

```ts title="auth.config.ts"
import { defineConfig } from 'auth-astro';
import Credentials from '@auth/core/providers/credentials';
import type { AuthConfig } from '@auth/core';
//highlight-next-line
import type { ExtendUser } from '@/types';

const authConfig: AuthConfig = {
  providers: [
    Credentials({
      // ...
      //highlight-start
      async authorize(credentials): Promise<ExtendUser | null> {
        const user = { id: 's2SLxym74Z', username: 'admin' };

        // we will fix this later. just for dummy.
        if (credentials.username === 'admin') {
          return user;
        } else {
          return null;
        }
      },
      //highlight-end
    }),
  ],
};
// ...
```

:::info
Think of `authorize()` like a hotel receptionist who checks if you're allowed to enter based on your reservation. The receptionist **verifies** your booking because **your password is correct**.

However, after checking in, The security guard will kick you out since the receptionist and security guard work in different departments.

Yes, you can check in again as many times as you want. But each time, the security guard will kick you out, creating a loop **until we implement a walkie-talkie (cookie)** to remember your status in next step.
:::

## Add JWT and sessions `callbacks` function

Auth.js default session is JWT.

First, let's add new Types

```ts title="src/types/index.ts"
import type { User as AuthUser } from '@auth/core/types';
//highlight-next-line
import type { JWT } from '@auth/core/jwt';

// ...
//highlight-start
export interface ExtendedJWT extends JWT {
  user?: {
    id?: string;
    username?: string;
  };
}
//highlight-end
// ...
```

```ts title="auth.config.ts"
import { defineConfig } from 'auth-astro';
import Credentials from '@auth/core/providers/credentials';
import type { AuthConfig } from '@auth/core';
//highlight-start
import type { ExtendedJWT, ExtendUser } from '@/types';
import type { JWT } from '@auth/core/jwt';
//highlight-end

const authConfig: AuthConfig = {
  // ...
  //highlight-start
  callbacks: {
    async jwt({ token, user }: { token: JWT; user: ExtendUser }): Promise<ExtendedJWT | null> {
      if (user) {
        //if user valid, merge and return a new token
        const newTokenData = {
          ...token,
          user: { id: user.id, username: user.username },
        };
        return newTokenData;
      }
      // If no user data, return null to destroy all data
      if (!token.user) {
        return null;
      }

      return token;
    },
    async session({ token, session }: { token: JWT; session: Session }) {
      //if token.user (newTokenData) is valid, merge into existing session for using in the future.
      if (token.user) {
        session.user = {
          ...session.user,
          ...token.user,
        };
      }
      return session;
    },
  },
  //highlight-end
  // ...
};
```

:::note
I want `session` returns everything from `jwt` data. Thus, I use spread operator (...).
:::

:::info

**Keycard (JWT) contains your data** but it doesn’t have the ability to communicate with the rest of the hotel staff.

**Walkie-talkie (session)** is what informs the entire all the hotel staff that you are verified and **allowed to stay**.

So, if you don’t insert your keycard into the room key switch, the **walkie-talkie (cookie) won't activate**. The security guard are **still able to kick you, even though the receptionist gave you the keycard (JWT)**.

The JWT is your proof of identity, while the session is what updates the entire hotel system.
:::

Visit sign in page at http://localhost:4321/api/auth/signin and try sign in with username **admin** (no password).

---

## Check authentication status (optional)

Am I really authenticated? Let's get started.

Create a new page `src/pages/dashboard.astro` to make a protected page.

```jsx title="src/pages/dashboard.astro"
---
import Layout from '@/layouts/Layout.astro';
import { getSession } from 'auth-astro/server';

const session = await getSession(Astro.request);
---
<Layout title="Dashboard">
  <h1>
    Welcome,
    {session ? session.user?.username : 'Who are you?'}
  </h1>
</Layout>

```

## References

- Auth.js Credentials `authorize()` https://authjs.dev/reference/core/providers/credentials#authorize
- Auth.js Session Strategiest https://authjs.dev/concepts/session-strategies
- Auth.js Callbacks https://next-auth.js.org/configuration/callbacks
