---
name: chamber-scheduling
description: 'Use when implementing or reviewing temperature chamber availability, daily booking slots from 9:00 AM to 6:00 PM, multi-day reservations, early release, booked-by display, preferred-time requests, or transfer of an occupied chamber slot.'
---

# Temperature chamber scheduling

A chamber is booked for specific times, not locked for the whole day just because one slot is occupied. Follow [Equipment booking](../equipment-booking/SKILL.md) for the shared catalog, identity, notification, and verified email approval flow. This skill controls chamber availability and what time a handoff transfers.

## 1. Represent availability (backend)

- Identify each chamber by its catalog asset identifier. Represent reservations as dated time intervals in the lab's configured timezone. Enforce the local daily window: starts at or after 09:00, ends at or before 18:00, and end is after start. Treat the end as exclusive so that 10:00-11:00 and 11:00-12:00 do not overlap.
- Support a single-day slot and booking across multiple days in one selection. Represent the multi-day booking as a group of daily intervals, not a continuous overnight lock. Validate every selected interval; do not silently skip a conflicting day or save a partial booking.
- Check overlaps per chamber against all current reservations and enforce the check atomically on booking and transfer. Pending requests do not reserve time. At the scheduled end, the reservation is no longer active; early release frees only the remaining time of the released interval. Preserve other dates or slots unless the user explicitly selects them for release.
- Confirm slot granularity and lab timezone before implementing if not configured. Do not infer either from the developer machine or hardcode an unconfirmed minimum duration or booking horizon.

## 2. Present actions by selected time (frontend)

- Show chambers within the category card flow. Let signed-in users choose a date or date range and a time window within 09:00-18:00. Display availability per day and the holder's display name and booked time for each occupied interval, including today's slots.
- For a free selection, show **Book**. For the user's own reservation, show **Release**. For an interval held by another associate, hide **Book** and show **Request**. Keep non-overlapping free time bookable on the same chamber. For a mixed selection, show which days or times conflict and let the user adjust or target an occupied slot for a request.
- Requesting lets the user choose a preferred date and time within the targeted occupied reservation. Include the chamber and exact requested date and time in the email. Do not substitute the holder's full reservation for the requested interval.

## 3. Process release and handoff (backend)

- Verify the authenticated holder and the selected interval on release. Keep elapsed time in booking history and return updated availability to the frontend.
- On a verified approval reply, atomically recheck that the approved interval is still held by the approving associate and is eligible for transfer. Transfer only the requested time and preserve any unrequested parts of the holder's reservation. For example, transferring 11:00-12:00 from a 10:00-14:00 booking keeps 10:00-11:00 and 12:00-14:00 with the original holder. Do not change another holder's reservation or transfer elapsed time.
- If a preferred window spans separate bookings or different holders, do not treat one reply as approval for the entire window. Keep each request tied to the reservation and holder it targets; require separate approvals for separate bookings.

## Acceptance checks

- Accept the 09:00 and 18:00 boundaries as start and end respectively; reject out-of-hours and zero-length intervals. Adjacent slots can both book, overlapping concurrent requests to book cannot, and a pending handoff request does not reserve time.
- A multi-day selection stores only the requested daily intervals, does not reserve overnight, and fails without partial writes if one day conflicts. Two users see the same holder and slot from server state.
- Early release makes the remaining selected slot bookable and leaves other dates untouched. An approved partial transfer changes only the requested time; stale or duplicate replies do not alter any other slot.
