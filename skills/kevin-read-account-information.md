---
name: Authorize and read bank account information
description: Run the kevin. AIS user-authorization flow and read accounts, transactions and balances.
api: "@kevin.eu/kevin-platform-client"
operations: [startAuth, receiveToken, refreshToken, getAccounts, getAccount, getAccountTransactions, getAccountBalance]
---

# Authorize and read bank account information (kevin.)

Use the kevin. Account Information Service (PSD2 AIS) to read a user's bank data
after they consent.

## Auth flow
1. `startAuth` — request authorization with the scopes you need
   (`payments`, `accounts_basic`; see `scopes/kevin-scopes.yml`) and a
   `Redirect-URL` header. Returns an auth key.
2. Redirect the user to authorize at their bank; on return you receive the auth key.
3. `receiveToken` — exchange the auth key for a bearer + refresh token pair.
4. `refreshToken` — exchange a refresh token for a fresh bearer token when it expires.

## Read steps (bearer token)
5. `getAccounts` — list the authorized accounts.
6. `getAccount` — account details.
7. `getAccountTransactions` — transactions for an account.
8. `getAccountBalance` — account balance.

## Rules
- Only request scopes you use; the bearer token authorizes per-consent AIS calls.
- Handle `KevinUnauthorizedError` (401) by refreshing or re-authorizing.
- See `authentication/kevin-authentication.yml` and `conventions/kevin-conventions.yml`.
