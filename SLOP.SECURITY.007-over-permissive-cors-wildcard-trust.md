# SLOP.SECURITY.007 — Over-permissive CORS / wildcard trust

**Family:** SECURITY

## Summary

In Python web apps, permissive cross-origin settings are often introduced to “fix the frontend.”
While CORS is primarily a browser policy, misconfiguration can expose authenticated endpoints to
cross-site attacks and create confusing security posture.  Assistants frequently recommend allow-all
configurations because it makes integration work instantly.


## Examples (Python)

```python
# Flask-CORS example
from flask import Flask
from flask_cors import CORS
app = Flask(__name__)
CORS(app, resources={r"/*": {"origins": "*"}})
```

```python
# Starlette/FastAPI CORS middleware example
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
app = FastAPI()
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

```python
# DIY allow-all header
def add_cors_headers(resp):
    resp.headers["Access-Control-Allow-Origin"] = "*"
    return resp
```

## Remediation

- Use explicit allowlists for origins; never combine wildcard origins with credentials.
- Treat CORS changes as security-sensitive and require review.
- Keep dev CORS settings separate from prod configuration.
- Ensure server-side authz remains correct regardless of CORS (CORS is not a security boundary).
