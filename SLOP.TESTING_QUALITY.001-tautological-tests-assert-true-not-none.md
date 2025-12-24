# SLOP.TESTING_QUALITY.001 — Tautological tests (assert True / not None)

**Family:** TESTING_QUALITY

## Summary

These tests exist to make CI green, not to validate behavior. AI outputs often generate tautologies
because they satisfy the instruction “write tests” without requiring deep understanding. The result
is false confidence: the suite passes while the system is broken.  In Python, the smell is
especially common in pytest because assertions are so lightweight.


## Examples (Python)

```python
def test_works():
    assert True
```

```python
def test_handler_returns_value():
    result = handler()
    assert result is not None
```

```python
def test_always_passes():
    assert 1 == 1
```

## Remediation

- Require behavior assertions: outputs, side effects, invariants—not mere existence.
- Use mutation testing to detect tests that don’t constrain behavior.
- Add reviewer guidance: “Would this test fail if the implementation were wrong?” If not, reject it.
- Create templates for meaningful tests (table-driven, property-based) to make good tests easier than bad ones.
