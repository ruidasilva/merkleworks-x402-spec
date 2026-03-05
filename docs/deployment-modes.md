# x402 Deployment Modes

x402 supports multiple deployment configurations. The protocol semantics, challenge format, proof format, and verification procedure are identical across modes. See [spec/x402.md](../spec/x402.md) Section 10.

## Client-Funded Settlement

In client-funded mode, the client:

- Constructs the payment transaction.
- Supplies all inputs (including the nonce UTXO spend and the payment output).
- Pays miner fees from its own funds.
- Broadcasts the transaction to the settlement layer.

The server issues challenges with `payee_locking_script_hex` and `nonce_utxo`. The client is responsible for obtaining or holding the nonce UTXO (or interacting with a service that provides it) and for funding the transaction.

**Use case:** Clients with direct settlement-layer access. No third-party fee sponsorship required.

## Fee Delegation

In fee-delegated mode, a third party (the delegator) sponsors miner fees. The client still constructs the payment logic (spending the nonce, paying the server). The delegator adds inputs and/or outputs to cover miner fees.

Conceptually:

1. The client builds a partial transaction (nonce spend, payee output).
2. The partial transaction is sent to the delegator.
3. The delegator finalizes the transaction (adds fee inputs, signs, completes).
4. The delegator returns the finalized transaction to the client.
5. The client broadcasts the transaction to the settlement layer.
6. The client includes the finalized transaction in the proof and retries the request.

The protocol does not define how the client and delegator communicate. That is out of scope. The protocol only requires that the proof contain a valid transaction that satisfies the challenge. This deployment configuration does not change protocol semantics. Whoever constructs the transaction, the verification rules are the same.

**Use case:** Clients without settlement-layer wallets or UTXOs. UX is simplified when users do not need to manage miner fees directly.

## Sponsored Settlement

In sponsored mode, the server (or a service acting on its behalf) absorbs the settlement cost. The server may:

- Pre-fund nonce UTXOs.
- Accept payment transactions that the server itself constructs or sponsors.
- The server may operate with relaxed verification policies in controlled environments such as testing or internal deployments.

The specification does not define sponsored behavior in detail. Sponsored deployments are implementation-specific. The protocol semantics remain: the server executes the request when it has verified a valid proof. How the server obtains or sponsors that proof is a deployment choice.

**Use case:** Free tiers, promotional access, or internal APIs where the server bears the cost.

## Summary

All three modes use the same challenge and proof format. The difference is who constructs the transaction and who pays. The server's verification logic is unchanged.
