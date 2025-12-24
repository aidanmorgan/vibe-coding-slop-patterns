# SLOP.TESTING_QUALITY.005 — Snapshot-style test spam (asserting blobs, not behavior)

**Family:** TESTING_QUALITY

## Summary

In Python, the snapshot equivalent is asserting huge serialized outputs (full HTML, JSON dumps,
repr()) rather than checking specific behaviors or invariants. These tests are brittle: any harmless
formatting change causes churn, and meaningful regressions can slip through if the snapshot is
updated reflexively.  AI-generated tests often choose snapshots because they are fast to generate
and look comprehensive.


## Examples (Python)

```python
def test_response_snapshot(client):
    r = client.get("/profile/1")
    assert r.text == open("snapshots/profile_1.html").read()
```

```python
import json
def test_json_snapshot():
    data = build_payload()
    assert json.dumps(data, sort_keys=True) == open("snapshots/payload.json").read()
```

```python
def test_repr_snapshot(obj):
    assert repr(obj) == open("snapshots/obj.txt").read()
```

## Remediation

- Prefer semantic assertions (key fields present, constraints satisfied) over full-output equality.
- Restrict snapshots to stable surfaces and require careful review of snapshot updates.
- Combine snapshots with invariant checks so changes are meaningful (e.g., schema validation, required fields).
- Add tests that target behavior under change, not formatting output.
