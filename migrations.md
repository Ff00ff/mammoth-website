# Migrations

Mammoth supports migrations via `@ff00ff/mammoth-cli` command line interface. You can execute your migrations using this interface, but you can also generate migrations based on changes to your tables. This saves you from repeating yourself repeating yourself!

```text
npm i -D @ff00ff/mammoth-cli
```

Mammoth's command line interface contains two commands.

* `mammoth next` generates your next migration based on your defined tables and any existing migrations
* `mammoth migrate` applies the migrations to your database

{% hint style="info" %}
Migrations, or specifically `@ff00ff/mammoth-cli`, is in beta. You can start using it but some changes cannot automatically be detected.
{% endhint %}

Migrations are written to `./migrations` and number `001` till `999` \(at some point you can squash them\). Migrations are written in pure SQL.

### Generating migrations

Say, in a new project with no tables nor migrations, you define a table `foo`.

```typescript
const foo = defineTable({
  id: uuid().primaryKey().default(`gen_random_uuid()`),
  name: text().notNull(),
});
```

Now, if you run `mammoth next` this automatically creates `migrations/001.sql` for you with the below contents.

```sql
CREATE TABLE foo (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  name text NOT NULL
);
```

If you change your table definition and re-run `mammoth next` it will automatically generate the next migration file for you. For example: I add another column to `foo`.

```typescript
const foo = defineTable({
  id: uuid().primaryKey().default(`gen_random_uuid()`),
  name: text().notNull(),
  value: integer(),
});
```

And I run `mammoth next` again. This generates `migrations/002.sql` for me with the below contents.

```text
ALTER TABLE foo ADD COLUMN value integer;
```

This should make it very easy to evolve your data model. Of course, you can always edit your migrations and add any other changes you feel are neccesary. You can also manually add migrations e.g. to update data instead of alter table definitions.

{% hint style="danger" %}
Even though Mammoth generates the migrations for you, you must review the migrations yourself before commiting the migrations.
{% endhint %}

### Applying migrations

Once your migrations are written, you can execute them through `mammoth migrate`. This executes the migrations in order one-by-one. Once a migration is applied, Mammoth stores this data in the `migration` table in your database, together with a checksum of the migration. You must not edit a migration after it has been applied.

Mammoth does not support rollback and instead advises to go with a roll-forward strategy. If you ever need to undo changes, create another migration to revert the previously applied changes.

