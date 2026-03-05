# x402 Test Vector 001

This test vector validates deterministic computation of `challenge_sha256`.

The hash is computed as:

```
challenge_sha256 = SHA256(canonical_challenge_json_bytes)
```

Canonical JSON encoding MUST follow RFC 8785.

---

## Challenge Object (Canonical JSON)

```
{"amount_sats":50,"domain":"api.example.com","expires_at":1700000000,"method":"GET","nonce_utxo":{"locking_script_hex":"76a91400112233445566778899aabbccddeeff0011223388ac","satoshis":1,"txid":"4f8b42a7c1d7c0e0a3e3f7b8d4c5a6b7c8d9e0f112233445566778899aabbcc","vout":0},"path":"/v1/weather","payee_locking_script_hex":"76a91489abcdefabbaabbaabbaabbaabbaabbaabba88ac","query":"city=lisbon","req_body_sha256":"e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855","req_headers_sha256":"5b2d5c2d9c7e1a2e3f0b4f6d7c8a9e0f1a2b3c4d5e6f7081920a1b2c3d4e5f60","require_mempool_accept":true,"scheme":"x402-bsv","v":1}
```

---

## Expected SHA256

```
e1c2b034b378048b8a7299137f9ffcabfe0fc6a06018f15dd05b553858237aa9
```

---

## Description

This vector ensures implementations:

- correctly apply RFC 8785 canonical JSON encoding
- compute SHA256 of the canonical byte representation
- encode the result as lowercase hexadecimal

---

## Verification Example

Pseudo-code:

```
canonical = RFC8785_JSON_SERIALIZE(challenge_object)
hash = SHA256(canonical)
hex(hash) == expected_sha256
```
