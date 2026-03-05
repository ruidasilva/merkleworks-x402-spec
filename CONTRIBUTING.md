# Contributing to the x402 Protocol Specification

Thank you for your interest in the x402 protocol.

This repository contains the **canonical specification** of the x402
Stateless Settlement-Gated HTTP Protocol.

## Scope of This Repository

This repository defines:

- protocol semantics
- message formats
- verification rules
- interoperability requirements

Reference implementations are maintained in separate repositories.

## Reporting Issues

If you believe you have found:

• a specification ambiguity  
• an interoperability problem  
• a security concern  

please open a GitHub issue describing the problem.

When possible include:

- the section of the specification
- the expected behavior
- the observed behavior

## Proposing Changes

Protocol changes should be proposed via Pull Request.

All changes must:

• preserve protocol invariants  
• maintain deterministic verification  
• avoid introducing trusted intermediaries  

Pull requests that modify protocol semantics should include:

- rationale
- compatibility considerations
- migration strategy (if applicable)

## Editorial Changes

Non-semantic changes are welcome, including:

• clarifications  
• grammar fixes  
• documentation improvements  

## Specification Authority

The canonical protocol specification is defined in:

```
spec/x402.md
```

Implementations must conform to the normative language defined there.

## License

By contributing, you agree that your contributions will be licensed under the
Apache License 2.0.
