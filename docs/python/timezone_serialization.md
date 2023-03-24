---
title: isoformat for js
---

# `isoformat()` for javascript

Postgres has a support of datetime with timezone, type TIMESTAMP:

> For timestamp with time zone, the internally stored value is always in UTC
> (Universal Coordinated Time, traditionally known as Greenwich Mean Time, GMT).
> An input value that has an explicit time zone specified is converted to UTC
> using the appropriate offset for that time zone. If no time zone is stated in
> the input string, then it is assumed to be in the time zone indicated by the
> system's TimeZone parameter, and is converted to UTC using the offset for the
> timezone zone.

More of this [in the official documentation](https://www.postgresql.org/docs/current/datatype-datetime.html).

If there is no tzinfo in the datetime object it equals to the machine timezone
and converts automatically.

For example for SQLA ([TIMESTAMP](https://docs.sqlalchemy.org/en/20/core/type_basics.html#sqlalchemy.types.TIMESTAMP)):

```python
class utcnow(expression.FunctionElement):  # noqa: N801
    type = DateTime()  # noqa: A003
    inherit_cache = True


class Table(Model):
    created_at = Column(TIMESTAMP, server_default=utcnow())
    updated_at = Column(TIMESTAMP, server_default=utcnow(), onupdate=utcnow())
    created_at_with_tz = Column(TIMESTAMP(timezone=True), server_default=utcnow())
    updated_at_with_tz = Column(TIMESTAMP(timezone=True))
```

In case of serialization if there is no timezone info in the db it will throw
data without `+00:00` part and this will confuse javascript `Date` class.

So for `created_at`, `updated_at` for marshmallow library we had to do this:

```python
from datetime import datetime, timezone
from typing import Any, Optional, Union

from marshmallow.fields import DateTime


class DateTimeWithTZ(DateTime):
    def _serialize(
        self,
        value: Optional[datetime],
        attr: Optional[str],
        obj: Any,
        **kwargs: dict[str, Any],
    ) -> Union[str, float, None]:
        if value is None:
            return None
        value = value.replace(tzinfo=timezone.utc)
        return super()._serialize(value, attr, obj, **kwargs)
```

and store all data as `utcnow()` instead of `now()` or provide a tz info manually.

To be continued...
