# Implementation Plan: Gameplay Interaction

## Summary

Add canvas drawing (drawer), guess submission/scoring (guessers), guess history display (all), and auto-redirect on game start. Sync canvas via dedicated HTTP polling.

## Architecture Changes

### Backend

**New types** (`backend/src/models/game.ts`):
- `Point { x: number; y: number }` — a single coordinate
- `Stroke = Point[]` — one continuous drawn line
- `Guess { participantId, participantName, text, isCorrect, timestamp }`

**Room additions**:
- `scores: Record<string, number>` — cumulative per-participant
- `guesses: Guess[]` — current-round guesses
- `canvasStrokes: Stroke[]` — current canvas drawing
- `correctGuessers: string[]` — who already scored this round

**RoomSnapshot additions**:
- `scores: Record<string, number>`
- `guesses: Guess[]`

**New routes** (`POST /:code/guess`, `GET /:code/canvas`, `POST /:code/canvas`, `POST /:code/canvas/clear`)

**New schemas** (`submitGuessSchema`, `saveCanvasSchema`)

### Frontend

- `api.ts`: add `submitGuess`, `fetchCanvas`, `saveCanvas`, `clearCanvas`
- `roomStore.ts`: add guesses/canvas state + polling
- `LobbyPage.tsx`: auto-redirect to `/game` on `status === "playing"`
- `GamePage.tsx`: canvas + guesses polling, wire draw/guess actions
- **New `Canvas.tsx`**: HTML5 Canvas with pointer-event drawing
- `GuessForm.tsx`: wire to `roomStore.submitGuess`
- `Scoreboard.tsx`: render real scores from room data
- `ResultPanel.tsx`: render guess history

## Implementation Order

1. Backend types + schemas
2. Backend room store functions (submitGuess, canvas CRUD)
3. Backend routes
4. Frontend API client additions
5. Frontend store additions
6. Frontend auto-redirect (LobbyPage)
7. Canvas component + GamePage integration
8. GuessForm wiring
9. Scoreboard + ResultPanel wiring
10. Build validation
