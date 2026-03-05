# x402 Protocol Invariants

This document defines the canonical invariants of the x402 protocol. Invariants are fundamental properties that MUST remain stable across implementations and deployments. They constitute the constitutional guarantees of the protocol.

The normative specification is [spec/x402.md](../spec/x402.md). This document summarizes invariant guarantees; it does not introduce new normative rules.

---

## Settlement Model

### S-1 Settlement-Gated Authorization

**Invariant**

Request execution MUST be conditioned solely on verifiable settlement proof. Accounts, API keys, and subscriptions are not part of the authorization model.

**Explanation**

The server authorizes execution only after validating the settlement transaction. Authorization is derived exclusively from proof verification. No identity-based credentials are required.

### S-2 Stateless Verification

**Invariant**

The server MUST NOT maintain client state for correctness. Verification depends only on the proof, the settlement layer, and the inbound request.

**Explanation**

The server does not track sessions, pending payments, or client identities. Each request is self-contained. Statelessness enables horizontal scaling and eliminates shared session stores.

### S-3 Mempool Acceptance Sufficient for Gating

**Invariant**

Mempool acceptance (0-conf) is sufficient for request gating. Block confirmation is NOT required by the protocol.

**Explanation**

When `require_mempool_accept` is true, the server verifies that the payment transaction has been accepted to the mempool or equivalent. Execution may proceed before blockchain confirmation.

### S-4 Funding Mode Invariance

**Invariant**

Wire format, challenge structure, proof structure, and verification procedure MUST NOT change based on who funds the transaction (client, delegator, or server).

**Explanation**

Client-funded, fee-delegated, and sponsored deployments use identical protocol semantics. Funding mode is a deployment choice, not a protocol variant.

### S-5 Client Broadcasts; Delegator Never Broadcasts

**Invariant**

In fee-delegated deployments, the delegator MUST return the finalized transaction to the client. The client MUST broadcast the transaction. The delegator MUST NOT broadcast.

**Explanation**

The client retains control of transaction submission. The delegator finalizes miner-fee inputs and returns the completed transaction. Broadcasting is the client's responsibility.

---

## Replay Protection

### R-1 UTXO Single-Spend Semantics

**Invariant**

Replay protection MUST be enforced exclusively through UTXO single-spend semantics at the settlement layer.

**Explanation**

Each challenge references a unique nonce UTXO. The payment transaction must spend it. A UTXO can be spent only once. The settlement layer's double-spend protection provides replay protection.

### R-2 No Persistent Nonce Tracking

**Invariant**

Servers MUST NOT rely on persistent nonce tracking for replay protection correctness. Replay protection is derived solely from settlement-layer double-spend protection of the nonce UTXO.

**Explanation**

The server does not maintain a database of spent nonces. Correctness does not depend on server-side nonce state. The UTXO spend is the only replay guard.

---

## Nonce Model

### N-1 Nonce-as-UTXO

**Invariant**

The nonce MUST be a UTXO that MUST be spent in the payment transaction. `nonce_utxo.satoshis` MUST be greater than zero.

**Explanation**

The nonce is represented as a settlement-layer UTXO (txid, vout, satoshis, locking_script_hex). The payment transaction must include an input that spends this outpoint.

### N-2 Gateway-Issued Nonces Only

**Invariant**

Nonce UTXOs MUST originate from the server issuing the challenge (or a service acting on behalf of that server). Clients MUST NOT supply arbitrary nonce UTXOs.

**Explanation**

Client-supplied nonces are invalid. The gateway controls nonce issuance. Clients obtain nonces through the challenge (the 402 response). This is a protocol invariant, not an implementation choice.

### N-3 Atomic Nonce Reservation

**Invariant**

Atomic nonce reservation MUST precede economic side effects. Separate check-then-record operations are non-compliant.

**Explanation**

Nonce allocation must be atomic. The nonce UTXO must be reserved in a single operation before the challenge is issued. Non-atomic reservation risks double allocation.

---

## Request Binding

### B-1 Deterministic Request Binding

**Invariant**

Proofs MUST be cryptographically bound to method, path, query, body hash, header hash, and domain.

**Explanation**

The challenge and proof include request-binding fields. The server verifies that the inbound request matches the binding. A proof for one request cannot authorize a different request.

### B-2 Canonical JSON (RFC 8785)

**Invariant**

Challenge and proof JSON objects MUST be serialized according to RFC 8785 (JSON Canonicalization Scheme) before hashing.

**Explanation**

Deterministic serialization ensures identical byte representation across implementations. Keys are sorted lexicographically; whitespace is excluded; numbers use shortest decimal form.

### B-3 Canonical Header Binding

**Invariant**

The request header binding string MUST be constructed by: lowercasing header names, trimming whitespace, sorting by name, concatenating as `name:value\n`. The SHA-256 digest MUST be encoded as lowercase hex.

**Explanation**

Header binding must be deterministic. The server selects which headers to bind; the construction rules ensure consistent hashing.

---

## Architecture Boundaries

### A-1 Three-Layer Separation

**Invariant**

Three layers MUST be kept distinct: Gatekeeper (HTTP, challenge, proof verification), Delegator (fee sponsorship, transaction construction), and Settlement Layer (UTXO enforcement). Boundaries MUST NOT be blurred.

**Explanation**

The gatekeeper handles HTTP and proof verification. The delegator handles fee sponsorship and tx finalization. The settlement layer enforces UTXO semantics. Separation prevents single-point failures and maintains clear responsibility boundaries.

### A-2 Gatekeeper Never Signs

**Invariant**

The gatekeeper MUST NOT sign transactions. The gatekeeper MUST NOT hold private keys.

**Explanation**

The gatekeeper verifies proofs; it does not construct or sign transactions. Key management is outside the gatekeeper's responsibility.

### A-3 Delegator Never Parses HTTP

**Invariant**

The delegator MUST NOT parse HTTP requests. The delegator MUST NOT understand request paths, methods, or headers.

**Explanation**

The delegator receives partial transactions and returns finalized transactions. It does not interpret HTTP semantics. Request binding is the gatekeeper's concern.

---

## Verification Procedure

### V-1 Ordered Verification

**Invariant**

The server MUST perform verification in the following order: decode proof → validate version and scheme → recompute and validate challenge hash → validate request binding → check expiry → decode transaction and verify txid → verify nonce spend → verify payee output → verify mempool acceptance (if required).

**Explanation**

The verification procedure is deterministic and ordered. Each step depends on prior steps. Implementations must follow this order.

### V-2 Scheme Identifier

**Invariant**

The canonical scheme identifier for this specification is `bsv-tx-v1`.

**Explanation**

Challenge and proof objects use the same scheme identifier. Servers reject unsupported schemes. Consistency ensures interoperability.
