# Deep Spec Discovery: Real-Time Notifications System
Date: 2026-03-15

> This is an example Research Brief + Decision Log produced by the `/deep-spec` skill. Your output will vary based on your project, codebase, and answers.

---

## Research Brief

### What Exists in the Codebase
- **Polling-based notifications already exist** in `src/api/notifications.ts` — fetches every 30 seconds via `useQuery` with a refetch interval. Works but adds unnecessary API load.
- **WebSocket infrastructure is partially built** — `src/lib/ws.ts` has a `createSocket()` helper wrapping `socket.io-client`, but it's only used for the chat feature (`src/features/chat/`). The server-side handler is in `server/ws/chat.ts`.
- **Notification data model exists** — `prisma/schema.prisma` has a `Notification` model with `type`, `message`, `read`, `userId`, and `createdAt`. No `channel` or `priority` field.
- **Toast component exists** — `src/components/ui/Toast.tsx` using `react-hot-toast`. Currently only triggered by API error handlers, not notifications.
- **No service worker or push notification setup** — No `sw.js`, no push subscription, no `Notification.requestPermission()` calls.

### What the Industry Does
- **Most SaaS apps use a tiered approach:** WebSocket for in-app real-time, email digest for offline, push notifications for mobile. Linear, Notion, and Slack all follow this pattern.
- **Server-Sent Events (SSE) is gaining traction** over WebSockets for notification-only use cases — simpler protocol, auto-reconnect, works through more proxies. Vercel and Railway both recommend SSE for one-way real-time data.
- **Common pitfall: notification fatigue.** Every app that adds real-time notifications eventually needs a "notification preferences" UI. Building this retroactively is painful because it requires backfilling notification types.
- **Web Push API is well-supported** — 95%+ browser coverage. But the UX of requesting permission is tricky — best practice is to ask contextually ("Get notified when your deploy finishes?"), not on first page load.

### Key Constraints Discovered
- **Existing WebSocket server uses `socket.io`** — Adding a second real-time channel (SSE) would mean maintaining two real-time systems. Easier to extend the existing `socket.io` setup.
- **Prisma schema has no migration for notification channels** — Adding a `channel` field requires a migration. Low risk but needs to happen before any channel-based routing.
- **Current hosting (Vercel) has limitations for WebSockets** — Vercel serverless functions have a 30-second timeout. The chat WebSocket likely runs on a separate server. Need to confirm deployment topology.

### Open Questions Raised by Research
- Is the chat WebSocket running on a separate server from the API, or is there a long-running process somewhere?
- Should we extend `socket.io` for notifications, or is this a good time to evaluate SSE for the simpler notification use case?
- Does the team want notification preferences from day one, or is "all notifications on" acceptable for V1?

---

## Decisions Made
- **Extend existing `socket.io` infrastructure:** Don't introduce SSE alongside the existing WebSocket setup. One real-time transport is simpler to operate. Revisit SSE if we hit scaling issues.
- **In-app only for V1:** No email digest, no push notifications. Just real-time toasts + a notification bell with a dropdown.
- **Add notification types from the start:** Add a `type` enum to the schema now so we can build preferences later without migrating existing rows. Types: `mention`, `assignment`, `status_change`, `comment`, `system`.
- **No preference UI in V1, but wire the data model for it:** Store `type` on every notification so V2 preferences have clean data to work with.
- **Reuse the existing Toast component:** Don't build a custom notification UI. Toasts for ephemeral alerts, bell dropdown for persistent history.

## Still Open
- [ ] Confirm deployment topology — where does the WebSocket server run? Is it the same process as the API?
- [ ] Notification retention policy — how long do we keep read notifications? (Suggest 30 days)
- [ ] Should clicking a notification toast navigate to the relevant page? (Probably yes, but need to define deep links per type)

## Scope Summary
**Building:**
- WebSocket channel for notifications (extend existing `socket.io` setup)
- Notification bell UI with unread count badge
- Notification dropdown with mark-as-read
- Toast integration for real-time alerts
- `type` enum on notification schema (5 types)
- Server-side notification dispatch (pub to user's socket room)

**NOT Building:**
- Email digest or push notifications
- Notification preferences UI
- Notification grouping or batching
- Sound or desktop alerts
- Mobile push

**MVP vs V2:**
- V1: In-app real-time via WebSocket, toast + bell UI, typed notifications
- V2: Notification preferences, email digest, push notifications, deep linking, retention policy

## Architecture Direction
**Approach:** Extend existing `socket.io` infrastructure with a `notifications` namespace
**Existing code to extend:**
- `src/lib/ws.ts` — Add notification socket connection alongside chat
- `server/ws/chat.ts` → refactor to `server/ws/index.ts` with namespace routing
- `src/components/ui/Toast.tsx` — Wire to notification events
- `prisma/schema.prisma` — Add `type` enum, add `channel` field

**New code needed:**
- `server/ws/notifications.ts` — Server-side notification dispatch
- `src/features/notifications/` — Bell component, dropdown, hooks
- `src/hooks/useNotifications.ts` — Socket listener + state management

**Key patterns to follow:**
- Feature folder structure (`src/features/notifications/`)
- `useQuery` pattern for initial load, socket for real-time updates
- Prisma enum for notification types (matches existing pattern in `Role` enum)

## Key Risks Identified
- **WebSocket server topology unknown:** If the WS server is a separate process, we need to understand how it communicates with the API for dispatching notifications. Could be a blocking investigation.
- **Notification fatigue (from research):** Without preferences, power users in active teams could get flooded. Mitigation: Keep V1 notification types conservative (mentions and assignments only), expand in V2 with preferences.
- **Socket reconnection:** If a user's connection drops and reconnects, they could miss notifications. Mitigation: Always fetch unread notifications on reconnect (hybrid polling + socket pattern).

## Technical Concepts Discussed
- **WebSocket namespace:** A way to multiplex different real-time features over a single connection. Like having different channels on the same radio frequency.
- **SSE (Server-Sent Events):** A simpler alternative to WebSockets for one-way data (server to client). Decided against it to avoid maintaining two real-time transports.
- **Notification fatigue:** When users get so many notifications they start ignoring all of them. A design problem, not a technical one.

## Next Step
→ Investigate WebSocket deployment topology (blocking question)
→ Then ready to generate a PRD or start implementation planning
