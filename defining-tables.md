# Defining tables

Now that you created your db instance let’s zoom into why and how to define your tables. You define your tables so Mammoth understands the types of your columns and the command line interface can help you generate migrations. To define your tables you use the `defineTable` call and you pass the result to your db instance to make the table available through the db instance.

The `defineTable` syntax should be very similar to SQL’s `CREATE TABLE` syntax.

{% tabs %}
{% tab title="Node.js" %}
```typescript
import { defineTable, uuid, text, integer } from '@ff00ff/mammoth';

export const foo = defineTable({
  id: uuid().primaryKey().default(`gen_random_uuid()`),
  name: text().notNull(),
  value: integer(),
});
```
{% endtab %}
{% endtabs %}

This is equivalent to the below SQL.

```sql
CREATE TABLE foo (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  name text NOT NULL,
  value integer
);
```

### Data types

The Mammoth data types map to the equally-named Postgres data type and an equivalent TypeScript return type. E.g. Mammoth's `text()` configures Postgres's `text` and maps to TypeScript `string`.

{% hint style="info" %}
Postgres's built-in general-purpose data types should all be supported in Mammoth already. If not, please file an issue. In the meantime, you can always create a custom data type.
{% endhint %}

You can always re-configure the TypeScript type. For example, if you reconfigured `pg` to map `bigint` to JavaScript's `BigInt` like the below.

```typescript
// Type Id 20 = BIGINT | BIGSERIAL
pg.types.setTypeParser(20, BigInt);

// 1016 = Type Id for arrays of BigInt values
const parseBigIntArray = pg.types.getTypeParser(1016);
pg.types.setTypeParser(1016, a => parseBigIntArray(a).map(BigInt));
```

{% hint style="danger" %}
Currently `pg` returns `string` instead of the newly-added `BigInt` type. See for some context and a workaround. [https://github.com/brianc/node-pg-types/issues/78](https://github.com/brianc/node-pg-types/issues/78)
{% endhint %}

You can just pass a different type to the `bigint()` call.

```typescript
const foo = defineTable({
  bar: bigint<BigInt>().notNull(),
});
```

To avoid having to specify the type every time you may consider creating a reusable data type.

```typescript
// You can name this as you wish e.g. bigint 
export const bigintAsJsBigInt = () => dataType<BigInt>(`bigint`);
```

And you can also create a reusable custom data type. For example if you wish to add an unsupported data type.

```typescript
export const mySpecialType = () => dataType<number>(`myspecialtype`);
```

Which allows you to define the column like below. Please note that you must wrap the `dataType` function in a function as `dataType()` returns a mutable object.

```typescript
const foo = defineTable({
  bar: mySpecialType().notNull(),
});
```

