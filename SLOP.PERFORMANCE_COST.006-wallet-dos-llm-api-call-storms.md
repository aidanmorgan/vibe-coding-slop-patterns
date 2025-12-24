# SLOP.PERFORMANCE_COST.006 — Wallet DoS (LLM/API call storms)

**Family:** PERFORMANCE_COST

## Summary

A “Wallet DoS” happens when code triggers expensive API calls (LLMs, external enrichment, paid
services) too frequently—on every request, in tight loops, or repeatedly for the same inputs. It
looks fine in a demo and becomes a cost incident in production.  Assistants often place calls in the
most obvious location (“just call the API here”), without caching, quotas, or batching.


## Examples (Python)

```python
def summarize_all(items):
    # calls an expensive model per item, no batching or caching
    return [llm.summarize(it["text"]) for it in items]
```

```python
def endpoint(request):
    # expensive call on every request, even if input unchanged
    return {"result": llm.complete(request.json["prompt"])}
```

```python
def render_page(user_id: str):
    # repeated call for same user each render
    profile = llm.complete(f"Write a bio for user {user_id}")
    return profile
```

## Remediation

- Add caching keyed by stable inputs; cache aggressively for repeated prompts or derived data.
- Introduce quotas and rate limits per user/tenant; enforce them server-side.
- Batch model calls where possible; move expensive work to background jobs.
- Monitor spend metrics and alert on anomalies; treat “cost” as an SLO.
