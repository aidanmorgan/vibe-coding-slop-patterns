# SLOP.SECURITY.008 — Naive crypto choices (weak hashes/insecure modes)

**Family:** SECURITY

## Summary

AI snippets often reach for outdated or insecure crypto: MD5/SHA1 for passwords, homebrew
encryption, static salts/IVs, or reversible “hashing.” These are attractive because they’re short
and easy to explain, but they fail modern security requirements.  Crypto mistakes are expensive to
unwind because they become baked into stored data and authentication flows.


## Examples (Python)

```python
import hashlib
def hash_password(pw: str) -> str:
    return hashlib.md5(pw.encode()).hexdigest()  # insecure for passwords
```

```python
import hashlib
def hash_token(token: str) -> str:
    return hashlib.sha1(token.encode()).hexdigest()
```

```python
from Crypto.Cipher import AES
def encrypt_ecb(key: bytes, data: bytes) -> bytes:
    cipher = AES.new(key, AES.MODE_ECB)  # insecure mode
    return cipher.encrypt(data)
```

## Remediation

- For passwords: use Argon2 or bcrypt via vetted libraries; never use general-purpose hashes.
- For encryption: use modern AEAD modes (e.g., AES-GCM) with unique nonces; never ECB.
- Define approved crypto primitives and ban everything else in review.
- Add tests and code review checklists for nonce/salt uniqueness and parameter correctness.
