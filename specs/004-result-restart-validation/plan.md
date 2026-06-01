# Implementation Plan: Result, Restart & Final Validation

**Branch**: `sdd-2-scribble-assignment` (single-branch workflow) | **Date**: 2026-06-01 | **Spec**: `specs/004-result-restart-validation/spec.md`

## Summary

Add round-end detection (auto when all guessers guess correctly + manual drawer End Round), result view (secret word revealed, final scores, guess history), host restart (return to lobby with players preserved, round state cleared). The result view is rendered inline on GamePage, replacing the canvas/guess form.

## Technical Context

**Language/Version**: TypeScript 5.x (backend + frontend)
**Primary Dependencies**: Backend — Express 4, Zod 3, cors. Frontend — React 18, React Router 6, Vite 5. No new top-level dependencies.
**Storage**: In-memory only (constitutional constraint)
**Target Platform**: Node.js (backend), modern browsers (frontend)
**Constraints**: HTTP polling only (no WebSockets), in-memory only, no auth, no new dependencies

## Constitution Check

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Clean Code & Atomic Commits | ✓ PASS | Isolated commits per file/concern |
| II. Deterministic & Testable Game Logic | ✓ PASS | Round-end detection is deterministic (all-of guessers check), restart is pure state reset |
| III. TypeScript Strictness | ✓ PASS | No `any` types; RoomStatus union extended with "result" |
| IV. Spec-Artifact Consistency | ✓ PASS | Spec written; plan tracks implementation |
| V. Brownfield Respect | ✓ PASS | Additive changes to existing models/store/routes/components; no rewrites |
| No WebSockets | ✓ PASS | Status changes detected via existing 2s room polling |
| No Databases | ✓ PASS | Round state stored on in-memory Room objects |
| No Authentication | ✓ PASS | No auth-related changes |
| No New Top-Level Dependencies | ✓ PASS | No new libraries needed |

## Project Structure

### Documentation

```
specs/004-result-restart-validation/
├── spec.md       # Feature specification
├── plan.md       # This file
└── tasks.md      # Task breakdown
```

### Source Code

```
backend/
└── src/
    ├── models/game.ts             # MODIFY: RoomStatus add "result"
    ├── api/schemas.ts             # ADD: endRoundSchema, restartGameSchema
    ├── api/rooms.ts               # ADD: POST /:code/end-round, POST /:code/restart
    └── services/roomStore.ts      # MODIFY: endRound, restartGame; update submitGuess (auto-end), toRoomSnapshot (reveal word in result)

frontend/
└── src/
    ├── services/api.ts            # MODIFY: RoomSnapshot.status add "result"; ADD endRound, restartGame methods
    ├── state/roomStore.ts         # ADD: endRound, restartGame methods
    ├── pages/GamePage.tsx         # MODIFY: detect "result" status, show ResultView; add End Round button for drawer
    ├── components/ResultView.tsx  # NEW: result display (correct word, final scores, guess history, restart button)
    └── styles/app.css             # ADD: result view styles
```

## Complexity Tracking

Low complexity. All changes are additive and follow existing patterns. Round-end detection is a simple check after submitGuess. Result view is a new component reusing existing Scoreboard/ResultPanel logic. Restart is a state reset similar to startGame.

## Implementation Order

1. Backend: Add "result" to RoomStatus type
2. Backend: Add endRound + restartGame functions + auto-end in submitGuess
3. Backend: Add endRound + restartGame schemas and routes
4. Backend: Update toRoomSnapshot to reveal secret word in "result" state
5. Frontend: Update RoomSnapshot type, add endRound/restartGame API + store methods
6. Frontend: Create ResultView component
7. Frontend: Update GamePage to detect "result" status, show ResultView, add End Round button
8. Frontend: Add result view CSS
9. Build validation
