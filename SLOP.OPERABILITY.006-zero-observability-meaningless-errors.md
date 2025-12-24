# SLOP.OPERABILITY.006 — Zero observability / meaningless errors

**Family:** OPERABILITY

## Summary

Systems without correlation IDs, structured logs, metrics, or meaningful error codes become
impossible to operate. When failures occur, the only option is guesswork or re-running prompts. AI-
generated apps often omit observability because it’s not required to “work” in a demo.  This slop is
operational debt that turns small bugs into multi-day incidents.


## Examples (Python)

```python
def handler():
    try:
        do_work()
    except Exception:
        return {"error": "Something went wrong"}
```

```python
def api_error(e: Exception):
    return {"error": str(e)}  # leaks internals and provides no stable code
```

```python
def do_task():
    # no logging, no metrics, silent success/failure
    run()
```

## Remediation

- Add baseline observability: request IDs, structured logs, and metrics for external calls.
- Define stable error codes and map them to user-safe messages; keep internal details in logs.
- Instrument dependency calls with duration, success/failure, and reason.
- Treat observability as part of definition-of-done for any production feature.
