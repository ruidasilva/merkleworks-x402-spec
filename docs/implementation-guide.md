# x402 Implementation Guide

This guide provides implementation guidance for developers. The normative specification is [spec/x402.md](../spec/x402.md). All implementation behavior MUST conform to the specification.

## Server-Side Implementation Overview

The server acts as an x402 gateway or middleware. It:

1. Intercepts requests to protected resources.
2. If no valid `X402-Proof` is present, responds with HTTP 402 and `X402-Challenge`.
3. If `X402-Proof` is present, runs the verification procedure.
4. On success, executes the request and returns the resource. On failure, returns 402 or 400 as defined in Section 9 of the spec.

**Integration points:** The server needs access to a nonce UTXO source, the payee locking script, and the ability to verify transactions against the settlement layer. Optionally, it needs mempool/acceptance verification when `require_mempool_accept` is true.

## Client Implementation Overview

The client:

1. Sends the initial request without proof.
2. Receives 402 with `X402-Challenge` header.
3. Base64url-decodes the challenge, parses the JSON.
4. Constructs a payment transaction that spends the `nonce_utxo` and pays `amount_sats` to `payee_locking_script_hex`.
5. Broadcasts the transaction to the settlement layer.
6. Waits for acceptance (if required by `require_mempool_accept` or deployment policy).
7. Computes `challenge_sha256` from the canonical JSON of the challenge.
8. Builds the proof object with `challenge_sha256`, request binding, and payment.
9. Retries the original request with `X402-Proof` header (base64url-encoded proof JSON).

## Middleware Integration

x402 can be implemented as middleware that sits in front of existing application logic. The middleware inspects the request, determines whether payment is required, and either issues a challenge or verifies the proof before passing the request to the downstream handler.

**Common frameworks:**

- **Node/Express:** Middleware that checks for `X402-Proof`, validates it, and either calls `next()` or returns 402.
- **Ruby on Rails:** Rack middleware or controller-level before_action that performs the same logic.
- **Go:** `http.Handler` or `net/http` middleware wrapping protected handlers.
- **Python (Django, Flask, FastAPI):** Decorator or middleware that wraps protected views.

The middleware must have access to challenge generation logic and verification logic. It must also compute or verify request binding (method, path, query, header hash, body hash) consistently with the challenge it issued.

## Challenge Generation

The server generates a challenge object containing:

- `v`, `scheme`, `domain`, `method`, `path`, `query`
- `req_headers_sha256`, `req_body_sha256` (computed per spec Section 4)
- `amount_sats`, `payee_locking_script_hex`, `nonce_utxo`
- `expires_at`, `require_mempool_accept`

The challenge MUST be serialized using RFC 8785 canonical JSON before base64url encoding for the `X402-Challenge` header. See the spec for the exact field requirements and the canonical header-binding string construction.

## Proof Verification

The server MUST follow the verification procedure in spec Section 7:

1. Decode the proof (base64url, then JSON).
2. Verify `challenge_sha256` equals SHA256 of the canonical challenge bytes.
3. Validate request binding (method, path, query, headers, body) against the proof and the actual request.
4. Check `expires_at` has not passed.
5. Decode the transaction from `rawtx_b64`, verify `txid` matches.
6. Verify the transaction spends `nonce_utxo` and pays at least `amount_sats` to `payee_locking_script_hex`.
7. If `require_mempool_accept` is true, verify acceptance on the settlement layer.

Reject with 402 or 400 per spec Section 9 when any check fails.

## Transaction Validation

The server MUST validate the payment transaction per spec Section 6:

- Input outpoint must exactly match `nonce_utxo` (txid, vout).
- An output must pay at least `amount_sats` to `payee_locking_script_hex`.
- `txid` must equal the double SHA-256 of the raw transaction bytes (Bitcoin-style).
- The transaction must be syntactically valid under the scheme's rules.

## Error Handling

Return status codes as defined in spec Section 9:

- Expired challenge → 402
- Nonce already spent → 402
- Invalid transaction → 400
- Request binding mismatch → 400
- Missing or invalid proof when payment required → 402 with `X402-Challenge`
- Unsupported scheme → 400
- Insufficient payment → 402

Implementations should log failures for debugging but avoid leaking sensitive details in responses.
