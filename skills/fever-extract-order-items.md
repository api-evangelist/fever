---
name: Extract Fever order-item sales data
description: >-
  Authenticate to the Fever Reporting API and pull a partner's order-item level
  sales data for a date range, using the asynchronous submit-then-poll search
  pattern and partition-based pagination.
api: openapi/fever-reporting-api-openapi-original.json
operations:
- create_token_auth_token_post
- search_order_items_reports_order_items_search_post
- get_order_items_search_page_reports_order_items_search__search_id__get
---

# Extract Fever order-item sales data

Use this to load Fever event sales into a CRM/BI/warehouse. This API is for
reporting only — never for real-time access control or live inventory.

## 1. Authenticate
Call `create_token_auth_token_post` (`POST /v1/auth/token`) with your Fever-issued
`username` and `password` (form-encoded). Read `access_token` from the response and
send it as `Authorization: Bearer <access_token>` on every subsequent call.
Auth endpoints are limited to 20 requests/minute.

## 2. Submit the search
Call `search_order_items_reports_order_items_search_post`
(`POST /v1/reports/order-items/search`) with a JSON body specifying at least a
`date_field` (e.g. `CREATED_DATE_UTC`), `date_from`, and `date_to`. A `202`
response means the search is running; capture the returned `search_id`.

## 3. Poll until ready
Call `get_order_items_search_page_reports_order_items_search__search_id__get`
(`GET /v1/reports/order-items/search/{search_id}`). Keep polling until it returns
`200`. A `410` means the `search_id` expired — go back to step 2. A `408` means the
query timed out server-side.

## 4. Paginate across partitions
On the `200`, read `data` (page 0 rows) and `partition_info[]`. For each partition
with `partition_num > 0`, call the same GET with `?page=<partition_num>` and append
its `data`. Total rows should equal the sum of `partition_info[].rows`.

## Rules
- Default (non-auth) endpoints allow 200 requests/minute; back off on `429`.
- Errors use a FastAPI `detail` envelope (see errors/fever-problem-types.yml), not RFC 9457.
- No idempotency key is needed — all Reporting API operations are read-only.
