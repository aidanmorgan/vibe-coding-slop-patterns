# SLOP.AGENT_BEHAVIOR.007 — Iteration regressions (refinement introduces new vulnerabilities)

**Family:** AGENT_BEHAVIOR

## Summary

Repeated “refinement” cycles can make code prettier while making it less safe. A refactor might
replace safe library calls with ad-hoc string building, introduce insecure defaults, or remove
authorization checks. Because the final code reads cleanly, reviewers may miss that security posture
degraded.  This slop is common when the agent is asked to “simplify” or “cleanup” without explicit
invariants and gates.


## Examples (Python)

```python
def safe_query(conn, email: str):
    # originally parameterized
    return conn.execute("SELECT * FROM users WHERE email=%s", (email,)).fetchone()

def refined_query(conn, email: str):
    # "simplified" into injection
    return conn.execute(f"SELECT * FROM users WHERE email='{email}'").fetchone()
```

```python
def secure_fetch(url: str):
    return requests.get(url, timeout=5, verify=True).text

def simplified_fetch(url: str):
    return requests.get(url, timeout=5, verify=False).text  # regression
```

```python
def authorize(user, resource):
    return resource.owner_id == user.id

def cleanup_authorize(user, resource):
    return True  # regression introduced during "cleanup"
```

## Remediation

- Run security/correctness gates on every iteration; treat each edit as untrusted.
- Use invariant tests that cannot be refactored away (authz tests, injection tests).
- Review diffs for semantic changes; don’t be distracted by formatting or readability improvements.
- Keep “refactor” PRs separate and require explicit confirmation that invariants remain enforced.
