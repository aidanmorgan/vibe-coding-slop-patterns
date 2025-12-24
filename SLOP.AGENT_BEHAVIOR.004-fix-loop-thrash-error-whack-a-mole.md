# SLOP.AGENT_BEHAVIOR.004 — Fix loop thrash (error whack-a-mole)

**Family:** AGENT_BEHAVIOR

## Summary

This happens when iterative agent “fixes” don’t converge: each change addresses one error while
introducing others. The system becomes fragile, and the final state is a pile of patches rather than
a coherent solution.  Because the agent is optimizing for making something pass quickly, it may
broaden types, swallow errors, or add dependencies to silence problems.


## Examples (Python)

```python
def parse(x: str) -> int:
    try:
        return int(x)
    except Exception:
        return 0  # introduced to "fix" a crash, changes semantics
```

```python
def do_work(data):
    # broadened to accept anything, hides type errors
    return str(data)
```

```python
def call():
    # retries added without bounds to "fix" flaky tests
    while True:
        try:
            return risky()
        except Exception:
            pass
```

## Remediation

- Require measurable progress per iteration (failing tests must decrease).
- Stop and revert if changes don’t converge; diagnose root cause instead of patching symptoms.
- Prefer small, verifiable diffs; avoid broad semantic changes to silence errors.
- Add guardrails: lint/typecheck/security gates on every iteration.
