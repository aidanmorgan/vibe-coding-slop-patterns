# SLOP.OPERABILITY.001 — Silent exception swallowing

**Family:** OPERABILITY

## Summary

Silently ignoring exceptions makes systems un-debuggable and can turn transient errors into
permanent data inconsistencies. Assistants often do this to “make the app work” and keep tests
green, but it destroys observability and correctness.  Silent failure is worse than loud failure
because it masks the true state of the system.


## Examples (Python)

```python
def save(conn, row):
    try:
        conn.execute("INSERT INTO t VALUES(%s)", (row,))
    except Exception:
        pass  # error disappears
```

```python
def parse_int(x: str) -> int:
    try:
        return int(x)
    except Exception:
        return 0  # silently changes meaning
```

```python
def upload(data: bytes):
    try:
        storage.put(data)
    except Exception:
        return None  # failure indistinguishable from success
```

## Remediation

- Never swallow exceptions in production paths. If you handle an error, do it explicitly and with intent.
- Log errors with context (request ID, user/tenant, operation) and return typed errors to callers.
- Add tests that force failure and assert the system surfaces the error correctly.
- Use domain-specific exceptions and error codes so failures are actionable.
