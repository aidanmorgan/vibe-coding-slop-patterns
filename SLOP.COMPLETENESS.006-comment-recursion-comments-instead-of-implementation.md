# SLOP.COMPLETENESS.006 — Comment recursion (comments instead of implementation)

**Family:** COMPLETENESS

## Summary

Assistants can get stuck describing the implementation rather than writing it—producing instruction-
like comments that repeat the prompt. This looks like progress in diffs but delivers zero behavior.
When merged, it becomes institutional confusion (“we thought this was implemented”).  Comment
recursion is especially common when prompts ask for “clean, well-documented code” and reviewers
conflate documentation with completion.


## Examples (Python)

```python
def hash_password(password: str) -> str:
    # Implement secure password hashing (argon2/bcrypt), including salt management.
    # Implement secure password hashing (argon2/bcrypt), including salt management.
    # TODO: implement secure password hashing
    return password  # placeholder
```

```python
def validate_request(payload: dict) -> None:
    # Validate required fields and enforce permissions.
    # Validate required fields and enforce permissions.
    pass
```

```python
def send_email(to: str, subject: str, body: str) -> None:
    # This should send an email via your provider and handle retries.
    return
```

## Remediation

- Comments should explain “why” and non-obvious constraints; they should not be a task list.
- If comments describe missing behavior, code should fail explicitly rather than pretending to work.
- Replace instruction comments with tracked tasks and remove the duplicate prose from code.
- Require at least one behavioral test for every function introduced; comments alone are not delivery.
