# SLOP.SECURITY.002 — Hard-coded secrets / keys / tokens

**Family:** SECURITY

## Summary

Hardcoding secrets is the fastest way to create a future incident. AI-assisted code frequently
embeds API keys, JWT secrets, database passwords, and service tokens “just to get it working.” The
risk isn’t theoretical: these get committed, shared, cached in build logs, and reused across
environments.  Even when discovered quickly, rotation and incident response become recurring
operational tax.


## Examples (Python)

```python
OPENAI_API_KEY = "sk_live_1234567890abcdef"  # committed secret
```

```python
JWT_SECRET = "supersecret"  # weak and in code
```

```python
SERVICE_ROLE_KEY = "eyJhbGciOi..."  # embedded token
```

## Remediation

- Move secrets to a secret manager or environment injection; never store them in repo or default config.
- Rotate any leaked secret immediately and assume compromise.
- Add automated secret scanning pre-commit and in CI, and block merges on detection.
- Make local developer setup ergonomic (env templates + tooling) so “just hardcode it” is never the easiest option.
