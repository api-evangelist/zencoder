---
name: Cancel or resubmit an encoding job
description: Look up a Zencoder job's state and either cancel an in-flight job or resubmit one that has not finished.
api: openapi/zencoder-openapi-original.yml
operations: [getJobs, getJob, cancelJob, resubmitJob]
---

# Cancel or resubmit an encoding job

Authenticate with the `Zencoder-Api-Key` header. Base URL `https://app.zencoder.com/api/v2`.

## Steps

1. **Find the job** — `GET /jobs` (`getJobs`, paginated `page`/`per_page`, max 50, newest first) or
   `GET /jobs/{job_id}` (`getJob`) to read the current state.
2. **Cancel** — `PUT /jobs/{job_id}/cancel` (`cancelJob`) to stop a job that is not yet finished.
3. **Resubmit** — `PUT /jobs/{job_id}/resubmit` (`resubmitJob`) to re-run a job that is not in the
   `finished` state; a successful resubmit returns `204 No Content`.

## Rules

- Cancelling or resubmitting a **finished** job returns `409 Conflict` — check state first via `getJob`.
- `403` = invalid/insufficient key. `500` = retry with backoff. See `errors/zencoder-problem-types.yml`.
- These state transitions are effectively idempotent by job state; there is no Idempotency-Key header.
