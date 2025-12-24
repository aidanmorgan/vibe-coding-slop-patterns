# SLOP.COMPLETENESS.002 — Stub bodies (pass / return None / NotImplemented)

**Family:** COMPLETENESS

## Summary

This slop appears when an assistant scaffolds a function signature and then punts on the
implementation. It often compiles and looks architected, but the runtime behavior is missing. The
failure shows up later (under real traffic) in the least convenient code path.  Agents also use
stubs as a “progress marker” while searching for fixes: they make the system appear complete enough
to move on, leaving a latent runtime crash or silent None-propagation.


## Examples (Python)

```python
def calculate_tax(invoice):
    pass  # returns None; caller may treat None as 0 or crash later
```

```python
def normalize_email(email: str) -> str:
    return None  # placeholder; breaks callers expecting str
```

```python
def verify_signature(message: bytes, signature: bytes) -> bool:
    raise NotImplementedError("TODO: implement signature verification")
```

## Remediation

- In production code, stubs must not be allowed to ship. Replace with explicit errors that tests will hit (e.g., raise a domain exception).
- Require a test that would have failed with the stub and now passes (proves the behavior is implemented).
- If you truly need a placeholder, gate it behind a feature flag that defaults off and returns a clear “not available” error.
- Establish a “no stubs in runtime paths” rule: `pass`, `return None` placeholders, and `NotImplementedError` are only allowed in tests/examples.
