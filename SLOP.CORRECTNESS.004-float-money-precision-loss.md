# SLOP.CORRECTNESS.004 — Float money / precision loss

**Family:** CORRECTNESS

## Summary

Using floating point types for money causes rounding anomalies and inconsistent totals. AI code uses
floats because they’re convenient; money is not convenient. Even tiny rounding bugs can become
customer-facing discrepancies, reconciliation headaches, and trust erosion.  This slop is especially
common in “MVP” code where correctness is assumed because amounts look right in a couple of
examples.


## Examples (Python)

```python
def total_amount(price: float, qty: int) -> float:
    return price * qty  # float rounding issues
```

```python
def add_tax(amount: float) -> float:
    return amount * 1.1  # accumulates float error
```

```python
def split_evenly(total: float, n: int) -> list[float]:
    return [total / n] * n  # rounding mismatch when summing
```

## Remediation

- Use integer minor units (cents) or `decimal.Decimal` with explicit quantization rules.
- Introduce a Money type that pairs amount with currency and forbids raw float arithmetic.
- Add tests for boundary values (0.01 steps, large totals, splitting/rounding behavior).
- Treat money formatting and rounding as domain rules, not incidental math.
