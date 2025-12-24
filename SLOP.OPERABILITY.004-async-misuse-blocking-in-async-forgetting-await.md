# SLOP.OPERABILITY.004 — Async misuse (blocking in async / forgetting await)

**Family:** OPERABILITY

## Summary

Async code is easy to get subtly wrong: calling blocking I/O in an async function, forgetting to
await, or mixing sync and async clients. The code may work in small tests and degrade badly under
concurrency.  Assistants frequently generate async code that “looks right” but violates event-loop
assumptions.


## Examples (Python)

```python
import asyncio, requests
async def handler(url: str):
    # blocking call inside async function
    return requests.get(url).text
```

```python
import asyncio
async def do_work():
    coro = asyncio.sleep(1)
    return coro  # forgot await, returns coroutine
```

```python
import aiohttp
async def fetch(url: str):
    session = aiohttp.ClientSession()
    resp = await session.get(url)
    return await resp.text()  # missing session.close() as well
```

## Remediation

- Use async-aware libraries in async functions (aiohttp/httpx async) and avoid blocking calls.
- Enforce linting/typechecking that catches un-awaited coroutines.
- Manage async resource lifecycles with `async with` and explicit close.
- Add concurrency tests that simulate parallel requests and verify performance/behavior.
