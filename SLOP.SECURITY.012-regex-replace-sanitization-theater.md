# SLOP.SECURITY.012 — Regex/replace “sanitization” theater

**Family:** SECURITY

## Summary

This is the illusion that removing a few characters or applying a simplistic regex makes input safe.
It does not. Contextual encoding and parameterization are what prevent injections; ad-hoc
sanitization is easy to bypass and often breaks valid data.  AI assistants propose these quick fixes
because they’re short and feel like “security,” especially to non-experts.


## Examples (Python)

```python
def sanitize_sql(value: str) -> str:
    return value.replace("'", "")  # bypassable and incorrect
```

```python
import re
def sanitize_html(value: str) -> str:
    return re.sub(r"<.*?>", "", value)  # broken sanitizer
```

```python
def sanitize_path(name: str) -> str:
    return name.replace("..", "")  # not sufficient
```

## Remediation

- Do not “sanitize” for SQL/HTML/paths with regex hacks. Use proper defenses: parameterized queries, contextual encoding, safe path handling.
- Validate by allowlist only for narrow domains (e.g., IDs that must match `[a-z0-9_-]+`).
- Use vetted libraries for parsing and sanitization where needed.
- Add adversarial tests; assume bypass attempts will occur.
