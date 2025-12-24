# SLOP.PERFORMANCE_COST.004 — N+1 query patterns

**Family:** PERFORMANCE_COST

## Summary

N+1 happens when code fetches one record per item in a loop. It reads naturally and passes tests,
but explodes under real datasets. Assistants generate this because it maps directly to “for each
thing, fetch its details.”  In production, N+1 becomes latency spikes and DB load amplification.


## Examples (Python)

```python
def load_users_and_posts(conn, user_ids):
    users = []
    for uid in user_ids:
        users.append(conn.execute("SELECT * FROM users WHERE id=%s", (uid,)).fetchone())
    return users
```

```python
def list_orders_with_items(conn, orders):
    out = []
    for o in orders:
        items = conn.execute("SELECT * FROM order_items WHERE order_id=%s", (o["id"],)).fetchall()
        out.append((o, items))
    return out
```

```python
def enrich(conn, rows):
    for r in rows:
        r["owner"] = conn.execute("SELECT name FROM users WHERE id=%s", (r["owner_id"],)).fetchone()["name"]
    return rows
```

## Remediation

- Batch fetch with `IN (...)`, joins, or preloading; avoid per-item lookups.
- Add integration tests that count queries for common endpoints and fail on regressions.
- Use query planners (`EXPLAIN`) on hot queries and add appropriate indexes.
- Consider caching or denormalization only after fixing query shape.
