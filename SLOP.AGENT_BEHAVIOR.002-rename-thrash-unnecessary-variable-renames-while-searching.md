# SLOP.AGENT_BEHAVIOR.002 — Rename thrash (unnecessary variable renames while searching)

**Family:** AGENT_BEHAVIOR

## Summary

Agents frequently rename variables, functions, or modules while “searching” for a fix. This is
progress theater: it increases diff noise, causes merge conflicts, and can introduce subtle bugs
when renames are inconsistent. It also erodes review focus by burying functional changes under
naming churn.  This slop is common when the agent is stuck: it tries renaming to match imagined
conventions or to align with a guessed API shape.


## Examples (Python)

```python
# rename-only change that adds noise and no behavior
def process(payload):
    return payload

# later renamed again without reason
def process_request(data):
    return data
```

```python
# oscillating rename leads to inconsistencies
TMP_DIR = "/tmp"
TEMP_DIR = TMP_DIR  # created during rename attempt
```

```python
def compute_total(amount_cents):
    return amount_cents
# agent renames to "amount" and changes units accidentally
def compute_total(amount):
    return amount
```

## Remediation

- Treat renames as intentional refactors with a plan: one purpose, one commit, language-aware refactoring tools.
- Block rename-only PRs unless explicitly requested.
- Require justification for signature changes and identifier churn.
- Use automated refactor tooling to ensure renames are consistent across references.
