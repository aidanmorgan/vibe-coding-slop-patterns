# SLOP.SECURITY.006 — Broken authorization / IDOR

**Family:** SECURITY

## Summary

IDOR-style bugs occur when code fetches resources by identifier without checking ownership or tenant
scope. AI code often adds authentication checks (“is logged in”) but omits authorization checks (“is
allowed to access this specific resource”).  This is one of the most common real-world security
failures in CRUD-heavy systems and is easy to miss in happy-path tests.


## Examples (Python)

```python
def get_invoice(conn, invoice_id: str):
    # missing user/tenant scoping
    return conn.execute("SELECT * FROM invoices WHERE id = %s", (invoice_id,)).fetchone()
```

```python
def update_profile(conn, user_id: str, payload: dict):
    # accepts user_id from request and updates it without verifying caller owns it
    conn.execute("UPDATE users SET name=%s WHERE id=%s", (payload["name"], user_id))
```

```python
def download_report(conn, report_id: str):
    return conn.execute("SELECT * FROM reports WHERE id=%s", (report_id,)).fetchone()  # no scope
```

## Remediation

- Make “principal scope” mandatory: every query must include tenant/user constraints, not just the object ID.
- Centralize authorization so it can’t be forgotten in individual handlers.
- Add tests proving user A cannot read/update user B’s resources.
- For multi-tenant systems, enforce tenant constraints at the database layer where possible (RLS, views, or mandatory tenant columns).
