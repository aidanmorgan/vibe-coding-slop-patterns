# SLOP.OPERABILITY.008 — Traceback destruction (missing exception chaining)

**Family:** OPERABILITY

## Summary

When handling errors, AI assistants often write `try/except` blocks that raise a new exception without linking the original cause (`from e`). This destroys the stack trace, making it impossible to see *where* the original error occurred. Debugging becomes a guessing game because the logs only show the generic "wrapper" error (e.g., `ProcessingError`) rather than the underlying `KeyError` or `ConnectionTimeout` that actually caused the failure.

## Examples (Python)

```python
def fetch_data(url):
    try:
        return requests.get(url)
    except Exception:
        # The original stack trace is lost here
        raise RuntimeError("Failed to fetch data")
```

```python
def parse_config(path):
    try:
        data = json.load(open(path))
    except json.JSONDecodeError:
        # Re-raising without context destroys the line number info of the bad JSON
        raise ValueError("Config is invalid")
```

```python
try:
    complex_logic()
except Exception as e:
    # "wrapping" that hides the cause
    raise MyCustomError(f"Logic failed: {str(e)}")
```

## Remediation

- Always use `raise NewException(...) from e` when wrapping exceptions in Python.
- If you intend to suppress the original context (rare), do so explicitly with `from None`.
- Enforce lint rules (e.g., `RSE102`, `TRY200`) that detect exception raising without chaining.
- Ensure error logs capture the full chained stack trace, not just the final exception message.
