# Security Policy — x402 Protocol Specification

This repository contains the canonical specification of the x402 protocol.

## Reporting a Vulnerability

If you believe you have discovered a security vulnerability in the x402
protocol specification or a security issue that could affect
implementations, please report it responsibly.

### Preferred contact

Open a GitHub Issue and prefix the title with:

`[SECURITY]`

If the issue includes sensitive exploit details, avoid publishing
step-by-step weaponized instructions. Provide:

- a high-level description
- affected specification section(s)
- impact analysis
- suggested mitigation(s)

## Threat Model Scope

Relevant security considerations for x402 include:

- replay attempts (nonce UTXO reuse)
- request-binding bypass attempts (method/path/query/header/body mismatch)
- challenge substitution / downgrade attacks (scheme and version handling)
- mempool visibility failures or false negatives (acceptance signal ambiguity)
- miner ordering variance and double-spend races
- fee manipulation / economic denial-of-service (spam 402 requests)
- delegator abuse modes (if used in a deployment configuration)

## Non-Goals

x402 does not define:

- user identity or authentication
- key management
- wallet security
- settlement-layer consensus rules

These concerns are out of scope for the protocol specification but may
affect real-world deployments.

## Security Updates

Security-related clarifications to the specification will be recorded in:

`spec/CHANGELOG.md`
