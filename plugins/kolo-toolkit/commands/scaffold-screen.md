Scaffold a new screen or flow named "$ARGUMENTS" in this Expo/React Native (expo-router) project.

If $ARGUMENTS is empty: STOP and ask what screen/flow to create. Do not guess.

Read ARCHITECTURE.md, CLAUDE.md, and ADR 0004 (routing) first. Match the existing feature-first + expo-router conventions exactly. Do not invent new patterns.

Respect the dependency rule at all times:
- Screen UI lives in a BC's ui/ layer (src/<bc>/ui/), NOT in app/.
- app/ route files are WIRING ONLY: they import and render a BC ui/ screen, zero business logic.
- Any decision logic (e.g. routing based on state) lives in logic/ (pure) or composition/, never in the route file or the screen's render.

Show every file before writing (show-then-pause). Do NOT commit — leave in working tree.

FOR THIS INVOCATION, build a minimal app-bootstrap flow — the thin vertical slice, placeholders only, no real data:

1. A local flag "has_launched_before" (boolean) stored on-device (expo-secure-store or async local store — pick the simplest, note the choice). This flag is NOT cycle data, NOT E2EE — just a launch marker.

2. Splash screen: shows a loader with a progress indicator briefly, then reads the flag and routes:
   - flag ABSENT  → "first-time" screen
   - flag PRESENT → "returning" screen

3. "First-time" screen (placeholder for future onboarding): plain text "Юзер перший раз" + a "Продовжити" button. Tapping it SETS the flag, then navigates to the home placeholder. (The flag is set on button tap = future "form submitted", NOT on app open — a user who just opens and leaves is NOT counted as onboarded.)

4. "Returning" / home screen (placeholder for future main cycle screen): plain text "Юзер вже заходив" — nothing else yet.

Keep the phase-calculation / cycle logic OUT of this slice entirely — this is only the launch gate. Do not touch cycle/data (blocked by ADR 0002).

Wire the routes in app/ (expo-router) as wiring-only. Put screen components in the appropriate BC ui/ (likely a new small "onboarding" or "app" concern, or reuse composition — propose which and ask if unsure).

After writing: run `make lint` to confirm boundaries rules still pass (no logic→data, no logic in app/). Report the result.

Before finishing: confirm the flag-set happens on button tap, not on app open — this is the one piece of logic that must be correct.
