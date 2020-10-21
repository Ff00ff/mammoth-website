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
db.select(db.foo.id).from(db.foo);
```

