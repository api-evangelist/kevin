---
name: Initiate an account-to-account bank payment
description: Initiate a PSD2 bank payment with kevin., redirect the payer to their bank, and confirm settlement.
api: "@kevin.eu/kevin-platform-client"
operations: [getCountries, getBanks, initiatePayment, getPayment, getPaymentStatus]
---

# Initiate an account-to-account bank payment (kevin.)

Use the kevin. Payment Initiation Service to move money bank-to-bank across Europe.

## Auth
Authenticate at the merchant level with `Client-Id` + `Client-Secret` headers
(SDK: `new kevin.Client(clientId, clientSecret)`). See
`authentication/kevin-authentication.yml`.

## Steps
1. `getCountries` / `getBanks` — resolve the payer's country and target bank (ASPSP).
2. `initiatePayment` — supply a `Redirect-URL` header and a body with `amount`,
   `currencyCode`, `description`, and `bankPaymentMethod` (`creditorName`,
   `endToEndId`, `creditorAccount.iban`). Set query `redirectPreferred: true` to
   get a hosted bank-selection/authorization URL at `redirect.kevin.eu`.
3. Redirect the payer to the returned URL to authorize at their bank.
4. `getPaymentStatus` (or `getPayment`) — poll for the status, or rely on the
   signed webhook (`asyncapi/kevin-webhooks.yml`) whose `bankStatus`/`statusGroup`
   fields report ISO 20022 states (e.g. `ACSP`, `STRD`).

## Rules
- `endToEndId` is your payment reference, not an idempotency key — track it yourself.
- Verify every webhook with `SecurityManager.verifySignature(body, headers, url, timestampTimeout)`.
- Handle `KevinBadRequestError` (400) and `KevinUnauthorizedError` (401); see
  `errors/kevin-error-codes.yml`.
