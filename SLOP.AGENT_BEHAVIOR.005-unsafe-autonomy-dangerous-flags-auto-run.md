# SLOP.AGENT_BEHAVIOR.005 — Unsafe autonomy / dangerous flags / auto-run

**Family:** AGENT_BEHAVIOR

## Summary

This slop appears when workflows encourage agents to execute commands automatically or bypass safety
checks. It increases the blast radius of mistakes and creates an incident pipeline—especially when
secrets are available in the environment.  In Python projects, this can show up as scripts that run
arbitrary shell commands sourced from suggestions or configs that skip confirmations.


## Examples (Python)

```python
import os
def run_agent_suggested(cmd: str):
    # executes arbitrary suggested commands
    os.system(cmd)
```

```python
import subprocess
def dangerous_cleanup():
    # no confirmation, no env guard
    subprocess.check_call(["bash", "-lc", "rm -rf /tmp/app/*"])
```

```python
AUTO_APPROVE_COMMANDS = True  # encourages auto-run behavior
```

## Remediation

- Require human approval for command execution and especially for destructive operations.
- Sandbox agent execution and restrict outbound network access; remove secrets from agent environments.
- Maintain a tools allowlist: what commands are permitted, in what contexts.
- Treat changes that enable auto-run or bypass prompts as security-sensitive and review-required.
