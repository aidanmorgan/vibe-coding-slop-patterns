# SLOP.SECURITY.005 — Insecure deserialization (pickle/eval) on untrusted input

**Family:** SECURITY

## Summary

Python makes it dangerously easy to deserialize arbitrary objects. Assistants may use `pickle` for
convenience or even `eval` for quick parsing. If the input is attacker-controlled, this can lead to
remote code execution or severe compromise.  This is a “works instantly” snippet that should almost
never exist in production request paths.


## Examples (Python)

```python
import pickle
def load_payload(raw: bytes):
    return pickle.loads(raw)  # unsafe if raw is untrusted
```

```python
def parse_expr(expr: str):
    return eval(expr)  # executes code
```

```python
import dill
def load(raw: bytes):
    return dill.loads(raw)  # also unsafe on untrusted input
```

## Remediation

- Ban `pickle`/`dill`/`eval` on untrusted input; use JSON + schema validation instead.
- If you must deserialize, enforce signatures, strict allowlists, and isolate execution (but prefer not doing it).
- Add code review rules: any use of pickle/eval must be security-reviewed.
- Treat these findings as high-severity vulnerabilities, not “style issues.”
