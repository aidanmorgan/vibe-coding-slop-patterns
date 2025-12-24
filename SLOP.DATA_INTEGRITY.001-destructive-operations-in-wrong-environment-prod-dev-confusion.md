# SLOP.DATA_INTEGRITY.001 — Destructive operations in wrong environment (prod/dev confusion)

**Family:** DATA_INTEGRITY

## Summary

Agentic workflows and quick scripts can run destructive operations against the wrong environment.
The slop is not merely the `DROP` or `TRUNCATE`; it’s the absence of hard safety rails: no
environment separation, no approvals, and credentials accessible to automation.  This pattern can
turn “fix a migration” into irreversible data loss.


## Examples (Python)

```python
def reset_tables(conn):
    # destructive command with no environment guard
    conn.execute("TRUNCATE users, invoices CASCADE")
```

```python
def cleanup(conn):
    # "temporary" cleanup that deletes everything
    conn.execute("DELETE FROM events")
```

```python
def migrate(conn):
    # runs migration against whatever DATABASE_URL points to
    run_migrations(conn)
```

## Remediation

- Hard-separate production and non-production credentials; keep prod creds out of dev and agent environments.
- Require human approval for destructive actions; implement “two-person rule” for prod schema changes.
- Add explicit environment guards (e.g., refuse to run if ENV=prod unless an approval token is present).
- Maintain backups and rehearse restores; verify rollback paths before running destructive migrations.
