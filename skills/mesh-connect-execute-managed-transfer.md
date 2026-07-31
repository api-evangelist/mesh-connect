---
name: Quote, preview, and execute a managed crypto transfer
description: Discover supported tokens/networks, quote and preview a managed transfer, execute it, and reconcile via webhooks.
api: openapi/mesh-connect-openapi-original.json
operations:
  - "GET /api/v1/transfers/managed/tokens"
  - "GET /api/v1/transfers/managed/networks"
  - "POST /api/v1/transfers/managed/quote"
  - "POST /api/v1/transfers/managed/preview"
  - "POST /api/v1/transfers/managed/execute"
---

# Quote, preview, and execute a managed crypto transfer

Base URL: `https://integration-api.meshconnect.com` (sandbox: `https://sandbox-integration-api.meshconnect.com`).
Authenticate with `X-Client-Id` and `X-Client-Secret`. A connected account is required
(see the connect-and-read-portfolio skill).

## Steps

1. **Discover support** — `GET /api/v1/transfers/managed/tokens` and
   `GET /api/v1/transfers/managed/networks` to confirm the token symbol and chain are
   supported. Pass the ticker **symbol**, never a contract address.
2. **Quote** — `POST /api/v1/transfers/managed/quote` to get min/max fees and amounts
   across funding sources.
3. **Preview** — `POST /api/v1/transfers/managed/preview` to validate the transfer
   parameters and surface the exact amounts before committing.
4. **Execute** — `POST /api/v1/transfers/managed/execute` to initiate the transfer.
5. **Reconcile via webhooks** — listen for transfer-status webhooks (`Pending`,
   `Succeeded`, `Failed`, `RefundPending`, `RefundSucceeded`). Verify the
   `X-Mesh-Signature-256` HMAC-SHA256 over the **raw** body, and deduplicate on
   `EventId` (delivery is at-least-once).

## Rules

- Verify webhook signatures over raw bytes — do not parse/re-serialize the JSON first.
- Whitelist Mesh's static webhook source IP `20.22.113.37`.
- A `Succeeded` webhook can take up to 24h for CEX transfers; amounts below the minimum
  or 2FA timeouts leave a transfer stuck pending.
- `403` = API key lacks write permission. Errors use the `ApiResult` envelope; see
  `errors/mesh-connect-problem-types.yml`.
