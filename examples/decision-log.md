# Spec Discovery: Team Invite System
Date: 2026-03-15

> This is an example Decision Log produced by the `/spec` skill. Your output will vary based on your project and answers.

## Decisions Made
- **Email-based invites only (no link sharing):** Reduces abuse surface. Link sharing deferred to V2 after we see adoption.
- **Inviter must be an admin:** Regular members can't invite. Keeps teams intentional, avoids sprawl.
- **Invites expire after 7 days:** Prevents stale invites from cluttering the system. Inviter gets notified on expiry.
- **No seat limit enforcement in V1:** We'll track seat count but won't block invites. Billing enforcement comes later.
- **Invited user sees a dedicated landing page:** Not just a login redirect. Shows who invited them, what team, and a clear "Join" CTA.

## Still Open
- [ ] What happens if an invited user already has an account on a different team? (Multi-team support is not scoped yet)
- [ ] Should we send a reminder email at day 5 if the invite is unclaimed?
- [ ] Rate limiting on invites — how many per admin per day?

## Scope Summary
**Building:**
- Invite creation (admin action, email input, optional personal message)
- Email delivery with branded invite template
- Invite acceptance flow (landing page, account creation or login, team join)
- Invite management UI (admin view: pending, accepted, expired, revoke)
- Invite expiry (7-day TTL, auto-cleanup)

**NOT Building:**
- Shareable invite links
- Bulk CSV import
- Role assignment at invite time (all invitees join as "member")
- Billing/seat enforcement
- SSO/SAML auto-provisioning

**MVP vs V2:**
- V1: Email invites, admin-only, 7-day expiry, basic management UI
- V2: Link sharing, role assignment, bulk import, billing enforcement, reminder emails

## Key Risks Identified
- **Email deliverability:** Invite emails hitting spam would silently kill the feature. Mitigation: use a verified sending domain, test with major providers before launch.
- **Race condition on team join:** If two people accept invites at the same time, could we double-count seats? Mitigation: use a database transaction for the join operation.
- **Invite enumeration:** Could an attacker probe the invite endpoint to discover valid emails? Mitigation: rate limit the endpoint, don't confirm whether an email exists.

## Technical Concepts Discussed
- **TTL (Time-To-Live):** A timer on data that auto-deletes it after a set period. Used here for 7-day invite expiry.
- **Race condition:** When two operations happen simultaneously and conflict. Relevant for the team join flow.
- **Invite enumeration:** An attack where someone probes an endpoint to discover valid data. Relevant for the invite acceptance URL.

## Next Step
→ Ready to generate a PRD or start implementation planning
