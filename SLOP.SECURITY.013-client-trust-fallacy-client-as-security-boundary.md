# SLOP.SECURITY.013 — Client-trust fallacy (client as security boundary)

**Family:** SECURITY

## Summary

This slop treats the client as a trusted enforcement point. In Python ecosystems it shows up when
server endpoints rely on client-side checks, hidden buttons, or client-provided role claims.
Attackers don’t use your UI; they call APIs directly.  It’s common in rapid “backendless” builds
where the UI and database are wired quickly and access control is assumed rather than enforced.


## Examples (Python)

```python
def is_admin(request) -> bool:
    # trusts client-provided header/claim without verification
    return request.headers.get("X-Role") == "admin"
```

```python
def delete_invoice(request, invoice_id: str):
    if request.json.get("confirmed") is True:  # client-controlled
        db.execute("DELETE FROM invoices WHERE id=%s", (invoice_id,))
```

```python
def list_invoices(request):
    # UI hides other tenants, but API returns all
    return db.execute("SELECT * FROM invoices").fetchall()
```

## Remediation

- Enforce authorization on the server/data layer, not in the client.
- Treat all client-provided claims as untrusted unless validated (signature verification, token verification, server-side session).
- Add API-level tests that bypass UI and attempt unauthorized actions.
- Adopt a “deny by default” posture and explicit permission checks for every sensitive action.
