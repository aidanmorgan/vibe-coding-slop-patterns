# SLOP.TESTING_QUALITY.002 — Tests that don’t run (misplaced/undiscovered)

**Family:** TESTING_QUALITY

## Summary

You can have “tests” that never execute due to naming, location, or CI configuration. AI-generated
repos can accidentally create this because the assistant doesn’t know your discovery conventions,
and humans assume “there are tests” means “they ran.”  This slop produces green builds with zero
signal.


## Examples (Python)

```python
# file name doesn't match pytest discovery rules (e.g., checks.py)
def test_login_flow():
    assert login("u", "p") is True
```

```python
# tests defined inside a module never collected by runner
class Tests:
    def test_something(self):
        assert True
```

```python
# test functions exist but CI never invokes pytest
def test_database_connection():
    assert True
```

## Remediation

- Require CI to publish test execution reports; fail builds if zero tests are collected.
- Standardize discovery conventions and enforce them (naming and folder structure).
- Add a CI sanity check: minimum number of tests executed (or minimum coverage on critical modules).
- Ensure local dev commands match CI exactly (same test invocation).
