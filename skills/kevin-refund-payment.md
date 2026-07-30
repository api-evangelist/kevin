---
name: Refund a payment and reconcile
description: Issue a refund against a completed kevin. payment and reconcile refund status.
api: "@kevin.eu/kevin-platform-client"
operations: [getPayment, initiatePaymentRefund, getPaymentRefunds, getPaymentStatus]
---

# Refund a payment and reconcile (kevin.)

## Auth
Merchant-level `Client-Id` + `Client-Secret` headers. See
`authentication/kevin-authentication.yml`.

## Steps
1. `getPayment` / `getPaymentStatus` — confirm the original payment reached a
   settled state before refunding.
2. `initiatePaymentRefund` — request a refund against the payment.
3. `getPaymentRefunds` — list refunds for the payment and track each refund's status.

## Rules
- Refunds are tied to the original payment id (`Payment has_many Refund`; see
  `data-model/kevin-data-model.yml`).
- Reconcile against the signed payment webhook rather than assuming synchronous
  completion (`asyncapi/kevin-webhooks.yml`).
- Handle `KevinBadRequestError` (400) / `KevinUnauthorizedError` (401).
