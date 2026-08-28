# kolo-toolkit

## Purpose
Scaffolding and guardrails for the Kolo Expo app — keeps new Bounded Contexts,
screens, and secrets consistent with the project's feature-first architecture.

## Install
--scope project into the repo so all collaborators get it after clone.

## Commands
- /kolo-toolkit:scaffold-screen <name> — scaffold a new screen/flow.

## Skills
- scaffold-bc — scaffold a new Bounded Context (ui/logic/data + aliases + CLAUDE.md).
  Invoke: /kolo-toolkit:scaffold-bc <name>.

## Hooks
- PreToolUse (Write|Edit) — blocks edits to .env files. Protects secrets deterministically.
