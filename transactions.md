# Transactions

Even though Mammoth does not include a database driver and thus is not responsible to manage transactions, transactions are a key construct in Postgres and it’s important to be able to use them. Luckily, it‘s fairly straightforward to add in your codebase when using Mammoth. The below shows a db instance \(as previously constructed through the `defineDb` call\) where everything is wrapped in a transaction.

```typescript
await wrapInTransaction((db) => {
  // This db instance and everything in this scope 
  // should be inside a transaction. So the below 
  // insert statements are all inside a single 
  // transaction.
  await db.insertInto(db.foo).values({ name: `Test` });
  
  await db.insertInto(db.bar).values({ name: `Test` });
});
```

And the actual implementation to get this working is below.

```typescript
const wrapInTransaction = (callback: (db: Db) => any) => {
  const client = await pool.connect()
  try {
    await client.query('BEGIN');
    
    const db = defineDb(tables, (query, parameters) => {
      const result = client.query(query, parameters);
      return {
        affectedCount: result.rowCount,
        rows: result.rows,
      };
    });
    
    const result = await callback(db);
    
    await client.query('COMMIT')
    return result;
  } catch (e) {
    await client.query('ROLLBACK')
    throw e
  } finally {
    client.release()
  }
}
```

This is just a quick example to get you started. Of course you can also decide to expose a rollback function to be able to programmatically rollback the transaction without having to rely on exceptions.

