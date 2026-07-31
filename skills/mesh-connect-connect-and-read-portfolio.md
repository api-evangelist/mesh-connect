---
name: Connect an account and read its portfolio
description: Link an end-user's exchange/wallet account via the Mesh SDK, then read holdings and balances.
api: openapi/mesh-connect-openapi-original.json
operations:
  - "POST /api/v1/linktoken"
  - "GET /api/v1/holdings/portfolio"
  - "POST /api/v1/balance/get"
---

# Connect an account and read its portfolio

Base URL: `https://integration-api.meshconnect.com` (sandbox: `https://sandbox-integration-api.meshconnect.com`).
Authenticate every request with the headers `X-Client-Id` and `X-Client-Secret`.

## Steps

1. **Mint a Link token** — `POST /api/v1/linktoken` with the user id and desired
   integration filters. The response returns a short-lived, single-use Link token
   (10-minute TTL). Never expose your `X-Client-Secret` to the browser; mint the
   token server-side.
2. **Launch the Mesh SDK** — hand the Link token to the client-side SDK
   (`@meshconnect/web-link-sdk` `openLink()`, or the React Native / Flutter / iOS /
   Android SDK). Handle the `onIntegrationConnected` callback to capture the
   resulting connection/auth token; handle `onExit` for cancellation.
3. **Read the portfolio** — `GET /api/v1/holdings/portfolio` for aggregated market
   values, or `POST /api/v1/holdings/get` for real-time holdings from the
   underlying integration.
4. **Read balances** — `POST /api/v1/balance/get` for real-time fiat balances, or
   `GET /api/v1/balance/portfolio` for cached aggregated balances.

## Rules

- Link tokens are single-use and expire after 10 minutes; mint a fresh one per session.
- Allowlist your domain and add `*.meshconnect.com` to CSP `frame-src`/`connect-src`
  or the SDK modal renders as a blank grey box.
- Errors use an `ApiResult` JSON envelope; `401` = bad Client Id/Secret, `403` =
  API key lacks read permission. See `errors/mesh-connect-problem-types.yml`.
