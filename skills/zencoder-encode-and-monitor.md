---
name: Encode a video and monitor it to completion
description: Submit a source video to Zencoder, produce one or more outputs, and track the job to a terminal state via progress polling or completion webhooks.
api: openapi/zencoder-openapi-original.yml
operations: [createJob, getJobProgress, getJob, getOutputDetails]
---

# Encode a video and monitor it to completion

Use the Zencoder v2 API (`https://app.zencoder.com/api/v2`). Authenticate every request with the
`Zencoder-Api-Key` header (or `api_key` query param). See `authentication/zencoder-authentication.yml`.

## Steps

1. **Create the job** — `POST /jobs` (`createJob`). Body must include the source `input` URL; optionally
   include an `outputs` array (format, codec, destination, label) and a `notifications` array for
   webhooks. To dry-run without processing or billing, use an integration-mode key
   (`sandbox/zencoder-sandbox.yml`). Response returns the job `id` and per-output ids.
2. **Track progress** — poll `GET /jobs/{job_id}/progress` (`getJobProgress`) for overall state, or set
   `notifications` so Zencoder POSTs `job.finished` / `job.failed` / `job.cancelled` to your URL
   (`asyncapi/zencoder-notifications-webhooks.yml`). Prefer webhooks; poll as a fallback.
3. **Fetch results** — when finished, `GET /jobs/{job_id}` (`getJob`) for the full job, or
   `GET /outputs/{output_id}` (`getOutputDetails`) for each output file's URL and metadata.

## Rules

- A `403` means an invalid/insufficient key (e.g. a read-only key on a write). `422` means invalid
  settings. `500` is retryable with backoff. See `errors/zencoder-problem-types.yml`.
- Job-level encoding failures surface a code from `errors/zencoder-error-codes.yml` (e.g.
  `DownloadFailureError`, `UnsupportedCodecError`) — do not retry those without fixing the input/settings.
- No idempotency key exists; de-duplicate on your own identifier before re-POSTing (`conventions/zencoder-conventions.yml`).
- Historical job data is retained only 60 days — persist results you need long-term.
