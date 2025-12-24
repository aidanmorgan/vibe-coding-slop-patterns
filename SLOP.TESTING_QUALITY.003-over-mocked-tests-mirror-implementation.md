# SLOP.TESTING_QUALITY.003 — Over-mocked tests (mirror implementation)

**Family:** TESTING_QUALITY

## Summary

Over-mocking creates brittle tests that assert internal calls rather than observable behavior. AI
code often over-mocks because it’s easy to satisfy “unit test” requirements by verifying method
calls. But these tests don’t catch integration failures and they resist refactoring.  In Python,
this often appears as heavy `unittest.mock.patch` usage for everything in sight.


## Examples (Python)

```python
from unittest.mock import patch
def test_process_calls_helper():
    with patch("app.helper") as helper:
        process("x")
        helper.assert_called_once()  # asserts internal call, not outcome
```

```python
from unittest.mock import Mock
def test_service():
    repo = Mock()
    svc = Service(repo)
    svc.run()
    repo.save.assert_called()  # no behavior verified
```

```python
from unittest.mock import patch
def test_order():
    with patch("payments.charge") as charge:
        checkout({"amount": 100})
        assert charge.called  # doesn't prove correctness
```

## Remediation

- Prefer outcome-based assertions: returned values, persisted state, emitted events.
- Use fewer mocks; replace with fakes for stable dependencies or run integration tests for critical paths.
- When mocking, avoid verifying call order unless it’s a contract; focus on inputs/outputs.
- Add at least one end-to-end or integration test per critical workflow (auth, payment, access control).
