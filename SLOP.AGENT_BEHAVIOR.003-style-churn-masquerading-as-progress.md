# SLOP.AGENT_BEHAVIOR.003 — Style churn masquerading as progress

**Family:** AGENT_BEHAVIOR

## Summary

Style churn is when the agent reformats code, reorders imports, or touches whitespace while
addressing an unrelated bug. It creates noisy diffs that hide the real change, wastes reviewer time,
and increases merge conflicts.  Assistants do this because they optimize for global cleanliness
rather than minimal risk changes.


## Examples (Python)

```python
# import churn unrelated to fix
import sys
import os  # reordered
# ... no functional change ...
```

```python
def handler(a,b):
    return a+b  # reformatted to comply with imagined style
```

```python
# whitespace-only changes across the file
def f(x):
    return x
```

## Remediation

- Separate formatting into dedicated PRs; keep functional PRs minimal.
- Apply autoformatters in a controlled, repo-wide pass rather than ad hoc changes.
- Review policies should reject noisy diffs unless required by the change.
- Encourage agents to operate with “minimal diff” constraints when fixing issues.
