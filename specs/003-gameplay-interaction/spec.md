# Feature Specification: Gameplay Interaction

**Feature Branch**: `sdd-2-scribble-assignment` (single-branch workflow)
**Created**: 2026-06-01
**Status**: Draft
**Input**: User description: "Scenario 3 — Gameplay Interaction: Given a round is active with a drawer and guessers (all scores start at 0), When the drawer draws/clears the canvas and guessers submit their guesses, Then the drawing is visible on the drawer's screen; guesses are trimmed, case-insensitively compared, and empty ones rejected; the guess history is synced to all players via polling; correct guesses score 100 (incorrect add 0)."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Draw and Share Canvas (Priority: P1)

The drawer can draw on the canvas using a basic drawing tool and clear it to start over. The current canvas state is visible to all players via polling, so guessers can see what the drawer is drawing in near real-time.

**Why this priority**: The drawing is the core visual of the game — guessers need to see it to make guesses. Without this, there is no gameplay.

**Independent Test**: Start a game with two browser tabs (one drawer, one guesser). The drawer draws a simple shape on the canvas. Within a few seconds, the guesser's screen shows the same drawing.

**Acceptance Scenarios**:

1. **Given** a round is active with a designated drawer, **When** the drawer draws on the canvas, **Then** the drawing marks appear on the drawer's canvas in real-time.
2. **Given** a round is active with a designated drawer, **When** the drawer clicks the "Clear Canvas" button, **Then** the canvas is cleared and both the drawer and all guessers see a blank canvas on the next poll.
3. **Given** a round is active with a guesser, **When** they view the game screen, **Then** they see the current canvas state (as last drawn by the drawer) updated via polling.

---

### User Story 2 - Submit and Score Guesses (Priority: P1)

Guessers submit text guesses for the secret word. Each guess is trimmed of whitespace, compared case-insensitively to the secret word, and empty guesses are rejected with an error. Correct guesses add 100 points to the guesser's score; incorrect guesses add 0. All guesses and their results are recorded in the guess history.

**Why this priority**: Guessing is the primary interaction for non-drawer players. Scoring creates engagement and competition.

**Independent Test**: Start a game with two guesser tabs. One submits the correct word (with extra spaces and wrong case) and gets +100. The other submits an incorrect word and gets +0. Empty submissions are rejected. Both see the guess history populating.

**Acceptance Scenarios**:

1. **Given** a guesser submits a guess with leading or trailing whitespace, **When** the guess is processed, **Then** the whitespace is trimmed before comparison.
2. **Given** a guesser submits a guess that matches the secret word case-insensitively (e.g., "Rocket" vs "rocket"), **When** the guess is processed, **Then** it is scored as correct (+100).
3. **Given** a guesser submits an empty or whitespace-only guess, **When** the guess is processed, **Then** an error message is displayed and no score change occurs.
4. **Given** a guesser submits an incorrect guess, **When** the guess is processed, **Then** the guesser's score stays unchanged (+0).
5. **Given** a guesser submits a correct guess, **When** the guess is processed, **Then** the guesser's score increases by 100 points.
6. **Given** a guesser submits a correct guess, **When** the round continues, **Then** the same guesser cannot score additional points for the same secret word in the same round.

---

### User Story 3 - Guess History via Polling (Priority: P2)

All players can see the full history of guesses for the current round, including who guessed what and whether each guess was correct. The history is synced via HTTP polling.

**Why this priority**: Seeing previous guesses helps guessers avoid repeating incorrect words and creates a shared game experience.

**Independent Test**: Multiple guessers submit guesses (correct and incorrect). All players (including the drawer) see the guess history update within a few seconds on their screens.

**Acceptance Scenarios**:

1. **Given** any player views the game screen, **When** a guess is submitted by any guesser, **Then** the guess appears in the guess history on the next poll (within a few seconds).
2. **Given** a player views the guess history, **When** they see a guess entry, **Then** it shows the guesser's name, the guess text, and whether it was correct or incorrect.
3. **Given** the drawer views the guess history, **When** the round is active, **Then** the drawer can see all guesses but the secret word remains hidden from guessers.

