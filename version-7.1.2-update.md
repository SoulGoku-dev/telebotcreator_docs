# Version 7.1.2 Update

*Released: April 2026 | Platform version: 7.1.2 · Telegram Bot API 10.1*

This update introduces the **Security Library** (`libs.security`) and **enhanced webhook capabilities** with full access to request headers and client IP address.

---

## New: libs.security — Cryptographic Toolkit

A new built-in library providing HMAC signatures, Ed25519 asymmetric signature verification, AES encryption/decryption, and hashing — all accessible without imports.

### HMAC (Symmetric Signatures)

Sign and verify data using a shared secret key with HMAC-SHA256.

```python
# Sign data
signature = libs.security.hmac_sign("my_secret_key", '{"amount":100}')

# Verify signature (timing-safe)
is_valid = libs.security.hmac_verify("my_secret_key", '{"amount":100}', signature)
# Returns True or False

# Use different algorithms: "sha256" (default), "sha512", "md5"
sig = libs.security.hmac_sign("secret", "data", algorithm="sha512")
```

### Ed25519 (Asymmetric Signature Verification)

Verify Ed25519 signatures using a public key. The signer holds a private key; your bot only needs the public key to verify authenticity. Even if someone accesses your bot's code, they cannot forge signatures — they can only verify them.

```python
# Verify an Ed25519 signature
public_key_hex = "300f4313f53eb2a1a5c418bf44bae1d50596f3eed7ca5428e57cea40223bb1ed"
signature_hex = options.headers.get("X-Coinsway-Signature", "")
raw_body = options.data

is_valid = libs.security.ed25519_verify(public_key_hex, signature_hex, raw_body)

if is_valid:
    bot.sendMessage("Signature verified!")
else:
    bot.sendMessage("Invalid signature — request rejected.")
```

### Coinsway Webhook Verification

A convenience function specifically for verifying Coinsway payment gateway webhook callbacks:

```python
# In your webhook command (e.g., /coinsway_callback):
public_key = "300f4313f53eb2a1a5c418bf44bae1d50596f3eed7ca5428e57cea40223bb1ed"
signature = options.headers.get("X-Coinsway-Signature", "")

if libs.security.verify_coinsway_webhook(public_key, signature, options.data):
    data = options.json
    amount = data.get("amount_human", "0")
    bot.sendMessage(f"Verified deposit: {amount} USDT")
else:
    Api.setWebhookResult({"ok": False, "error": "Invalid signature"})
```

### AES Encryption (Fernet)

Encrypt and decrypt strings using symmetric AES encryption. Useful for storing sensitive data.

```python
# Generate a new encryption key (do this once, store in .env)
key = libs.security.generate_key()

# Encrypt
encrypted = libs.security.encrypt("my secret data", key)

# Decrypt
decrypted = libs.security.decrypt(encrypted, key)
# decrypted == "my secret data"
```

### Hashing

Generate one-way hashes for data integrity checks.

```python
h = libs.security.sha256("hello world")   # SHA-256 hex digest
h = libs.security.sha512("hello world")   # SHA-512 hex digest
h = libs.security.md5("hello world")      # MD5 hex digest (not for security)
```

---

## New: Webhook Headers & Client IP

Webhook commands now receive the full HTTP request headers and the caller's IP address through the `options` variable.

### options.headers

A dictionary of all HTTP headers sent with the webhook request (excluding `Host`, `Cookie`, and `Authorization` for security).

```python
# Access any header
content_type = options.headers.get("Content-Type", "")
signature = options.headers.get("X-Coinsway-Signature", "")
custom_header = options.headers.get("X-My-Custom-Header", "")

bot.sendMessage(f"Content-Type: {content_type}")
```

### options.ip

The IP address of the client that called the webhook.

```python
# Check caller IP
caller_ip = options.ip
bot.sendMessage(f"Request from: {caller_ip}")

# IP whitelist example
ALLOWED_IPS = ["212.47.77.122", "1.2.3.4"]
if options.ip not in ALLOWED_IPS:
    Api.setWebhookResult({"ok": False, "error": "Forbidden"})
    returnCommand()
```

### Backward Compatibility

- `options.data` and `options.json` continue to work exactly as before.
- `options.headers` and `options.ip` are only present for webhook-triggered commands. They will not exist in commands triggered by Telegram messages, callback queries, or `runCommandAfter`.
- Existing bot code is **not affected** by this update.

---

## Summary of options in Webhook Commands

| Key | Type | Description |
|-----|------|-------------|
| `options.data` | `str` | Raw request body as string |
| `options.json` | `dict/None` | Parsed JSON body (if Content-Type is application/json) |
| `options.headers` | `dict` | **New** — HTTP request headers |
| `options.ip` | `str` | **New** — Caller's IP address |

---

## libs.security Function Reference

| Function | Description |
|----------|-------------|
| `hmac_sign(secret, data, algorithm="sha256")` | Create HMAC signature. Returns hex string. |
| `hmac_verify(secret, data, signature, algorithm="sha256")` | Verify HMAC signature. Returns `True`/`False`. |
| `ed25519_verify(public_key_hex, signature_hex, data)` | Verify Ed25519 signature. Returns `True`/`False`. |
| `verify_coinsway_webhook(public_key_hex, signature_hex, raw_body)` | Verify Coinsway webhook signature. Returns `True`/`False`. |
| `generate_key()` | Generate a new Fernet encryption key. |
| `encrypt(plaintext, key)` | Encrypt a string with AES. Returns encrypted token. |
| `decrypt(token, key)` | Decrypt a Fernet token. Returns plaintext. |
| `sha256(data)` | SHA-256 hash. Returns hex string. |
| `sha512(data)` | SHA-512 hash. Returns hex string. |
| `md5(data)` | MD5 hash. Returns hex string. |

---

## Upgrading

All changes are backward compatible. Simply restart your bot to access the new `libs.security` library and webhook enhancements. No code changes are required for existing bots.
