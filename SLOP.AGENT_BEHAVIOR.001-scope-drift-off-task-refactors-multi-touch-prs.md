# SLOP.AGENT_BEHAVIOR.001 — Scope drift (off-task refactors / multi-touch PRs)

**Family:** AGENT_BEHAVIOR

## Summary

Agent tools can’t resist “improving” everything. You ask for a bug fix; the agent refactors
unrelated modules, reformats files, and touches many concerns at once. That increases risk, makes
review harder, and hides the actual change.  Scope drift is especially dangerous because it’s framed
as “cleanup,” so it bypasses skepticism.


## Examples (Python)

```python
# Example: bug fix includes unrelated refactor
def fix_bug(x):
    # ... actual fix ...
    return x

# plus unrelated renames and formatting across the module
def helper_function():
    return 42
```

```python
# Example: agent rewrites multiple modules for a one-line behavior change
# (represented here as a single file containing unrelated changes)
def endpoint():
    # changed logging, caching, error handling, and business logic at once
    ...
```

```python
# Example: reorganizing imports and rearranging functions unrelated to the defect
import os
import sys
# ... many reorderings ...
```

## Remediation

- Enforce scope fences: define which files/modules can be edited for a task.
- Require PRs to be single-concern: formatting/refactor separate from behavior changes.
- Add review rules for churn: reject large diffs without clear necessity.
- Use incremental commits and require each commit to be justified and test-backed.