---

### Edge Cases

- Same guesser submits the correct word multiple times — only the first correct guess adds points; subsequent correct guesses by the same guesser in the same round score 0.
- Guesser submits a guess that matches the secret word after trimming but differs in spacing internally (e.g., secret word is "hot dog", guess is "hot  dog") — comparison is exact after trim; internal spacing matters.
- Multiple guessers guess correctly in the same round — each gets +100 for their first correct guess.
- Canvas is cleared by the drawer while a guesser is viewing — on the next poll, the guesser sees a blank canvas.
- Guesser submits a guess during a round transition — guess should be rejected with an appropriate error (round is no longer active).
- Extremely long guess text — system should handle gracefully by truncating or rejecting with a message.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST provide a drawing surface that responds to the drawer's pointer input during an active round.
- **FR-002**: System MUST provide a "Clear Canvas" button for the drawer that resets the canvas to blank.
- **FR-003**: System MUST make the current canvas state available to all players so that guessers see the drawing.
- **FR-004**: System MUST accept guess submissions from non-drawer participants during an active round.
- **FR-005**: System MUST trim leading and trailing whitespace from submitted guesses before validation.
- **FR-006**: System MUST reject empty or whitespace-only guesses with a clear error message.
- **FR-007**: System MUST compare guesses against the secret word case-insensitively.
- **FR-008**: System MUST award 100 points to a guesser for their first correct guess in a round.
- **FR-009**: System MUST NOT award points for incorrect guesses, empty guesses, or subsequent correct guesses by the same guesser in the same round.
- **FR-010**: System MUST track and expose a running score per participant across rounds.
- **FR-011**: System MUST expose a guess history for the current round, including guesser name, guess text, and correctness, to all players.
- **FR-012**: System MUST ensure the secret word is never exposed to guessers in any data they receive from the server.

### Key Entities *(include if feature involves data)*

- **Canvas State**: The visual content of the drawing canvas at a given point in time. Updated by the drawer and synced to all players.
- **Guess**: A text submission from a guesser during an active round. Includes the guesser's participant ID, trimmed guess text, timestamp, and correctness flag. Stored in round-level history.
- **Score**: A cumulative integer value per participant, starting at 0 each game. Updated when a guesser submits their first correct guess in a round.
- **Guess History**: An ordered list of all guesses submitted in the current round, visible to all players.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A guesser can submit a guess and see their score reflect +100 (correct) or +0 (incorrect) within 2 seconds of submission.
- **SC-002**: Empty or whitespace-only guesses are rejected with an error message within 1 second of submission.
- **SC-003**: A guesser can see the current canvas state within 3 seconds of the drawer drawing or clearing.
- **SC-004**: The guess history shows all guesses for the current round, including correctness, to all players within 3 seconds of a guess being submitted.
- **SC-005**: All players' scores are consistent across all screens at all times (no player sees a different score for the same participant).

## Assumptions

- The round and secret word are already set up by Scenario 2 — this feature assumes an active round with a drawer, guessers, and a secret word.
- The canvas is a basic 2D drawing surface (freehand drawing with a single color/brush). Advanced drawing tools (color picker, brush sizes, shapes) are out of scope.
- Canvas state is synced via periodic updates, not real-time streaming. This means there may be a short delay between drawing and visibility for other players.
- The drawer cannot see guesses during the round to prevent influencing their drawing.
- The polling interval is assumed to be 2 seconds (matching Scenario 2's lobby polling interval) for canvas state and guess history.
- Guesses are compared against the secret word on the server side only — the word never leaves the server for guessers (preserving FR-007 from Scenario 2).
- Scores persist across rounds within the same game session but reset when a new game is created.
- Multiple rounds and round transitions are out of scope for this scenario — the round is assumed to be active throughout.
