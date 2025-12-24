# SLOP.ARCHITECTURE.005 — Data model drift (duplicate fields / multiple truths)

**Family:** ARCHITECTURE

## Summary

This slop “solves” a data bug by adding a new field instead of fixing the existing concept. You end
up with `name`, `full_name`, `display_name`, `name2`, and nobody knows which is canonical. Over
time, different parts of the code read/write different fields and data quality collapses.  AI
assistants do this because adding a field is a quick fix that avoids refactoring existing usage.


## Examples (Python)

```python
# multiple representations of same concept
user = {"name": None, "full_name": "Alice Smith", "display_name": "Alice"}
```

```python
# migration-style drift in code
def migrate_user(u: dict):
    if "name" not in u:
        u["name"] = u.get("full_name")
```

```python
# shadow field "fix"
def set_name(u: dict, name: str):
    u["name2"] = name  # avoids touching existing 'name' semantics
```

## Remediation

- Establish schema ownership and require review for any new fields.
- Enforce “one concept → one field” and remove deprecated fields via migrations.
- Add constraints and validations (NOT NULL, uniqueness) to prevent partial/ambiguous states.
- Provide explicit migration paths and update all readers/writers together.
