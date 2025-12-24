# SLOP.COMPLETENESS.004 — Placeholder tokens never replaced (REPLACE_ME / CHANGEME)

**Family:** COMPLETENESS

## Summary

Templates and vibe-builders often include “fill this in later” tokens. Slop happens when those
tokens survive into runtime config, environment files, or code. The result is a system that’s
neither correctly configured nor obviously broken—leading to brittle deployments and risky
“temporary” hardcoding of secrets to get unstuck.  Placeholder tokens also signal incomplete
integration work that should be made explicit and validated at startup.


## Examples (Python)

```python
API_KEY = "REPLACE_ME"  # accidentally committed
```

```python
DATABASE_URL = "postgres://CHANGEME"  # placeholder used at runtime
```

```python
OAUTH_CALLBACK_URL = "[YOUR_CALLBACK_URL]"  # template token
```

## Remediation

- Validate configuration at startup and fail fast with clear errors when required values are missing or still placeholders.
- Centralize config loading (one module), and keep placeholders out of runtime defaults.
- Add a pre-release checklist item: scan configuration files and code for known placeholder tokens.
- Prefer environment-injected secrets over in-repo defaults; do not “temporarily” hardcode to fix placeholders.
