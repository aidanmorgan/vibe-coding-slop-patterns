# SLOP.CORRECTNESS.001 — Edge-case blindness (happy path only)

**Family:** CORRECTNESS

## Summary

AI-generated code frequently solves the obvious path and ignores production reality: missing fields,
empty lists, retries, duplicate submissions, partial state, concurrency, and timeouts. The issue
isn’t “bugs happen”—it’s systematic omission of invariants and error semantics.  This slop is
dangerous because it often passes basic tests and works for the initial user journey, failing only
under real inputs or adversarial conditions.


## Examples (Python)

```python
def first_item_id(data: dict) -> str:
    return data["items"][0]["id"]  # crashes if items missing/empty
```

```python
def normalize_name(payload: dict) -> str:
    return payload["user"]["name"].strip()  # KeyError if missing
```

```python
def mark_paid(invoice_id: str) -> None:
    # no idempotency; webhook replay can double-apply side effects
    db.execute("UPDATE invoices SET paid = true WHERE id = %s", (invoice_id,))
```

## Remediation

- Write invariants explicitly and test them (e.g., “an invoice can’t be paid twice”, “tenant A can’t see tenant B”).
- Validate inputs at boundaries using schemas (reject/normalize before business logic).
- Add negative-path tests as a standard: missing field, empty list, duplicate request, replay, timeout.
- Prefer fail-closed behavior: if uncertain, raise a typed error rather than guessing.
