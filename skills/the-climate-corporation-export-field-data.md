---
name: Export FieldView data
description: Request a data export, poll for completion, and download the contents.
api: openapi/the-climate-corporation-platform-openapi-original.yml
operations: [postExport, fetchExportStatusById, fetchExportContentsById]
scopes: [exports:read]
---

# Export FieldView data

Pull a grower's data back out of FieldView as an asynchronous export job.

## Prerequisites
- Bearer access_token with scope `exports:read` plus `X-Api-Key`.
- Base URL `https://platform.climate.com`.

## Steps
1. **Request the export** — `POST /v4/exports` (`postExport`) describing what to export. The response returns an `exportId`.
2. **Poll status** — `GET /v4/exports/{exportId}/status` (`fetchExportStatusById`) until the job is ready.
3. **Download contents** — `GET /v4/exports/{exportId}/contents` (`fetchExportContentsById`) to retrieve the generated file(s).

## Rules
- Exports are asynchronous — poll status rather than assuming immediate availability.
- Honor `429` + `Retry-After`; capture `X-Http-Request-Id`.
- See `conventions/` and `errors/` for pagination and the status map.
