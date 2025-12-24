# SLOP.OPERABILITY.003 — Resource leaks (files/sockets/handles)

**Family:** OPERABILITY

## Summary

Resource leaks occur when code opens files, sockets, or responses and fails to close them. They
often don’t show up in unit tests and only appear under sustained load, where leaked file
descriptors or sockets accumulate into outages.  AI code may omit cleanup because examples are short
and focus on the “core logic,” not lifecycle management.


## Examples (Python)

```python
def read_all(path: str) -> bytes:
    f = open(path, "rb")
    return f.read()  # f never closed
```

```python
import requests
def fetch_bytes(url: str) -> bytes:
    r = requests.get(url, stream=True)
    return r.raw.read()  # response not closed properly
```

```python
import socket
def ping(host: str, port: int):
    s = socket.socket()
    s.connect((host, port))
    s.send(b"hi")
    # missing s.close()
```

## Remediation

- Use context managers (`with`) for files and network resources.
- Ensure HTTP responses are closed; prefer session/client wrappers that manage lifecycle.
- Add load or soak tests that run long enough to expose leaks.
- Monitor FD/socket counts in production and alert on growth.
