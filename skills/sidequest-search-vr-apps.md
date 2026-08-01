---
name: Search the SideQuest VR app catalog
description: Browse and filter SideQuest's VR app/game catalog for Meta Quest, PCVR, Pico, Magic Leap and WebXR.
api: SideQuest Public API
base_url: https://api.sidequestvr.com
operations:
  - GET /v2/apps
auth: none (public read)
generated: '2026-07-21'
method: generated
---

# Search the SideQuest VR app catalog

Retrieve and filter the SideQuest app catalog. This is a public, unauthenticated read.

## Steps

1. Call `GET /v2/apps` on `https://api.sidequestvr.com`. No Authorization header is required.
2. Narrow results with query parameters (all optional):
   - Platform: `supports_quest`, `supports_pico`, `supports_magic_leap`, `supports_link`, `is_webxr`
   - Discovery: `staff_picks`, `early_access`, `has_oculus_url`, `app_categories`, `app_comfort_level`, `app_license`, `app_download_method`
   - Free text: `search` or `full_search`
   - Specific apps: `app_ids`
   - Incremental sync: `updated_since`, `created_since`
3. Page with `skip` and `limit` (offset pagination). Sort with `sortOn` plus `descending`.
4. Read from each app object: `apps_id`, `name`, `summary`, `image_url`, platform `supports_*` flags, `downloads`, `sort_rating`, `oculus_url`.

## Rules

- Errors are plain text (`text/plain`), not JSON — handle HTTP 400 (bad parameters), 403, 404 by reading the message body. See errors/sidequest-problem-types.yml.
- For a full nightly mirror, page with `skip`/`limit` and use `updated_since` to pull only changed listings. See conventions/sidequest-conventions.yml.
