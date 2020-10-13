# Getting started

Of course, first, you need to install Mammoth.

{% tabs %}
{% tab title="NPM" %}
```text
npm i @ff00ff/mammoth@1.0.0-rc.7
```
{% endtab %}

{% tab title="Yarn" %}
```
yarn add @ff00ff/mammoth@1.0.0-rc.7
```
{% endtab %}

{% tab title="Deno" %}
```
import { defineDb } from 'https://deno.land/x/mammoth@v1.0.0-rc.7'
```
{% endtab %}
{% endtabs %}

Because Mammoth is a query builder pur sang it doesn’t include a database driver. This means you have to manage your connection pool to your database yourself and pass a query executor function to Mammoth’s `defineDb` call.

The query executor function, or `QueryExecutorFn`, receives the query as a string and it’s parameters as arguments and should return a promise with the resulting rows and the affected row count.

Below is an example on how to set this all up. Please note that the pool in this case uses a `pg` pool but you can use any pool you want.

{% tabs %}
{% tab title="Node.js" %}
```typescript
import { defineDb } from '@ff00ff/mammoth';
import { pool } from './pool';
import { foo, bar } from './tables';

const db = defineDb({ foo, bar }, async (query, parameters) => {
  const result = await pool.query(query, parameters);

  return {
    affectedCount: result.rowCount,
    rows: result.rows,
  };
});
```
{% endtab %}
{% endtabs %}

As you can see the above example imports a set of tables from `./tables` . These are the tables of your database schema. What they do and how they are defined are explained in the next chapter.

