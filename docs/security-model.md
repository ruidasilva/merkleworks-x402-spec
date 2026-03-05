# x402 Security Model

This document explains the security properties of the x402 protocol in non-normative terms. The canonical specification is [spec/x402.md](../spec/x402.md). This page does not add new protocol rules.

## Replay Protection via Nonce UTXO

Each challenge includes a unique nonce UTXO. The payment transaction MUST spend that UTXO (spec Section 6). Because a UTXO can be spent only once, the settlement layer's double-spend protection ensures each proof is cryptographically single-use. A proof cannot be replayed: the underlying nonce has already been consumed. See spec Section 8.

## Deterministic Request Binding

The challenge binds to a specific HTTP request: method, path, query, selected header hash, and body hash. The proof must reference the same binding. The server recomputes these values and verifies they match the proof and the inbound request (spec Section 7, Request Binding Validation). If any field differs, the server MUST reject the proof. This prevents a proof for one request from being used to authorize a different request.

## Challenge Price Commitment

The challenge commits to the settlement conditions: `amount_sats` and `payee_locking_script_hex`. These are bound into the challenge object, and `challenge_sha256` is the hash of the canonical JSON. The proof references this hash. The server verifies that the payment transaction pays at least `amount_sats` to the specified locking script. The server cannot change the price after issuing the challenge without invalidating the hash; the client can verify the challenge before paying.

## Mempool Acceptance Gating

When `require_mempool_accept` is true, the server verifies that the payment transaction has been accepted to the mempool (or an equivalent transaction acceptance signal) before executing the request (spec Section 7). This allows execution before blockchain confirmation. Operational considerations:

- **False negatives:** A valid transaction might not yet be visible to the server's mempool view due to propagation delay. The server may reject a valid proof if it checks too quickly.
- **Delays:** The client may need to wait for propagation before retrying. Implementations should consider retry policies and timeouts.
- **Acceptance signal ambiguity:** Different nodes may have different mempool views. The specification refers to "equivalent transaction acceptance signal from the settlement layer" to allow implementation flexibility.

## Economic Abuse Considerations

Servers may wish to rate limit unpaid requests (spec Section 12, Security Considerations). Clients that repeatedly request 402 without paying consume server resources. Rate limiting unpaid 402 responses is an operational mitigation, not a protocol rule. The specification does not define how or when to apply such limits.

## Out of Scope

The x402 specification does not define:

- **Identity or authentication:** The protocol authorizes based on proof of payment, not identity. User authentication, if needed, is a separate concern.
- **Wallet security:** Key management, seed storage, and wallet practices are out of scope for the protocol.
- **Settlement-layer consensus rules:** Transaction validity, block propagation, and consensus mechanics are defined by the settlement layer, not by x402.

These concerns affect real-world deployments but are not part of the protocol specification.
