---
name: Log a user in and read their app achievements
description: Run SideQuest's device-style short-code OAuth login, then read a user's unlocked achievements for an app.
api: SideQuest Public API
base_url: https://api.sidequestvr.com
operations:
  - POST /v2/oauth/getshortcode
  - POST /v2/oauth/checkshortcode
  - POST /v2/oauth/token
  - GET /v2/users/{route_users_id}/apps/{apps_id}/achievements
auth: bearer (OAuth 2.0); scopes user.app_achievements.read
generated: '2026-07-21'
method: generated
---

# Log a user in and read their app achievements

Authenticate a user with SideQuest's short-code (device) login, then read their achievements. Grounded in operations verified in openapi/sidequest-openapi.json.

## Steps

1. **Request a short code.** `POST /v2/oauth/getshortcode` with your `client_id` (from the SideQuest app-management dashboard) and the `scopes` you need — at minimum `user.app_achievements.read`. The response returns `code`, `device_id`, `verification_url`, `interval`, and `expires_at`.
2. **Prompt the user.** Show them `code` and `verification_url` so they can approve access.
3. **Poll for approval.** `POST /v2/oauth/checkshortcode` with `device_id` and `code`, waiting `interval` seconds between calls. A `204` means "not yet approved — keep polling"; a `200` returns the access token, refresh token, and granted `scopes`. Stop when the code `expires_at` passes (HTTP 400 = expired/invalid).
4. **Call the API.** Send `Authorization: Bearer <access_token>`. Read achievements with `GET /v2/users/{route_users_id}/apps/{apps_id}/achievements` (supports `skip`, `limit`, `name`, `sortOn`, `descending`). Each achievement has `achievement_identifier`, `name`, `description`, `status`, `unlocked_at`.
5. **Refresh when expired.** `POST /v2/oauth/token` with `grant_type=refresh_token`, the `refresh_token`, and `client_id` to obtain a new access token.

## Rules

- A `403` means the token lacks the required scope — request `user.app_achievements.read` at getshortcode time. See scopes/sidequest-scopes.yml.
- A `404` can mask an existing resource the token cannot see; verify ids and scopes. Errors are plain text. See errors/sidequest-problem-types.yml.
- Access tokens carry `access_token_expires_at`; refresh proactively. See conventions/sidequest-conventions.yml.
