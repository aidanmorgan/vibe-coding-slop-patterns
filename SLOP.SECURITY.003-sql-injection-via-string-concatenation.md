# SLOP.SECURITY.003 — SQL injection via string concatenation

**Family:** SECURITY

## Summary

AI-generated code often builds SQL using f-strings, concatenation, or `.format()`. It’s easy,
readable, and extremely dangerous. Injection vulnerabilities can lead to data exfiltration,
corruption, or privilege escalation.  This slop persists because it works in basic tests and looks
“normal” to inexperienced reviewers.


## Examples (Python)

```python
def get_user_by_email(conn, email: str):
    q = f"SELECT * FROM users WHERE email = '{email}'"
    return conn.execute(q).fetchone()
```

```python
def delete_user(conn, user_id: str):
    q = "DELETE FROM users WHERE id = " + user_id
    conn.execute(q)
```

```python
def search(conn, term: str):
    q = "SELECT * FROM posts WHERE title LIKE '%" + term + "%'"
    return conn.execute(q).fetchall()
```

## Remediation

- Use parameterized queries everywhere; treat dynamic SQL as an exception requiring review.
- Prefer query builders or safe ORM patterns where possible.
- Add security tests using representative payloads (quotes, comments, UNION attempts) and ensure they fail safely.
- Lock down DB permissions so a single injection can’t become full compromise.
