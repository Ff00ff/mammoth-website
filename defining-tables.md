# Defining tables

Now that you created your db instance let’s zoom into why and how to define your tables. You define your tables so Mammoth understands the types of your columns. To define your tables you should use the `defineTable` call and you should pass the result to your db instance to make the table available through the db instance.

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
  id uuid PRIMARY KEY DEFAULT ’gen_random_uuid()’,
  name text NOT NULL,
  value integer
);
```

Today, Mammoth only focusses on being an excellent query builder. But in the near future there should be something which generates your migrations based on these table definitions.

