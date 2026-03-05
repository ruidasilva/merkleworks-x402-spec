# x402 Deployment Modes

x402 supports multiple deployment configurations. The protocol semantics,
challenge format, proof format, and verification procedure are identical
across all modes. See [spec/x402.md](../spec/x402.md) Section 10.

Only two settlement modes exist:

• Client-funded settlement  
• Fee-delegated settlement

These modes differ only in who supplies miner fees. The transaction
structure and verification rules remain unchanged.

---

## Client-Funded Settlement

In client-funded mode, the client constructs and funds the payment
transaction directly.

The client:

- Spends the `nonce_utxo` specified in the challenge.
- Creates the payment output to `payee_locking_script_hex`.
- Provides additional inputs required to pay miner fees.
- Broadcasts the transaction to the settlement layer.

The server issues challenges containing the required payment conditions
including the `nonce_utxo` and the `payee_locking_script_hex`.

Nonce UTXOs originate from the server or gateway and are provided to the
client in the challenge.

**Use case**

Clients with direct access to the settlement layer and their own UTXOs.

---

## Fee-Delegated Settlement

In fee-delegated mode, a third party (a **delegator**) sponsors the miner
fees required to broadcast the payment transaction.

The transaction construction process is:

1. The client constructs a partial transaction containing:
   - the nonce UTXO spend
   - the payment output to the server
2. The client sends the partial transaction to the delegator.
3. The delegator adds inputs and/or outputs necessary to pay miner fees.
4. The delegator signs the fee inputs and finalizes the transaction.
5. The delegator returns the finalized transaction to the client.
6. The client broadcasts the transaction to the settlement layer.
7. The client includes the transaction in the `X402-Proof` and retries the request.

The protocol does not define how the client and delegator communicate.
This interaction is outside the scope of the protocol.

The delegator may be:

- a third-party service
- the server itself
- infrastructure operated by the API provider

Regardless of who acts as the delegator, the transaction must satisfy
the challenge conditions and pass the same verification procedure.

**Use case**

Clients that do not manage UTXOs or miner fees directly.

---

## Summary

x402 defines only two settlement modes:

| Mode          | Who Pays Miner Fees |
| ------------- | ------------------- |
| Client-funded | Client              |
| Fee-delegated | Delegator           |

The protocol semantics, challenge format, proof format, and verification
rules are identical in both cases.

Deployment configuration does not change protocol behavior.
