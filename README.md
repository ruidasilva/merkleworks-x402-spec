# x402 Protocol Specification

Canonical specification of the **x402 Stateless Settlement-Gated HTTP Protocol**.

x402 defines a deterministic use of HTTP status code **402 Payment Required**
that allows APIs and services to require **verifiable economic settlement**
before executing a request.

The protocol enables:

• pay-per-request APIs  
• machine-to-machine commerce  
• economic rate limiting  
• stateless payment authorization  

without requiring:

• user accounts  
• API keys  
• subscriptions  
• billing infrastructure  

---

## Specification

The canonical protocol specification is located at:

spec/x402.md

---

## Reference Implementation

Reference implementation:

https://github.com/ruidasilva/x402-gateway

---

## License

Apache License 2.0
