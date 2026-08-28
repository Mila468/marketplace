---
name: scaffold-bc
description: Scaffold a new Bounded Context in this Expo/React Native project. Use when adding a new BC, creating a new feature domain, or when the user says "add a bounded context", "new BC", or "scaffold a feature domain" (e.g. lunar, analytics, progress).
---

# Scaffold a new Bounded Context

Creates a new feature-first Bounded Context named `$ARGUMENTS` with ui/logic/data layers and
wires it into every alias + boundary config. Creates STRUCTURE ONLY — empty contract stubs,
no business logic (that comes later, in a separate prompt with the feature's actual spec).

Read `ARCHITECTURE.md` and `CLAUDE.md` first to match conventions. Show every file/edit
before writing (show-then-pause). Do NOT commit.

If `$ARGUMENTS` is empty: STOP and ask which BC to create. Do not guess a name.

## Workflow

- [ ] **Step 1 — folders.** Create `src/$ARGUMENTS/{ui,logic,data}`. Each `index.ts` is a
      COMMENT-ONLY contract stub (no runnable code) stating what the layer owns + its rule:
      - `logic/index.ts` → PURE: no react/react-native/expo-*/UI/infra. May import: logic, shared (@shared/ports types).
      - `data/index.ts`  → implements logic ports. May import: data, logic, shared, platform.
      - `ui/index.ts`    → screens/components. May import: ui, logic, shared, platform.

- [ ] **Step 2 — tsconfig alias.** Add `"@$ARGUMENTS/*": ["src/$ARGUMENTS/*"]` to
      `compilerOptions.paths` in `tsconfig.json`. (WITH `/*` suffix.)

- [ ] **Step 3 — babel alias.** Add `"@$ARGUMENTS": "./src/$ARGUMENTS"` to the module-resolver
      alias map in `babel.config.js`. (NO `/*` suffix — babel does prefix matching.)

- [ ] **Step 4 — eslint boundaries.** In `.eslintrc.js`, ensure `boundaries/elements` maps
      `src/$ARGUMENTS/{ui,logic,data}` to the ui/logic/data element types. Do NOT weaken
      existing element-types rules.

- [ ] **Step 5 — CLAUDE.md.** Add `$ARGUMENTS` to the BC list in the Architecture rules section.

- [ ] **Step 6 — verify.** Run `make lint`. Report the raw result. Separate BOUNDARIES
      violations (real problem) from unresolved-import errors (install-only, not a code issue).

## Ask before writing

This BC's E2EE posture changes `ARCHITECTURE.md`. Ask the user:
"Is `$ARGUMENTS` E2EE (reads decrypted cycle data -> on-device only) or non-E2EE (server-cache OK)?"
Then add it to the correct side of the E2EE perimeter section.

## Gotchas (real pitfalls seen in this project)

- **The alias must match in THREE places** (tsconfig, babel, eslint resolver). A mismatch
  passes `tsc` but crashes Metro at runtime with "cannot resolve module". Verify all three
  agree letter-for-letter before finishing.
- **tsconfig uses `/*`, babel does NOT.** `"@x/*"` in tsconfig but `"@x"` in babel. This
  asymmetry is correct — do not "fix" it to match.
- **`logic/` must stay pure.** The most common mistake is importing `expo-*` or a UI/infra
  package into `logic/`. Storage/native access lives in `data/` behind a port; `logic`
  depends on the port TYPE, never the concretion.
- **Do not hoist BC-specific ports to @shared.** A port used only by this BC lives in
  `src/$ARGUMENTS/logic/ports/`. Only cross-cutting capabilities (crypto/sync/storage) go to @shared/ports.
- **eslint default-disallow.** If boundaries has no default-disallow, an unmapped path is
  silently allowed. Confirm the new BC's layers are actually matched by an element rule.
