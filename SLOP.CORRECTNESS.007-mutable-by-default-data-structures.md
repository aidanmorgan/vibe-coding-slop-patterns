# SLOP.CORRECTNESS.007 — Mutable-by-default data structures

**Family:** CORRECTNESS

## Summary

AI assistants default to mutable data structures (standard lists, dictionaries, and non-frozen dataclasses) because they are the path of least resistance in Python. This leads to "spooky action at a distance" bugs where data is modified unexpectedly by downstream consumers, especially in async or concurrent contexts. While Python allows mutability, "vibe-coded" systems rarely have the discipline to manage it safely, leading to state corruption that is hard to trace.

## Examples (Python)

```python
from dataclasses import dataclass

@dataclass
class UserConfig:
    # Mutable by default; a function receiving this can silently modify it
    settings: dict
    roles: list[str]
```

```python
def process_items(items: list):
    # modifies the list in-place, affecting the caller's reference
    items.append("processed")
    return items
```

```python
class State:
    def __init__(self, data={}):  # Dangerous mutable default argument
        self.data = data
```

## Remediation

- Prefer immutable structures by default: use `@dataclass(frozen=True)` or `NamedTuple` for data transfer objects.
- Return copies, not references, when dealing with mutable internal state.
- Use static analysis (e.g., mypy, ruff) to flag mutable default arguments and discourage unnecessary mutation.
- Explicitly type variables as `Sequence` or `Mapping` (read-only interfaces) instead of `List` or `Dict` when mutation is not intended.
