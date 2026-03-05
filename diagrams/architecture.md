# x402 Architecture

```mermaid
flowchart LR

    Client["Client Application"]

    Server["HTTP Server
    x402 Gateway / Middleware"]

    Delegator["Optional Fee Delegator
    (Transaction Finalization)"]

    Settlement["Settlement Layer
    (e.g. BSV)"]

    Resource["Protected Resource
    API / Compute / Data"]

    Client -->|HTTP Request| Server

    Server -->|402 Challenge| Client

    Client -->|Payment Transaction| Settlement

    Client -->|HTTP Request + Proof| Server

    Server -->|Verify Transaction| Settlement

    Server -->|Execute Request| Resource

    Delegator -.optional tx finalization.-> Settlement
```

## Components

### Client

The client performs the following actions:

• receives the challenge
• constructs the settlement transaction
• retries the request with proof

### Server (x402 Gateway)

The server:

• issues settlement challenges
• verifies payment proofs
• validates transaction requirements
• executes the request when proof is valid

### Settlement Layer

The settlement layer provides:

• transaction validation
• double-spend protection
• replay protection via UTXO spending

### Optional Fee Delegator

In fee-delegated deployments:

• the client constructs a partial transaction
• a delegator finalizes the miner fee inputs
• the finalized transaction is returned to the client

The client still submits the proof to the server.

This component is **optional and does not change protocol semantics**.
