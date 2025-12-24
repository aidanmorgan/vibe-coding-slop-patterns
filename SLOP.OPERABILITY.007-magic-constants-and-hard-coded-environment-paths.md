# SLOP.OPERABILITY.007 — Magic constants and hard-coded environment paths

**Family:** OPERABILITY

## Summary

Hardcoded URLs, ports, filesystem paths, and environment-specific values create “works on my
machine” failures. AI code chooses plausible defaults (localhost, /Users/...), which breaks in
containers, CI, and production.  This slop is easy to introduce and annoying to root-cause because
it looks harmless in code review.


## Examples (Python)

```python
DATA_DIR = "/Users/alice/data"  # developer-specific path
```

```python
BASE_URL = "http://localhost:8080"  # not valid in prod
```

```python
AWS_REGION = "us-east-1"  # hardcoded region assumption
```

## Remediation

- Centralize configuration and validate at startup; do not embed environment specifics in code.
- Use environment variables or config files with schema validation.
- Provide sensible defaults only when they are safe and environment-agnostic.
- Add deployment tests that run in containers and fail if localhost/user paths are referenced.
