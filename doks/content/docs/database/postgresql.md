---
title: Tips for PostgreSQL
---

How to know to which table/column the foreign key constraint belong:

```sql
SELECT conname, conrelid::regclass AS table_name, a.attname AS column_name
FROM pg_constraint c
JOIN pg_class r ON c.conrelid = r.oid
JOIN pg_attribute a ON r.oid = a.attrelid AND a.attnum = ANY (c.conkey)
WHERE conname in (SELECT conname from pg_constraint where contype = 'f');
```

[Rename Postgres Table with Alembic](https://petegraham.co.uk/rename-postgres-table-with-alembic/)
