# SLOP.SECURITY.010 — Path traversal / unsafe file reads and writes

**Family:** SECURITY

## Summary

Python code that reads/writes files using user-controlled names can allow attackers to access or
overwrite arbitrary files. Assistants often compose paths by concatenation because it’s simple and
looks harmless.  Path traversal bugs can lead to credential leakage, code execution (writing to
importable paths), and data destruction.


## Examples (Python)

```python
def save_upload(filename: str, content: bytes) -> None:
    with open("/tmp/" + filename, "wb") as f:  # traversal possible
        f.write(content)
```

```python
import os
def read_report(name: str) -> bytes:
    path = "/var/reports/" + name
    return open(path, "rb").read()
```

```python
from pathlib import Path
def write_file(base: str, name: str, data: bytes):
    # naive join without validation
    Path(base, name).write_bytes(data)
```

## Remediation

- Normalize and validate paths; enforce a base directory boundary and reject `..`, absolute paths, and separators.
- Use safe temp file APIs and randomized filenames for uploads.
- Separate user-visible names from filesystem names (store mapping in DB).
- Add security tests attempting traversal patterns and ensure they fail.
