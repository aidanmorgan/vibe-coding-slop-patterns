# SLOP.PERFORMANCE_COST.003 — Unbounded queries / missing pagination

**Family:** PERFORMANCE_COST

## Summary

“List everything” queries work on day one and collapse at scale. AI code often omits pagination
because it complicates the API and UI, so it returns all rows by default. That becomes memory
blowups, slow endpoints, and expensive database scans.  This slop is especially common in CRUD
scaffolds and “admin list” screens.


## Examples (Python)

```python
def list_events(conn):
    return conn.execute("SELECT * FROM events").fetchall()
```

```python
def list_users(conn):
    return conn.execute("SELECT id, name FROM users ORDER BY created_at DESC").fetchall()  # no limit
```

```python
def list_logs(conn, since):
    return conn.execute("SELECT * FROM logs WHERE ts > %s", (since,)).fetchall()  # potentially huge
```

## Remediation

- Require pagination for list endpoints; enforce default limits and maximums.
- Use cursors or keyset pagination for large datasets.
- Add load tests that simulate large tables and ensure endpoints remain bounded.
- Treat missing pagination as a design defect, not a performance optimization to “do later.”
