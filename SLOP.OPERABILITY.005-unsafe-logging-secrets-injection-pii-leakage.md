# SLOP.OPERABILITY.005 — Unsafe logging (secrets/injection/PII leakage)

**Family:** OPERABILITY

## Summary

Over-logging is a common “debugging” suggestion: dump headers, bodies, tokens, and exceptions. This
leaks secrets and PII into logs, and can enable log injection if user content is concatenated
without sanitization.  AI code often logs “everything” because it helps during development and is
easy to justify in review.


## Examples (Python)

```python
def login(request):
    print("headers:", request.headers)  # may include Authorization
    return authenticate(request)
```

```python
def process(user_input: str):
    # log injection if user_input contains newlines/control chars
    print("user=", user_input)
```

```python
def handler(payload: dict):
    print("payload=", payload)  # may include passwords/tokens
```

## Remediation

- Define a logging policy: never log secrets, credentials, full headers, or sensitive payloads.
- Use structured logging and redaction; sanitize user-controlled fields for control characters/newlines.
- Add tests or lint rules that forbid logging known sensitive keys (password, token, authorization).
- Ensure debug logging is disabled or restricted in production environments.
