# x402 Protocol Specification

Canonical specification of the **x402 Stateless Settlement-Gated HTTP Protocol**.

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

## Status

Internet-Draft — Experimental

## Reference Implementation

An experimental reference implementation of the x402 protocol
is currently under development.

The reference gateway implementation will be published at:

https://github.com/ruidasilva/merkleworks-x402-gateway

## License

Apache License 2.0
