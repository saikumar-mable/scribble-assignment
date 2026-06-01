# Tasks: Gameplay Interaction

**Input**: Design documents from `specs/003-gameplay-interaction/`
**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/

**Organization**: Tasks are grouped by user story to enable independent implementation and testing.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

---

## Phase 1: Setup

**Purpose**: Project is already initialized. No setup tasks needed.

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Backend infrastructure + frontend API/store layer that ALL user stories depend on.

**⚠️ CRITICAL**: No user story work can begin until this phase is complete.

- [ ] T001 [P] Add Point, Stroke, Guess types and Room/RoomSnapshot fields (scores, guesses, correctGuessers, canvasStrokes) in `backend/src/models/game.ts`
- [ ] T002 [P] Add submitGuessSchema, saveCanvasSchema, clearCanvasSchema in `backend/src/api/schemas.ts`
- [ ] T003 Add room store functions (submitGuess, saveCanvasState, clearCanvasState, getCanvasState, getGuesses) + update createRoom/startGame initializers in `backend/src/services/roomStore.ts`
- [ ] T004 Add POST /:code/guess, GET /:code/canvas, POST /:code/canvas, POST /:code/canvas/clear routes in `backend/src/api/rooms.ts`
- [ ] T005 [P] Add submitGuess, fetchCanvas, saveCanvas, clearCanvas API methods in `frontend/src/services/api.ts`
- [ ] T006 Add store methods + state for guesses, canvas strokes, and polling intervals in `frontend/src/state/roomStore.ts`

**Checkpoint**: Foundation ready — all backend endpoints functional, frontend can call them. User story implementation can now begin.

---

## Phase 3: User Story 2 — Auto-Navigate to Game on Start (Priority: P1)

**Goal**: When the host starts the game from the lobby, all guessers automatically detect the "playing" status via polling and are redirected to the game view.

**Independent Test**: Open two browser tabs and join the same room. Start the game from the host tab. Within 3 seconds, both tabs show the game view.

- [ ] T007 [US2] Add auto-redirect to /game in lobby polling callback in `frontend/src/pages/LobbyPage.tsx` when `room.status === "playing"`

**Checkpoint**: US2 complete — lobby-to-game redirect works automatically.

---

## Phase 4: User Story 1 — Draw and Share Canvas (Priority: P1)

**Goal**: The drawer can draw on an HTML5 Canvas and clear it. The canvas state is available to all players via HTTP polling at 1s interval.

**Independent Test**: Start a game with two browser tabs. The drawer draws a shape. Within a few seconds (at most 1s poll interval + network), the guesser's screen shows the same drawing. Clear canvas → both see blank.

- [ ] T008 [P] [US1] Create Canvas component with pointer-event drawing (pointerdown/move/up) + clear button in `frontend/src/components/Canvas.tsx`
- [ ] T009 [US1] Integrate Canvas into GamePage, add canvas polling for guessers, wire save/clear/clearCanvas, add CSS in `frontend/src/pages/GamePage.tsx` and `frontend/src/styles/app.css`

**Checkpoint**: US1 complete — drawer draws, guessers see canvas update.

---

## Phase 5: User Story 3 — Submit and Score Guesses (Priority: P1)

**Goal**: Guessers submit text guesses. Guesses are trimmed, case-insensitively compared to the secret word. Correct guesses score +100. Empty/long guesses are rejected.

**Independent Test**: Start a game with two guesser tabs. One submits the correct word and gets +100. The other submits an incorrect word and gets +0. Empty submissions are rejected with "Guess cannot be empty". Long submissions rejected with "Guess is too long (max 100 characters)".

- [ ] T010 [US3] Wire GuessForm to call roomStore.submitGuess, display error messages for empty/too-long guesses in `frontend/src/components/GuessForm.tsx`
- [ ] T011 [US3] Wire Scoreboard to render real scores from room data in `frontend/src/components/Scoreboard.tsx`

**Checkpoint**: US3 complete — guess submission works, scores update, errors display.

---

## Phase 6: User Story 4 — Guess History via Polling (Priority: P2)

**Goal**: All players (including drawer) can see the full guess history for the current round — who guessed what and whether it was correct.

**Independent Test**: Multiple guessers submit guesses (correct and incorrect). All players see the guess history update within a few seconds (via 2s room poll).

- [ ] T012 [US4] Wire ResultPanel to render guess history from room data in `frontend/src/components/ResultPanel.tsx`

**Checkpoint**: US4 complete — guess history visible to all players.

---

## Phase 7: Polish & Cross-Cutting Concerns

- [ ] T013 Run `npm run build` in both `backend/` and `frontend/` — fix any build errors

---

## Dependencies & Execution Order

### Phase Dependencies

- **Phase 1 (Setup)**: No dependencies — already complete
- **Phase 2 (Foundational)**: Blocks ALL user stories
- **Phase 3 (US2)**: Depends on Phase 2 — auto-redirect uses store polling
- **Phase 4 (US1)**: Depends on Phase 2 — canvas uses API and store
- **Phase 5 (US3)**: Depends on Phase 2 — guess submission uses API and store
- **Phase 6 (US4)**: Depends on Phase 2 + Phase 5 — guess history depends on guesses existing
- **Phase 7 (Polish)**: Depends on all phases

### User Story Dependencies

- **US2 (P1)**: Independent — can start right after Phase 2
- **US1 (P1)**: Independent of US2/US3/US4 — can start right after Phase 2
- **US3 (P1)**: Independent of US1/US2 — can start right after Phase 2
- **US4 (P2)**: Depends on US3 (needs guesses to display)

### Within Each User Story

- Backend before frontend
- Models before services
- Services before routes
- API client before store
- Store before UI components

### Parallel Opportunities

- T001 and T002 can run in parallel (different files)
- T005 can run in parallel with T004 (independent frontend/backend)
- US1, US2, US3 can start in parallel after Phase 2
- US4 depends on US3 (needs guesses data)

---

## Parallel Example: User Story 1

```bash
# T008 and API/store wiring can be done concurrently:
Task: "Create Canvas component in components/Canvas.tsx"
Task: "Update GamePage to integrate canvas + polling"
```

## Parallel Example: Phase 2

```bash
# T001 and T002 are independent:
Task: "Add types in models/game.ts"
Task: "Add schemas in api/schemas.ts"
```

---

## Implementation Strategy

### Recommended Order

1. **Phase 2**: Complete all backend + frontend infrastructure
2. **Phase 3 (US2)**: Auto-redirect (small, quick win)
3. **Phase 4 (US1)**: Canvas drawing (core gameplay loop)
4. **Phase 5 (US3)**: Guess submission & scoring
5. **Phase 6 (US4)**: Guess history display
6. **Phase 7**: Build validation

### Incremental Delivery

1. Foundational complete → Backend fully ready, frontend can call all endpoints
2. US2 done → Players auto-navigate to game (Scenario 2 flow + redirect)
3. US1 done → Drawer draws, guessers see canvas (core visual gameplay)
4. US3 done → Full guess/score loop works
5. US4 done → History visible to all (complete feature)

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story
- Each user story is independently completable and testable
- Commit after each task or logical group
- Stop at any checkpoint to validate independently
