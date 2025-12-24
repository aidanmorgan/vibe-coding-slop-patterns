# SLOP.TESTING_QUALITY.004 — Sleeps as assertions (flaky timing tests)

**Family:** TESTING_QUALITY

## Summary

Using `sleep()` in tests is a reliability tax: it makes tests slow and flaky. AI code often uses
sleeps to wait for background jobs or async operations because it’s the simplest way to “make the
test pass.”  In production-like environments, timing varies and sleeps become nondeterministic.


## Examples (Python)

```python
import time
def test_job_completes():
    start_job()
    time.sleep(2)
    assert job_status() == "done"
```

```python
import time
def test_async_side_effect():
    trigger()
    time.sleep(1)
    assert get_result() is not None
```

```python
import time
def test_eventually():
    do()
    time.sleep(0.5)
    assert happened()
```

## Remediation

- Replace sleeps with deterministic synchronization: event hooks, polling with bounded timeout, or fake clocks.
- Use dependency injection to control time and scheduling in tests.
- Add diagnostics on timeout to make failures actionable.
- Keep tests fast: slow sleeps hide real flakiness rather than solving it.
