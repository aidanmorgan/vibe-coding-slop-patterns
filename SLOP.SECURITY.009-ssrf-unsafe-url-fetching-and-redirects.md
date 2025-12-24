# SLOP.SECURITY.009 — SSRF / unsafe URL fetching and redirects

**Family:** SECURITY

## Summary

Features that “fetch a URL” or “follow a redirect” are common, and assistants often implement them
without constraints. This can enable SSRF (server-side request forgery) against internal services,
metadata endpoints, or private networks. Similarly, unvalidated redirects enable phishing and auth-
flow abuse.  The slop is typically “take the user-provided URL and request it,” which works in demos
and fails under adversarial input.


## Examples (Python)

```python
import requests
def fetch_url(url: str) -> str:
    return requests.get(url, timeout=5).text  # no allowlist
```

```python
def proxy(request):
    url = request.args["url"]
    return requests.get(url).content  # SSRF to internal network possible
```

```python
def redirect(next_url: str) -> str:
    # open redirect behavior if next_url is unvalidated
    return next_url
```

## Remediation

- Allowlist destinations: permitted schemes, hosts, and paths; reject everything else.
- Block private IP ranges, link-local, and metadata endpoints.
- Don’t follow redirects blindly; validate redirect targets against allowlists.
- Add tests that attempt to fetch internal IPs and ensure they are blocked.
