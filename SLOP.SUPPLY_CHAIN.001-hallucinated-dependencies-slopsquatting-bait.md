# SLOP.SUPPLY_CHAIN.001 — Hallucinated dependencies (slopsquatting bait)

**Family:** SUPPLY_CHAIN

## Summary

Assistants sometimes invent package names that sound plausible. Developers may attempt to install
them, or the name may later be registered by an attacker. Either way, the dependency graph becomes a
supply-chain hazard introduced by suggestion rather than intent.  Even when the package doesn’t
exist, the pattern is harmful: it pushes teams toward unvetted dependencies and erodes discipline
around provenance.


## Examples (Python)

```python
# import that looks plausible but doesn't exist
import fastyaml  # hypothetical/hallucinated dependency
```

```python
# requirements.txt suggestion that may be bogus or unvetted
# fastjson-utils==1.2.3
```

```python
# using importlib to load a "helper" package that isn't part of your standard deps
import importlib
mod = importlib.import_module("secure_helpers_plus")
```

## Remediation

- Require review/approval for any new dependency; treat dependency introduction as a security event.
- Enforce lockfiles and verify integrity; pin versions and audit provenance.
- Prefer well-known, maintained packages with clear ownership and security posture.
- Maintain an allowlist of approved packages for critical areas (crypto, auth, parsing).
