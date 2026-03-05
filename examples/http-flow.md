# x402 HTTP Flow Example

Example request flow using x402 settlement gating.

---

## 1. Client requests resource

GET /v1/weather?city=lisbon
Host: api.example.com

---

## 2. Server responds with challenge

HTTP/1.1 402 Payment Required

X402-Challenge: <base64url(challenge-json)>

---

## 3. Client performs settlement

Client constructs a transaction that:

• spends the `nonce_utxo`
• pays `amount_sats` to `payee_locking_script_hex`

---

## 4. Client retries request with proof

GET /v1/weather?city=lisbon
Host: api.example.com

X402-Proof: <base64url(proof-json)>

---

## 5. Server verifies proof

Server performs:

1. challenge hash verification
2. request binding verification
3. transaction validation
4. nonce spend verification
5. payment output verification
6. optional mempool acceptance check

---

## 6. Server executes request

HTTP/1.1 200 OK

{ weather response }
