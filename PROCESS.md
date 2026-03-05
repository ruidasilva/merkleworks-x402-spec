# x402 Specification Process

This document describes how the x402 protocol specification evolves.

## Specification Status

The current specification is published as:

**Internet-Draft — Experimental**

Future revisions may advance through the following stages:

1. Draft
2. Candidate
3. Stable

## Versioning

The specification uses semantic versioning.

Major versions indicate protocol changes that affect compatibility.

Minor versions include:

• clarifications  
• additional examples  
• editorial improvements  

Patch versions include editorial corrections only.

## Protocol Invariants

Certain properties of the protocol are considered **invariants** and
must not change without a new major version.

These include:

• settlement-gated request execution  
• deterministic request binding  
• nonce-based replay protection  
• stateless authorization  

Changes that violate these invariants would define a different protocol.

## Reference Implementations

Reference implementations may exist in separate repositories.

These implementations are used to validate the specification but
do not override the canonical specification.

## Compatibility

Changes to the protocol must consider interoperability between
independent implementations.

Backward-incompatible changes require a major version increment.
