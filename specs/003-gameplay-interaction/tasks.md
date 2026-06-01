# Implementation Tasks: Gameplay Interaction

## Backend

- [ ] T001: Add new types (Point, Stroke, Guess) and Room/RoomSnapshot fields
- [ ] T002: Add Zod schemas (submitGuessSchema, saveCanvasSchema)
- [ ] T003: Add room store functions (submitGuess, saveCanvasState, clearCanvasState, getCanvasState) + Room init updates
- [ ] T004: Add backend routes (POST guess, GET canvas, POST canvas, POST canvas/clear)

## Frontend

- [ ] T005: Add API client methods (submitGuess, fetchCanvas, saveCanvas, clearCanvas)
- [ ] T006: Add store methods + state for guesses/canvas + polling
- [ ] T007: Auto-redirect LobbyPage to /game when status === "playing"
- [ ] T008: Create Canvas component with pointer-event drawing + clear button
- [ ] T009: Wire GuessForm to submit guesses + show errors
- [ ] T010: Wire Scoreboard to show real scores from room data
- [ ] T011: Wire ResultPanel to show guess history
- [ ] T012: Build validation (backend + frontend)

Legend: [ ] = pending, [X] = completed
