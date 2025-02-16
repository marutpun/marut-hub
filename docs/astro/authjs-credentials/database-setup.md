---
sidebar_position: 3
---

# Database Setup

Connect your application with Turso libSQL.

Database is designed to be hit. Why don't we try on production mode for real-world situation?

## Prepare script

Edit the following scripts by adding `--remote`

```json title="package.json
{
  "scripts": {
    //highlight-start
    "dev": "astro dev --remote",
    "build": "astro build --remote"
    //highlight-end
  }
}
```

## Create new Turso account and database.

Create a new account at https://turso.tech and a new database. There are free and paid accounts.

:::tip
We recommend creating a new database, token and URL through the web browser. There's no need to worry about running scripts in the terminal.
:::

:::warning
Copy and save your Turso Token. Once closed, they can't be retrieved and you'll need to create new ones.
:::

## Add environment variables

After you create a new database. Copy and paste your Turso Token and Turso URL in environment variables

```ini title=".env"
ASTRO_DB_REMOTE_URL=libsq........
ASTRO_DB_APP_TOKEN=eyJ........
```

:::warning
You must follow the exact name. If you use a different name, you will need to set it up manually later.
:::

## Migration

We have set up a server (remote) and a database schema (local). It's time to push (migrate) it to server.

Run this script in your terminal

```sh
astro db push --remote
```

Then, visit https://app.turso.tech to see the result.

## Seeding (optional)

Your database is ready. If you want to try add something in your database as a dummy data.

```ts title="db/seed.ts"
//highlight-next-line
import { db, Users } from 'astro:db';

// https://astro.build/db/seed
export default async function seed() {
  //highlight-start
  try {
    await db.insert(Users).values([
      {
        _id: 'user_xoxo_iluvu',
        username: 'maruthub',
        password: 'pa$sw0rD',
      },
    ]);
  } catch {
    console.error('Error:', error);
  }
  //highlight-end
}
```

Then, visit https://app.turso.tech to see the result in `Users` database. (Refresh if you don't see it.)

We'll transform password to random string next chapter.

## References

- Turso Quick Start (for advanced user) https://docs.turso.tech/sdk/ts/quickstart
- Migration to Turso server https://docs.astro.build/en/guides/astro-db/#migrate-from-astro-studio-to-turso
