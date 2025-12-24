# SLOP.PERFORMANCE_COST.005 — Inefficient algorithms / accidental quadratic work

**Family:** PERFORMANCE_COST

## Summary

Assistants often produce correct-but-inefficient code: nested loops, repeated conversions, or
repeated allocations. It’s invisible in small examples and becomes expensive under production load.
Because AI code is optimized for readability and directness, it can miss algorithmic costs and data-
structure choices.


## Examples (Python)

```python
def build_list(items):
    out = []
    for x in items:
        out += [x * 2]  # repeated list reallocations
    return out
```

```python
def contains_duplicates(items):
    for i in range(len(items)):
        for j in range(len(items)):
            if i != j and items[i] == items[j]:
                return True
    return False
```

```python
def parse_many(lines):
    # needless encode/decode churn
    return [line.encode("utf-8").decode("utf-8") for line in lines]
```

## Remediation

- Profile hot paths and set performance budgets for critical endpoints.
- Prefer linear-time data structures (sets/dicts) over nested loops for lookups.
- Add microbenchmarks for core operations that must remain efficient.
- Review generated code for algorithmic complexity, not just correctness.
