# SLOP.SECURITY.004 — XSS sinks via unsafe HTML rendering

**Family:** SECURITY

## Summary

XSS in Python systems often arrives via template rendering and “render raw HTML” conveniences.
Assistants may suggest bypassing escaping for rich text or convenience, which becomes stored or
reflected XSS if any user-controlled data flows into HTML contexts.  The trap: the UI looks correct
and feature-complete, while the system quietly becomes scriptable by attackers.


## Examples (Python)

```python
from flask import render_template_string
def render_profile(name: str):
    # user-controlled name injected into HTML without escaping
    return render_template_string(f"<h1>{name}</h1>")
```

```python
from jinja2 import Template
def render_comment(comment_html: str):
    # treating untrusted input as trusted HTML
    return Template("{{ c|safe }}").render(c=comment_html)
```

```python
def render_raw(user_bio: str) -> str:
    return "<div>" + user_bio + "</div>"  # no escaping
```

## Remediation

- Default to escaping and safe rendering; only allow raw HTML with strict sanitization.
- Use templating best practices: avoid `render_template_string` for user content, avoid `|safe` except for vetted content.
- Add CSP headers as defense-in-depth in web apps.
- Include security tests for XSS payloads on any user-supplied HTML/markdown feature.
