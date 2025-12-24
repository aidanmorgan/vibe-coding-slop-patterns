# SLOP.ARCHITECTURE.002 — Over-engineering / unnecessary abstraction layers

**Family:** ARCHITECTURE

## Summary

Assistants often generate “enterprise-looking” structures—factories, service locators, multi-layer
patterns—for problems that don’t need them. The code becomes harder to understand and change, with
no real payoff.  In Python this commonly looks like excessive class hierarchies or indirection
modules that exist only to “look clean.”


## Examples (Python)

```python
class UserServiceFactory:
    @staticmethod
    def create():
        return UserService(UserRepository())
```

```python
class Strategy:
    def run(self, x): raise NotImplementedError()
class ConcreteStrategy(Strategy):
    def run(self, x): return x  # pointless
```

```python
def get_service():
    # indirection without benefit
    return Service(ServiceDeps())
```

## Remediation

- Adopt a “two call sites” rule before introducing abstractions.
- Prefer simple functions and modules until complexity demands otherwise.
- When adding a layer, document the extension point and add a second use case that proves it.
- Refactor for clarity, not for architectural aesthetics.
