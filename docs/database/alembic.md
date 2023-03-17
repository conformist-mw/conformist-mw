---
title: How to use Alembic from the beginning
---

### Version file names

By default variable `file_template` is commented out in `alembic.ini` and
revisions creates with the hash at the beginning of the filename, so they are
cannot be sorted.

To fix that we need to set how to name them:

```ini
# template used to generate migration file names; The default value is %%(rev)s_%%(slug)s
# Uncomment the line below if you want the files to be prepended with date and time
file_template = %%(year)d_%%(month).2d_%%(day).2d_%%(hour).2d%%(minute).2d-%%(rev)s_%%(slug)s
```

### Constraint naming

By default constrains names are defined by database itself and it is possible
that during migration `alembic` fails to find target constraint.

Best option here is to set rules for naming, for example:

```python
from sqlalchemy import Metadata, DeclarativeBase

convention = {
    "ix": "ix_%(column_0_label)s",
    "uq": "uq_%(table_name)s_%(column_0_name)s",
    "ck": "ck_%(table_name)s_%(constraint_name)s",
    "fk": "fk_%(table_name)s_%(column_0_name)s_%(referred_table_name)s",
    "pk": "pk_%(table_name)s",
}

Model = DeclarativeBase()
Model.metadata = MetaData(naming_convention=convention)
```

Links:

- [sqlalchemy docs](https://docs.sqlalchemy.org/en/20/core/constraints.html#configuring-constraint-naming-conventions)
- [alembic docs](https://alembic.sqlalchemy.org/en/latest/naming.html)

### Auto detection

By default `alembic` ignores type changes in columns, so we have to set it 
up separately.

Within `env.py`:

```python {12}
def run_migrations_online():
    connectable = engine_from_config(
        config.get_section(config.config_ini_section),
        prefix="sqlalchemy.",
        poolclass=pool.NullPool,
    )

    with connectable.connect() as connection:
        context.configure(
            connection=connection,
            target_metadata=target_metadata,
            compare_type=True,
        )

        with context.begin_transaction():
            context.run_migrations()
```

Links:

[alembic autogeneration](https://alembic.sqlalchemy.org/en/latest/autogenerate.html#what-does-autogenerate-detect-and-what-does-it-not-detect)
