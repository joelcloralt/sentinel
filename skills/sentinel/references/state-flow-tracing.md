# State Flow Tracing

This is the most critical analytical technique Sentinel uses. Many bugs survive fixes because the fix addresses where a problem is *observed* without tracing all the paths that *produce* the state.

## The Core Principle

For every piece of state a task touches, you must map the complete lifecycle: every place it's written, every place it's read, and every user journey that exercises each path. A fix that doesn't account for all write paths will break in production under a user journey the developer didn't test.

## The Five Questions

For every piece of state the task touches, answer:

### 1. Where is it read?

List every screen, component, API response, or process that consumes this value. **Search the codebase — don't guess.** A subscription status might be read by:
- The settings page
- The paywall / upgrade modal
- A middleware route guard
- An API endpoint's authorization check
- An analytics event payload
- A push notification targeting query
- A feature gate / entitlement check

### 2. Where is it written?

List every code path that creates, updates, or invalidates this value. This includes:
- **Obvious paths**: API calls, form submissions, database updates
- **Easy-to-miss paths**: Webhooks, onboarding flows, restore/sync processes, background jobs, cache invalidation, session mutations, OAuth callbacks, deep link handlers

### 3. Are all write paths consistent?

If a value is updated in one write path but not another, you have a **state gap**. 

Real example: A settings page reads `subscriptionStatus` from the user session. The Stripe webhook updates the database correctly. But the onboarding paywall — after a successful in-app purchase — navigates to the next screen without updating `session.user.subscriptionStatus`. Result: the database says "active" but the session says "none" until the user force-quits and re-launches.

The fix isn't to make settings read from the database — that might work for settings but doesn't fix analytics events that also read from the session. The fix is to ensure every write path that changes subscription state also updates the session, OR to refactor all read paths to use a single, always-fresh source.

### 4. What triggers each write?

Map the user journeys and system events that lead to each write path:
- First-time purchase (during onboarding)
- First-time purchase (from settings or paywall after onboarding)
- Plan upgrade / downgrade
- Subscription renewal (background, via webhook)
- Subscription cancellation
- Subscription expiry
- Restore purchases (new device, reinstall)
- Manual admin action
- Webhook retry / delayed delivery
- App launch / session initialization

Each of these is a test case. If you can't confirm that a specific journey correctly writes the state, flag it.

### 5. What's the source of truth?

Identify where the canonical state lives:
- Database (your Prisma models)
- External service (Stripe, RevenueCat, Clerk)
- Client-side session / cache
- Local state (React state, AsyncStorage)

Then verify that all read paths ultimately resolve to this source. If different parts of the app trust different sources (one reads from session, another from database, another from Stripe directly), that's a **consistency risk** that must be resolved.

## Format

Document the trace in this format:

```
State: [name of the state value]
Source of truth: [where the canonical value lives]

WRITE paths:
- [Path 1] → [what it updates] ✅ (verified)
- [Path 2] → [what it updates] ⚠️ CHECK THIS
- [Path 3] → [what it updates] ❌ DOES NOT UPDATE [target]

READ paths:
- [Consumer 1] → reads from [source]
- [Consumer 2] → reads from [source]
- [Consumer 3] → reads from [different source] ⚠️ INCONSISTENT

GAPS:
- [Path 2] writes to DB but not session → [Consumer 1] will show stale data
- [Consumer 3] reads from [source A] while everything else reads from [source B]

RECOMMENDATION:
- [Specific fix that addresses ALL gaps, not just the reported symptom]
```

## When to Trace

- **Bug fixes**: ALWAYS trace. The bug you're fixing is the symptom. The state flow reveals the disease.
- **Features that add new state**: Trace the new state's full lifecycle from the start.
- **Features that modify existing state**: Trace the existing state to understand what you're affecting.
- **Refactors that move state**: Trace before AND after to ensure nothing breaks.
- **"Simple" UI fixes**: If the UI reads from state, trace where that state comes from. What looks like a display bug is often a write-path bug.

## Common State Gap Patterns

### The Session/Cache Stale Pattern
**Symptom**: Data is correct in the database but wrong in the UI.
**Cause**: An action updates the DB (or external service) but doesn't invalidate/refresh the client-side cache, session, or local state.
**Fix**: Ensure every write path also invalidates or refreshes the cache/session.

### The Multi-Entry-Point Pattern
**Symptom**: Feature works when accessed one way but not another.
**Cause**: State is correctly initialized from one entry point (e.g., normal app launch) but not another (e.g., deep link, push notification tap, widget, onboarding flow).
**Fix**: Centralize state initialization so all entry points go through the same path.

### The Webhook Timing Pattern
**Symptom**: Works in development but intermittently fails in production.
**Cause**: Client-side code assumes a webhook has already fired and updated the DB, but webhooks can be delayed seconds or minutes.
**Fix**: Don't rely on webhook completion for immediate UI feedback. Use optimistic updates or poll.

### The Dual-Source-of-Truth Pattern
**Symptom**: State is sometimes right, sometimes wrong, with no clear pattern.
**Cause**: Two different parts of the system each think they own the state (e.g., Stripe says "active" but your DB says "canceled" because a webhook failed).
**Fix**: Designate one authoritative source and reconcile on read, or add a sync mechanism.
