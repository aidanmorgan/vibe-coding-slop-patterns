# SLOP.PERFORMANCE_COST.002 — Retry storms (unbounded retries/no jitter)

**Family:** PERFORMANCE_COST

## Summary

Naive retries can amplify outages: when a downstream service fails, thousands of callers retry in
lockstep and overwhelm it further. Assistants often implement retries as simple loops because it
“feels robust.”  In production, retries must be bounded, jittered, and conditioned on idempotency
and error type.


## Examples (Python)

```python
import time
def call_with_retry(fn):
    while True:
        try:
            return fn()
        except Exception:
            time.sleep(1)  # unbounded retry
```

```python
def retry_three_times(fn):
    for _ in range(1000):  # accidentally huge
        try:
            return fn()
        except Exception:
            pass
```

```python
def retry_without_jitter(fn):
    for _ in range(10):
        try:
            return fn()
        except Exception:
            time.sleep(1)  # synchronized retries
```

## Remediation

- Bound retries (max attempts) and use exponential backoff with jitter.
- Retry only on retryable errors; distinguish client errors from transient failures.
- Require idempotency keys for retried side-effecting operations.
- Add circuit breakers and bulkheads to prevent thundering herds.
