# SLOP.PERFORMANCE_COST.001 — Missing timeouts on network calls

**Family:** PERFORMANCE_COST

## Summary

Without timeouts, network calls can hang indefinitely. Under load, hung requests consume worker
threads, saturate connection pools, and trigger cascading failures. AI code often omits timeouts
because requests succeed in quick demos and examples.  This slop becomes a production outage
generator the first time a dependency slows down.


## Examples (Python)

```python
import requests
def call_service(url: str):
    return requests.get(url).json()  # no timeout
```

```python
import httpx
def call_service(url: str):
    with httpx.Client() as c:
        return c.get(url).json()  # default may be too lenient/unset
```

```python
import urllib.request
def call_service(url: str) -> bytes:
    return urllib.request.urlopen(url).read()  # no timeout
```

## Remediation

- Set explicit timeouts for all outbound calls; treat “no timeout” as a defect.
- Centralize HTTP clients behind a wrapper that enforces deadlines and retries correctly.
- Add tests that simulate slow dependencies (sleep servers) and verify timeout behavior.
- Monitor timeout rates and latency; tune based on SLOs rather than defaults.
