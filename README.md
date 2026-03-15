# x402 Protocol Specification

This repository contains the canonical specification of the x402 protocol.

x402 Stateless Settlement-Gated HTTP Protocol

**Specification version:** v1.0

Canonical specification of the x402 protocol.

x402 converts economic settlement into a stateless authorization primitive for HTTP.

x402 defines a deterministic use of HTTP status code **402 Payment Required**
that allows APIs and services to require verifiable economic settlement
before executing a request.

The protocol enables stateless request authorization based on settlement proof.

## Capabilities

x402 enables:

• pay-per-request APIs  
• machine-to-machine commerce  
• economic rate limiting  
• stateless payment authorization

without requiring:

• user accounts  
• API keys  
• subscriptions  
• billing infrastructure

## Specification

The canonical protocol specification is located at:

[spec/x402.md](spec/x402.md)

## Protocol Status

Version: v1.0  
Status: Stable

The v1.0 specification is considered frozen.

Implementations should target this version for interoperability.

Future protocol changes will be introduced through versioned extensions or future major versions to preserve compatibility.

## Conformance

An implementation is considered x402 compatible if it:

• implements the challenge-response flow defined in the specification  
• validates settlement proofs according to the verification rules  
• enforces replay protection requirements  
• adheres to the HTTP semantics defined by the protocol

Normative requirements use the terminology defined in RFC 2119.

## Versioning

x402 follows semantic versioning.

Major versions introduce protocol-level breaking changes.

Minor versions introduce backwards-compatible extensions.

Patch versions clarify documentation without changing protocol behaviour.

The v1 series will remain stable to preserve interoperability.

## Reference Implementation

An experimental reference implementation of the x402 protocol
is currently under development.

The reference gateway implementation will be published at:

https://github.com/merkleworks/x402-gateway

## License

Apache License 2.0
