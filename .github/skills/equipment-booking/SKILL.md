---
name: equipment-booking
description: 'Use when implementing or reviewing lab equipment category cards, EQUIPMENT DETAILS and ASSOCIATE DETAILS Excel imports, NT-ID mapping, Book and Release actions, Request emails, holder approval replies, and booking transfers. Also defines shared email approval behavior for chamber slot requests.'
---

# Equipment booking and email handoff

Use this workflow for ordinary lab equipment. For chamber requests, also load [Chamber scheduling](../chamber-scheduling/SKILL.md) and apply its interval rules instead of treating the whole asset as occupied.

## 1. Import and identity (backend)

- Map `EQUIPMENT DETAILS` to category, asset name, and asset number. Map `ASSOCIATE DETAILS` to NT-ID and display name. Inspect actual workbook headers before implementing; do not assume the worksheets are in the same file.
- Treat asset numbers and NT-IDs as identifiers, not numeric quantities. Use the asset number as the equipment identifier if the workbook guarantees uniqueness; otherwise confirm the identity rule before importing. Validate missing fields and duplicates, report affected rows, and do not silently overwrite records or recreate live bookings on reimport.
- Map authenticated users to associates by NT-ID. NT-ID and display name alone do not provide an email address. Resolve addresses through an approved directory or verified mapping; do not guess an email domain.

## 2. Book, release, or request (frontend and backend)

Selecting a category expands its corresponding equipment cards. Each card shows the asset name and number and the current booking status. While booked, show the holder's display name and when the current booking started.

| Server state for ordinary equipment | Card action |
| --- | --- |
| Available | **Book** |
| Booked by the signed-in user | **Release**, never **Book** |
| Booked by someone else | **Request**, never **Book** or **Release** |

- Allow at most one active booking per ordinary asset. It stays booked until the holder releases it or a valid approval transfers it. Do not add a timed expiry to ordinary equipment.
- A request is not a booking. Save it against the current booking and requester, then notify the current holder by email. Show whether the request is pending or delivery failed; do not claim mail was sent if submission or delivery fails. Handle retries without creating duplicate requests or notifications.
- Refresh the card with authoritative state after actions. If another user books or releases the item first, explain the conflict and show the available next action.

## 3. Approve by reply and transfer (shared backend workflow)

1. Correlate the inbound email with one pending request and its target booking using a request-specific identifier. Use a trusted mail integration and verify the replying identity is the authorized current holder; a display name, quoted text, or the bare word `Approved` without verified identity and correlation is not enough.
2. Recognize an unambiguous affirmative reply, such as `Approved` in the newly written reply body. Ignore quoted history and automatic replies. Do not transfer for ambiguous, unauthorized, stale, or already processed replies.
3. In one transaction, recheck the request, holder, and availability constraints; end the old ordinary-equipment booking, start the requester's booking, and record the approval. There must be no intermediate state where a third user can book the asset. For a chamber, transfer only the approved interval(s) according to its skill. If the underlying booking changed before approval, do not overwrite a newer booking; surface the conflict.
4. Make reply processing idempotent and retain a decision and handoff audit trail. Notify the requester and previous holder of the outcome through the configured integration without undoing an already committed transfer if a notification fails.

## Acceptance checks

- Selecting a category shows only its equipment. Import preserves identifiers, reports invalid rows, and does not reset existing bookings.
- Two users booking the same asset cannot both succeed. On a subsequent sign-in, a different user sees the current holder and **Request**, not **Book**. Only the holder can release, and release makes the asset bookable again.
- Requesting sends mail to the holder without changing ownership. An authorized `Approved` reply transfers ownership exactly once; unrelated, forged, duplicate, and stale replies cannot cause an extra transfer. Simulate a mail failure and verify the UI does not report success.
