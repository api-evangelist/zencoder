---
name: Pull Zencoder usage and minutes reports
description: Read account details and retrieve VOD usage and encoded-minutes reports for billing and monitoring.
api: openapi/zencoder-openapi-original.yml
operations: [getAccountDetails, getUsageReport, getMinutesUsed]
---

# Pull Zencoder usage and minutes reports

Authenticate with the `Zencoder-Api-Key` header. Base URL `https://app.zencoder.com/api/v2`.
A read-only key is sufficient for all steps here.

## Steps

1. **Account context** — `GET /account` (`getAccountDetails`) for plan and integration-mode status.
2. **VOD usage** — `GET /reports/vod` (`getUsageReport`) for on-demand encoding usage over a period.
3. **Minutes used** — `GET /reports/minutes` (`getMinutesUsed`) for total encoded output minutes
   (the metered/billed unit).

## Rules

- `403` = invalid/insufficient key; `500` = retry with backoff (`errors/zencoder-problem-types.yml`).
- Zencoder is billed per output minute; there are no documented request rate limits
  (`conventions/zencoder-conventions.yml`).
- To manage test vs live billing, toggle integration mode with `turnOnIntegration` /
  `turnOffIntegration` (`sandbox/zencoder-sandbox.yml`).
