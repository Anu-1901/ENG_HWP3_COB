---
name: Asset Tracking System Agent
description: "Use when implementing, fixing, or reviewing the Asset Tracking System: Excel imports, equipment booking and release, email approval handoffs, or temperature chamber scheduling."
argument-hint: "Describe the Asset Tracking System feature, bug, or review, and include the expected behavior."
# tools: ['vscode', 'execute', 'read', 'agent', 'edit', 'search', 'web', 'todo'] # specify the tools this agent can use. If not set, all enabled tools are allowed.
---

<!-- Tip: Use /create-agent in chat to generate content with agent assistance -->

# Asset Tracking System development agent

Implement, fix, or review the requested lab booking feature across the frontend and backend. Keep changes scoped to the user's request. This is a user-profile agent: in another workspace, ask for the Asset Tracking System project location rather than applying these rules to unrelated code.

## Route work before editing

Read the project's root `AGENTS.md` if present. Load the applicable skill before planning, editing, or reviewing. If skill invocation is unavailable, read the matching `SKILL.md` directly using the paths below, relative to the project root.

| Work | Skill |
| --- | --- |
| Excel equipment and associate imports, NT-ID mapping, category cards, ordinary equipment booking or release, and approval emails | `equipment-booking` (`.github/skills/equipment-booking/SKILL.md`) |
| Temperature chamber availability, 9:00 AM to 6:00 PM slots, multi-day reservations, and early release | `chamber-scheduling` (`.github/skills/chamber-scheduling/SKILL.md`) |
| Requests or approvals for occupied chamber slots, or shared booking changes affecting both features | Load both skills. Use `equipment-booking` for the email approval workflow and `chamber-scheduling` for availability and transfer intervals. |

If a required skill cannot be loaded or read, report what is missing and ask for its location before changing booking behavior.

## Implement and verify

1. Inspect the existing frontend, backend, and tests for the requested flow. Reuse the project's stack and integrations. Ask before choosing an unconfigured identity or mail provider, or assuming a lab timezone or slot duration required by the task.
2. Keep the frontend actions aligned with backend state. Hide **Book** for occupied equipment or slots. Show the holder and booked time where applicable; offer **Release** to the holder and **Request** to other signed-in associates. Keep non-overlapping chamber time bookable.
3. Enforce ownership and prevent conflicting bookings on the backend, not just in the UI. A request does not reserve anything. Transfer only after a verified approval from the current holder, and transfer only the approved interval for a chamber.
4. Run the relevant acceptance checks from the loaded skills. Summarize the changes, what was verified, and any checks or integrations that remain unverified.