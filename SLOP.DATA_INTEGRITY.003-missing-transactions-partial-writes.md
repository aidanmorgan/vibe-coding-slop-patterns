# SLOP.DATA_INTEGRITY.003 — Missing transactions / partial writes

**Family:** DATA_INTEGRITY

## Summary

Multi-step workflows that write state in sequence without transactions or compensating actions
create partial, inconsistent state. This passes basic tests, but fails under timeouts, retries, and
partial dependency outages.  Vibe-coded implementations often string operations together because it
reads naturally, but production needs atomicity or a defined saga.


## Examples (Python)

```python
def create_order(conn, order, items):
    conn.execute("INSERT INTO orders(id, status) VALUES(%s, 'created')", (order["id"],))
    for it in items:
        conn.execute("INSERT INTO order_items(order_id, sku) VALUES(%s, %s)", (order["id"], it["sku"]))
    # if anything fails above, state is partial
```

```python
def charge_and_mark_paid(conn, invoice_id: str, amount: int):
    payment.charge(invoice_id, amount)
    # if the update fails, you've charged but not marked paid
    conn.execute("UPDATE invoices SET paid=true WHERE id=%s", (invoice_id,))
```

```python
def reserve_then_ship(conn, item_id: str):
    conn.execute("UPDATE stock SET reserved=reserved+1 WHERE item_id=%s", (item_id,))
    ship(item_id)  # external side effect; no compensation if ship fails
```

## Remediation

- Use database transactions for atomic units; wrap multi-step writes in `BEGIN/COMMIT` with rollback on error.
- For distributed steps (payments, shipping), implement a saga/state machine with compensating actions and idempotency.
- Add tests that simulate failures between steps (exception after step 1, timeout on step 2) and assert state remains consistent.
- Track and reconcile “in-flight” states explicitly (pending, processing) rather than pretending everything is immediate.
