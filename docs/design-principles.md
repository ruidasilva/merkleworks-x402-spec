# x402 Design Principles

This document explains the architectural ideas behind the x402 protocol. The canonical specification is defined in [spec/x402.md](../spec/x402.md).

## Settlement as an Authorization Primitive

In traditional HTTP APIs, authorization is typically based on identity: the client presents credentials (API key, OAuth token, session cookie) and the server checks whether that identity is permitted to access the resource.

x402 inverts this model. Instead of "who are you?" the server asks "have you paid?" Payment proof becomes the authorization primitive. The server executes the request only when it can verify that settlement has occurred.

**Why:** This allows pay-per-request models without user accounts, billing systems, or payment processors. The settlement layer provides the economic signal directly. Authorization and payment collapse into a single step.

## Stateless Verification

The server does not maintain client state. It does not track sessions, pending payments, or client identities. When a request arrives with an `X402-Proof` header, the server verifies the proof from the data in the proof itself and the settlement layer.

**Why:** Statelessness reduces server complexity, scales horizontally, and eliminates the need for shared session stores. Each request is self-contained. The settlement layer is the only source of truth for whether payment occurred.

## Deterministic Request Binding

The challenge binds to a specific HTTP request: method, path, query, selected headers, and body. The proof must reference the same binding. The server recomputes the request hash and verifies it matches.

**Why:** Without deterministic binding, a proof for one request could be replayed against another. Binding ensures the proof authorizes exactly the request the client is making. Canonical JSON (RFC 8785) and explicit hashing rules ensure all implementations compute identical hashes.

## Replay Protection via Nonce UTXO

Each challenge includes a unique nonce UTXO that must be spent in the payment transaction. A UTXO can be spent only once. Therefore each proof is cryptographically single-use.

**Why:** The settlement layer's native double-spend protection provides replay protection. No server-side nonce store is needed. The protocol relies on the settlement layer's existing guarantees rather than inventing new mechanisms.

## Separation of Protocol and Settlement Layer

x402 defines how HTTP requests are gated by settlement proof. It does not define how transactions are validated, how consensus works, or how the settlement layer orders and confirms transactions. The protocol speaks to the settlement layer only through well-defined interfaces: challenge, proof, and transaction validation.

**Why:** This separation allows x402 to be settlement-layer agnostic in principle. The spec currently defines `scheme` values (e.g. `bsv-tx-v1`) for a specific settlement layer, but the architecture does not require a single blockchain. Different schemes could target different settlement layers in future versions.
