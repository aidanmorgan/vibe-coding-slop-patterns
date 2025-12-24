# SLOP.SECURITY.001 — TLS verification disabled

**Family:** SECURITY

## Summary

Disabling TLS verification is the classic “make it work” hack that becomes a production
vulnerability. It enables trivial man-in-the-middle attacks and undermines the core trust guarantees
of HTTPS.  This shows up in AI-generated code because examples on the internet often use it for
debugging, and assistants optimize for immediate success over long-term safety.


## Examples (Python)

```python
import requests
def fetch(url: str) -> str:
    return requests.get(url, verify=False, timeout=5).text
```

```python
import ssl, urllib.request
def fetch(url: str) -> bytes:
    ctx = ssl.create_default_context()
    ctx.check_hostname = False
    ctx.verify_mode = ssl.CERT_NONE
    return urllib.request.urlopen(url, context=ctx).read()
```

```python
import httpx
def fetch(url: str) -> str:
    with httpx.Client(verify=False) as c:
        return c.get(url).text
```

## Remediation

- Never ship `verify=False` or `CERT_NONE` in production code. Remove it; don’t “toggle” it quietly.
- If you need dev overrides, make them explicit, local-only, and noisy (e.g., separate config file ignored by git).
- Use proper CA bundles or mTLS, and validate hostname verification.
- Add security review for any code touching TLS options.
