# SLOP.CORRECTNESS.006 — Schema/spec drift across layers

**Family:** CORRECTNESS

## Summary

When server behavior, client expectations, and documentation are generated in separate passes, they
diverge: optional vs required fields, naming mismatches, and inconsistent defaults. In Python
ecosystems this often appears as mismatched pydantic models, ad-hoc dict payloads, and docstrings
that claim behavior the code doesn’t enforce.  This slop is dangerous because each piece seems
reasonable in isolation, and failures only appear at integration boundaries.


## Examples (Python)

```python
# Server expects 'email' required, client sends 'user_email'
def create_user(payload: dict):
    email = payload["email"]  # KeyError when client sends user_email
```

```python
# Docs claim 'expires_in_seconds' optional, code treats as required
def create_token(payload: dict):
    ttl = int(payload["expires_in_seconds"])
```

```python
# Two models disagree on field names/types
USER_SCHEMA_A = {"age": int}
USER_SCHEMA_B = {"age": str}  # drift
```

## Remediation

- Establish one source of truth for schemas (pydantic models or OpenAPI) and generate/validate everything against it.
- Add contract tests that validate real request/response payloads.
- Version your schema changes and provide migration paths for consumers.
- Make docs executable where possible (doctests or integration tests for examples).
