---
description: A type-safe query builder pur sang
---

# Mammoth

* Type-safe query builder—sticking as close to SQL as possible
* BYOCP \(bring your own connection pool\)
* Supports Postgres only
* Excellent autocomplete
* Train\_case to camelCase and vice versa conversion
* Auto-migration generation based on your schema changes
* [Awesome CLI to run your migrations](https://github.com/ff00ff/mammoth-cli)

```typescript
db.with(
  `regionalSales`,
  () =>
    db
      .select(db.orderLog.region, sum(db.orderLog.amount).as(`totalSales`))
      .from(db.orderLog)
      .groupBy(db.orderLog.region),
  `topRegions`,
  ({ regionalSales }) =>
    db
      .select(regionalSales.region)
      .from(regionalSales)
      .where(
        regionalSales.totalSales.gt(
          db.select(sum(regionalSales.totalSales).divide(10)).from(regionalSales),
        ),
      ),
  ({ topRegions }) =>
    db
      .select(
        db.orderLog.region,
        db.orderLog.product,
        sum(db.orderLog.quantity).as(`productUnits`),
        sum(db.orderLog.amount).as(`productSales`),
      )
      .from(db.orderLog)
      .where(db.orderLog.region.in(db.select(topRegions.region).from(topRegions)))
      .groupBy(db.orderLog.region, db.orderLog.product),
);
```

The above is a crazy example what Mammoth can do. The best part is that the return type is automatically inferred. No worries you're making a mistake there. And all of this works without a build step. No need to have a file watcher running.

Mammoth consists of a couple of repositories:

* The query builder [https://github.com/ff00ff/mammoth](https://github.com/ff00ff/mammoth)
* The accompanying command line interface [https://github.com/Ff00ff/mammoth-cli](https://github.com/Ff00ff/mammoth-cli)
* This documentation website [https://github.com/Ff00ff/mammoth-website](https://github.com/Ff00ff/mammoth-website)

