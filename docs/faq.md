# x402 FAQ

Answers reflect the current specification. See [spec/x402.md](../spec/x402.md) for normative rules.

## Is x402 tied to a specific blockchain?

The protocol design is settlement-layer agnostic. The specification separates the HTTP layer from the settlement layer and does not mandate a single blockchain. Future versions could define additional schemes for other settlement systems. Implementations conform to the scheme(s) they support.

This reference specification and reference implementation target BSV because it provides characteristics well-suited for per-request settlement: deterministic low fees, high throughput, and mempool acceptance behavior suitable for request gating. That is a practical deployment choice, not a normative protocol requirement. Other settlement layers could be targeted by defining additional scheme values.

## Why use HTTP 402?

HTTP 402 Payment Required has existed since early HTTP but was never fully standardized. x402 gives it a deterministic meaning: the server requires payment before executing the request, and the client proves payment via the `X402-Proof` header. Using 402 signals intent clearly and allows generic HTTP clients to recognize payment-required responses.

## Does the server commit to the price in the challenge?

Yes. The challenge includes `amount_sats`, which defines the minimum payment. The server commits to this when issuing the challenge. The client must pay at least this amount to the `payee_locking_script_hex` for the proof to be valid.

## Can challenges be reused?

No. Each challenge includes a unique `nonce_utxo`. That UTXO must be spent exactly once. Once spent, the proof is single-use. Any attempt to reuse the same proof or the same nonce for another request would fail: either the nonce is already spent (replay), or the request binding would not match (different request).

Additionally, challenges include `expires_at`. Proofs for expired challenges are rejected.

## Does the protocol require accounts?

No. The specification states that the server MUST NOT require client accounts, API keys, or subscriptions as a condition of x402 authorization. Authorization is derived solely from verification of the proof. The client proves payment; it does not prove identity.

## How does replay protection work?

Replay protection relies on the settlement layer's double-spend mechanism. Each challenge references a specific nonce UTXO. The payment transaction must spend that UTXO. A UTXO can be spent only once. Therefore, each proof is cryptographically single-use. A proof cannot be replayed because the underlying nonce UTXO has already been consumed. See spec Section 8.

## What if the transaction is not yet confirmed?

The challenge can set `require_mempool_accept` to `true`. In that case, the server must verify that the payment transaction has been accepted to the mempool (or an equivalent transaction acceptance signal) before executing the request. This allows execution before blockchain confirmation while still requiring the transaction to be valid and accepted. If `require_mempool_accept` is false, the server's policy for accepting unconfirmed transactions is implementation-defined; the spec does not mandate confirmation depth.

## Can the client change the request when retrying?

No. The proof binds to the original request (method, path, query, header hash, body hash). If the client changes any of these, the request binding check will fail and the server will reject the proof. The client MUST retry with the identical request plus the `X402-Proof` header.
