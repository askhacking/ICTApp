# V2.5 Backend Requirements — Notification Center & Event Report

Status: **frontend draft; backend integration is not complete**.

The repository contains the frontend HTML and calls to existing Supabase RPC functions, but the database migration/function definitions and Worker source are not present in this repository. Do not deploy the new notification UI as production-ready until the contract below is implemented and tested in the current Supabase environment.

## 1. Notification RPC contract

All RPCs must derive the authenticated user from the verified Supabase session. Never trust a recipient ID supplied by the browser.

### `list_notifications(p_device text, p_limit integer default 50, p_category text default null)`

Return a list ordered by `created_at DESC`, limited to a safe maximum (for example 100), containing only the current user's notifications.

Expected row fields:
- `id`: stable notification ID
- `type`: `registration | event | course | grade`
- `title`, `message`
- `reference_type`, `reference_id`
- `target_path` (optional; must be a known internal route)
- `is_read`
- `created_at`

The frontend accepts either an array or an object with an `items` array.

### `mark_notification_read(p_device text, p_notification_id bigint)`

Marks one notification as read only when it belongs to the authenticated user. Set `read_at` if the schema includes it. Return success/failure without disclosing whether another user's notification exists.

### `mark_all_notifications_read(p_device text)`

Marks all unread notifications belonging to the authenticated user as read.

### Existing badge counts

Update the existing `get_state` response so `S.notif` is the total unread notification count for the current user. Keep `S.notifEvt` only if existing navigation still needs a separate event-approval count; do not use it as a substitute for the full notification count.

## 2. Suggested data model

Map this to the actual database before creating anything. Reuse existing tables if a suitable notification store already exists.

Suggested `notifications` fields:
- `id` (identity/bigint or UUID primary key)
- `recipient_user_id` (foreign key to the real user identity)
- `type` (validated category)
- `title`, `message`
- `reference_type`, `reference_id` (nullable)
- `target_path` (nullable)
- `is_read` (not null, default false)
- `created_at` (not null, default now())
- `read_at` (nullable)
- `dedupe_key` (nullable unique key for idempotency)

Add an index for unread notification lookup by recipient and creation time. If using `dedupe_key`, enforce uniqueness at the database level for the intended scope.

## 3. Notification trigger matrix

| Business event | Recipient | When to create |
|---|---|---|
| New member registration | Authorized reviewers | After the registration is successfully stored |
| Registration status changed | Applicant | After approval/rejection/activation is committed |
| Event submission | Authorized event reviewer | After submission is stored |
| Event status changed | Event applicant | After review status is committed |
| Event published/meaningfully changed | Relevant participants only, if product rules require it | After publication/update is committed |
| Course published | Eligible learners | When course transitions to published/approved |
| Assignment published | Enrolled learners | When assignment becomes visible |
| Grade published | The learner who owns that submission | Only when grade is explicitly published, not while draft/saving |

Generate notifications server-side after the business state change succeeds. Retries must be idempotent. Do not generate a notification only from a client-side click handler.

## 4. Security requirements

- Apply RLS to the notification table, if exposed through PostgREST.
- Users may select only their own notifications and update only their own read state.
- Server-side functions must check the current identity and relevant role before creating or reading notifications.
- Never expose a Supabase service-role secret in the browser.
- Validate referenced objects and access rights again when opening a notification.
- Do not include sensitive grade values or private personal information in notification messages.
- Test direct RPC calls, changed IDs, unauthorized roles, and cross-user access.

## 5. Event Report

The current frontend report is based on the existing `event_detail(p_id)` response and its `members` list. It summarizes the available member status/role and exports CSV.

Before production:
- Confirm the existing `event_detail` response contains complete and correctly authorized event member records.
- Enforce report access in the database/server, not only by hiding the UI for participants.
- Distinguish “registered/selected” from actual attendance. The current report does not claim attendance unless a verified attendance field is available.
- If the event report needs full application submissions, attendance records, or review history beyond `members`, add a dedicated server-side report RPC after auditing the real schema.
- Verify CSV escaping and prevent spreadsheet formula injection.

## 6. Release gates

Do not merge/release the feature as complete until:
1. Database schema and RPCs are implemented in the existing Supabase project.
2. `get_state` returns the correct unread count.
3. Registration, event, course and grade publication flows create the correct notifications.
4. Duplicate delivery, RLS, role access, and stale/deleted references are tested.
5. The frontend's notification list, mark-read and mark-all-read operations work against the real backend.
6. Event report data matches the authoritative event records.
