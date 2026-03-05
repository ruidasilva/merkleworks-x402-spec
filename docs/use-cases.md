# x402 Use Cases

This document describes practical applications of the x402 protocol. The canonical specification is [spec/x402.md](../spec/x402.md). x402 enables settlement-gated access to HTTP resources without accounts or traditional billing.

## Pay-per-Request APIs

APIs can charge per request instead of per subscription. The server issues a challenge with `amount_sats` for each protected request. The client pays, obtains proof, and retries. No API keys, no usage metering, no billing backend. The settlement layer is the ledger.

**How x402 enables it:** The challenge commits to a price. The proof commits to payment. The server verifies and executes. Each request is independently paid.

## Machine-to-Machine Commerce

Services can call other services and pay per call. No OAuth flows, no shared secrets, no pre-established contracts. A service receives 402, pays, and retries. The payment proof is the credential.

**How x402 enables it:** Stateless verification. No session or account. The calling machine proves payment directly. Ideal for service meshes and microservices where machines need to pay each other on demand.

## AI Agent Access to APIs

AI agents can pay for API access without human intervention. The agent receives 402, constructs a transaction (or delegates to a wallet service), and retries with proof. No API key to steal or rotate. Payment is per-request and auditable.

**How x402 enables it:** No accounts. The agent only needs the ability to create and submit payments. The protocol binds the payment to the specific request, preventing misuse.

## Economic Rate Limiting

Rate limiting can be economic instead of purely heuristic. Expensive operations require higher `amount_sats`. Clients that overuse the API pay more. Abuse becomes costly rather than free.

**How x402 enables it:** The challenge encodes the required payment. The server can vary `amount_sats` by endpoint, load, or client behavior. Economic cost replaces or supplements traditional rate limits.

## Data Marketplaces

Data providers can sell access per query. Each query returns 402 with a challenge. The buyer pays, receives proof, and retries to get the data. No subscription, no bulk pricing model required.

**How x402 enables it:** Pay-per-request. The data provider controls price per query. The buyer pays only for what they request. Settlement is verifiable and does not require a centralized marketplace operator.

## Compute Markets

Compute providers can charge per job. The client submits a job, receives 402, pays, and retries. The job runs only after payment is verified. No prepaid credits or invoicing.

**How x402 enables it:** Settlement before execution. The server does not run the job until proof is valid. Prevents free-riding and simplifies pricing for short-lived or one-off compute tasks.
