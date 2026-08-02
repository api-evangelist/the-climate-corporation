---
name: List a grower's fields and boundaries
description: Authenticate with Climate FieldView and retrieve a grower's fields and their GeoJSON boundaries.
api: openapi/the-climate-corporation-platform-openapi-original.yml
operations: [fetchFields, fetchFieldById, fetchBoundaryById]
scopes: [fields:read]
---

# List a grower's fields and boundaries

Use this to read the fields a FieldView grower has authorized your app to see, and pull each field's boundary geometry.

## Prerequisites
- OAuth2 client_id / client_secret and an X-Api-Key issued by Climate during onboarding.
- A grower access_token obtained via the authorization_code flow with scope `fields:read` (see `authentication/`). Send `Authorization: Bearer $access_token` AND `X-Api-Key: <your-key>` on every call.
- Base URL `https://platform.climate.com`.

## Steps
1. **List fields** — `GET /v4/fields` (`fetchFields`). Optionally filter with the `fieldName` query parameter. Results are paginated via headers: pass `X-Limit`, and follow `X-Next-Token` while the response is `206`; stop on `200` or `304` (no results).
2. **Get one field** — `GET /v4/fields/{fieldId}` (`fetchFieldById`) for full detail on a field of interest.
3. **Get the boundary** — `GET /v4/boundaries/{boundaryId}` (`fetchBoundaryById`) using the boundary id referenced by the field, to retrieve the GeoJSON geometry.

## Rules
- On `401 {"message":"Unauthorized"}` refresh the access token (refresh tokens are single-use, 30-day). On `403 {"message":"Forbidden"}` your `X-Api-Key` is missing/invalid.
- On `429` back off and honor `Retry-After` (per-partner rate limit / monthly quota).
- Record the `X-Http-Request-Id` response header for support triage.
- See `conventions/` for pagination and `errors/` for the full status map.
