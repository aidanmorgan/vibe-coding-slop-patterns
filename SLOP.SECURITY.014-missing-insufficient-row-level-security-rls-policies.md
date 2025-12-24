# SLOP.SECURITY.014 — Missing/insufficient row-level security (RLS) / policies

**Family:** SECURITY

## Summary

In “backendless” architectures where the database is directly reachable by clients, policies are
your backend. Slop occurs when policies are missing, overly permissive, or not tested. The system
works for the builder but leaks data across users or to unauthenticated actors.  Even if you don’t
use Supabase specifically, the pattern generalizes to any policy-driven data access model.


## Examples (Python)

```python
# Pseudocode policy mistake represented in Python as "allow all" filter:
def list_invoices_for_user(user_id: str):
    return db.execute("SELECT * FROM invoices").fetchall()  # no WHERE owner
```

```python
def can_read(row: dict, user_id: str) -> bool:
    return True  # policy that grants everyone everything
```

```python
def query_with_optional_scope(user_id: str | None):
    if user_id is None:
        return db.execute("SELECT * FROM reports").fetchall()  # anon access
    return db.execute("SELECT * FROM reports WHERE owner=%s", (user_id,)).fetchall()
```

## Remediation

- Deny by default: require explicit scoping in every query.
- Add policy tests: verify unauthenticated and cross-user access is rejected.
- Separate service-level credentials from client credentials; clients must be least-privileged.
- Automate policy audits in CI and treat policy changes as security-sensitive code.
