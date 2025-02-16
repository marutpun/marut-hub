---
sidebar_position: 2
---

# Schema Setup

Design a database schema with Astro DB.

After installing Astro DB from previous step. You will have a new folder `db` with new 2 files. A configuration file and seeding file.

:::info
Astro DB has an integrated Drizzle ORM.
:::

```ts title="db/config.ts"
import { defineDb } from 'astro:db';

// https://astro.build/db/config
export default defineDb({
  tables: {},
});
```

## Add new `Users` Table

### Declare `Users` schema

```ts title="db/config.ts"
//highlight-next-line
import { defineDb, defineTable } from 'astro:db';

//highlight-next-line
const Users = defineTable({});

// https://astro.build/db/config
export default defineDb({
  tables: {},
});
```

### Add column in table

We want to store basic authentication data, including ID, Username, Password, Creation Date, and Updating Date.

```ts title="db/config.ts"
//highlight-next-line
import { defineDb, defineTable, column, sql, NOW } from 'astro:db';

const Users = defineTable({
  //highlight-start
  columns: {
    _id: column.text({ primaryKey: true }),
    username: column.text({ unique: true }),
    password: column.text(),
    created_at: column.date({ default: NOW }),
    updated_at: column.number({ default: sql`(unixepoch())` }),
  },
  indexes: [{ on: ['username'] }],
  //highlight-end
});

// https://astro.build/db/config
export default defineDb({
  //highlight-next-line
  tables: { Users },
});
```

:::note

- \__id_: Storing primary keys as random strings prevents enumeration attacks by making IDs harder to guess, enhancing security.
- _username_: Username must be unique to prevent duplicates
- _password_: Password must not be empty.
- _created_at_: Automatically storing the record's creation date.
- _updated_at_: Automatically storing the record's updating date in Unix Epoch.
- _indexes_: Add index for improving the speed of data retrieval operations on a table.
  :::

## References

- Column references https://docs.astro.build/en/guides/integrations-guide/db/#table-configuration-reference
