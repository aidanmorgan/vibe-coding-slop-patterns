# SLOP.CORRECTNESS.003 — Naive timezone/date handling

**Family:** CORRECTNESS

## Summary

Time bugs are classic because they hide in plain sight. AI code often uses local timestamps, naive
datetimes, and locale-specific strings. It “works” on the author’s machine and then breaks across
regions, DST changes, and audits.  This slop creates inconsistent behavior that’s difficult to
reason about and extremely expensive to debug after the fact.


## Examples (Python)

```python
from datetime import datetime, timedelta
def expires_at():
    return datetime.now() + timedelta(days=1)  # naive local time
```

```python
from datetime import datetime
def serialize_timestamp():
    return datetime.now().strftime("%c")  # locale-dependent representation
```

```python
from datetime import datetime
def sort_by_created(items):
    return sorted(items, key=lambda x: x["created_at"])  # if created_at is non-ISO string, sorts wrong
```

## Remediation

- Store and compute time in UTC; convert to local time only at display boundaries.
- Use timezone-aware datetimes (`datetime.now(timezone.utc)`), and never store locale-formatted strings as canonical data.
- Freeze time in tests and avoid relying on the wall clock.
- For durations/timeouts, use monotonic clocks where possible (or stable time sources).
