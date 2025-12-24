# SLOP.DOCUMENTATION.003 — Noise comments (explaining the obvious)

**Family:** DOCUMENTATION

## Summary

Large Language Models often treat code generation as a tutorial, adding verbose comments that explain basic language mechanics ("# increment i", "# import os") rather than business intent. These comments create visual noise, require maintenance, and quickly drift from the code (e.g., the comment says "return list" but the code was changed to return a generator). This "comment slop" trains developers to ignore comments entirely, causing them to miss important warnings or invariants.

## Examples (Python)

```python
# Import the os module
import os

# Set the counter to zero
counter = 0

# Loop through the items in the list
for item in items:
    # Check if the item is valid
    if item.is_valid():
        # Add to counter
        counter += 1
```

```python
def get_user(id):
    # This function gets the user by ID
    return db.get(id)
```

```python
# Returns the result
return result
```

## Remediation

- Remove comments that simply restate the code in English.
- Focus comments on *why* a decision was made, non-obvious constraints, or business rules.
- Use linting tools to flag trivial comments (e.g., high ratio of comment lines to code lines in simple blocks).
- During review, treat noise comments as technical debt that must be deleted before merging.
