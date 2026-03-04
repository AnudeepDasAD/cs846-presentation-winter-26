<!-- Simulated PR description for Problem B -->

# Add user display lookup for dashboard header

---

## Overview

The dashboard header currently displays a placeholder value instead of the logged-in user's name.

This PR integrates the external User API to retrieve and display:

- The user’s full name
- The user’s role (if present)

Example display:

Jane Doe (admin)

Additionally, this PR introduces a server-side helper for resolving users by email for use in the internal support tool.

---

## Changes Introduced

### `user_helpers.py` (new backend module)

Added the following helper functions:

- `get_user_display(user_id)`
  - Fetches name and email for a given user ID.

- `get_user_list(role=None)`
  - Retrieves all users.
  - Optional role filter (e.g., `admin`, `support`).

- `format_user_for_header(user_id)`
  - Returns formatted display string:
    - `"Name"`
    - `"Name (role)"`

- `lookup_by_email(email)`
  - Returns user ID for a given email.
  - Used by the internal support tool only.
  - Not exposed to the front end.

---

## Constraints & Requirements

- Do **not expose internal user IDs** (UUIDs, PKs) to the front end.

- All external API calls must:
  - Timeout after **2 seconds**
  - Avoid blocking the dashboard render
  - Fail gracefully if the external API is unavailable or malformed

- Keep the dashboard responsive:
  - Avoid redundant or unnecessary API calls in hot paths.
  - The dashboard header is rendered on **every page load**.
  - It must not make multiple network calls per request.

- Authentication and input validation are handled upstream (API gateway).

---

## Out of Scope

- Refactoring unrelated modules
- Changing the external User API contract
- Modifying authentication/session logic
- Style-only formatting changes (unless correctness-related)

---

## Risk & Impact

- Introduces dependency on external User API in dashboard render path.
- This code will be deployed to production this week.
- Any latency or failure in the external API may affect all users.
- Must ensure timeout handling and graceful degradation.