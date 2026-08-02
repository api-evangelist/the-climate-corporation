---
name: Upload agronomic data to FieldView
description: Start an upload, send data chunks, and poll until the asynchronous ingest completes.
api: openapi/the-climate-corporation-platform-openapi-original.yml
operations: [postUpload, chunkedUpload, fetchUploadStatusById]
scopes: [asPlanted:write, asHarvested:write, asApplied:write, imagery:write, rx:write, soil:write]
---

# Upload agronomic data to FieldView

Ingest as-planted / as-harvested / as-applied data, imagery, seeding prescriptions (rx), or soil sample results into a grower's account. Uploads are asynchronous.

## Prerequisites
- Bearer access_token with the write scope matching your data type (e.g. `asApplied:write`) plus `X-Api-Key`.
- Base URL `https://platform.climate.com`. See `sandbox/` for downloadable sample datasets to test with.

## Steps
1. **Begin the upload** — `POST /v4/uploads` (`postUpload`) with the file metadata. The response returns an `uploadId`.
2. **Send content** — `PUT /v4/uploads/{uploadId}` (`chunkedUpload`) to stream the data content (chunked).
3. **Poll status** — `GET /v4/uploads/{uploadId}/status` (`fetchUploadStatusById`) until the upload reports a terminal state (completed or failed).

## Rules
- There is no idempotency-key; do not blindly re-POST `/v4/uploads` on a network error — first poll status to see whether the prior upload already registered.
- Match the scope to the data: seeding prescriptions need `rx:write`, soil results need `soil:write`, imagery needs `imagery:write`.
- Honor `429` + `Retry-After`. Capture `X-Http-Request-Id` for support.
- See `conventions/` (uploads/pagination) and `errors/` (status map) for details.
