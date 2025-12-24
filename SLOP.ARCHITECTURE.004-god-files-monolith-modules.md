# SLOP.ARCHITECTURE.004 — God files / monolith modules

**Family:** ARCHITECTURE

## Summary

Agentic tools can dump entire features into a single giant module to maintain coherence. It works
initially, but destroys maintainability: review becomes impossible, boundaries blur, and changes
create accidental coupling.  In Python this often manifests as `app.py` or `main.py` growing into
thousands of lines of mixed concerns.


## Examples (Python)

```python
# app.py contains routing, DB, auth, business logic, and HTML rendering
def handle_request():
    ...
```

```python
# one module implements everything, importing everything
from auth import *
from db import *
from billing import *
from ui import *
```

```python
# giant class with many responsibilities
class Application:
    def handle_login(self): ...
    def handle_payments(self): ...
    def handle_reports(self): ...
```

## Remediation

- Enforce module boundaries and maximum file size/complexity budgets.
- Split by responsibility: API layer, domain logic, persistence, integrations.
- Add architecture tests (import rules) to prevent boundary violations.
- Keep PRs small and focused; don’t accept “everything in one file” as a shortcut.
