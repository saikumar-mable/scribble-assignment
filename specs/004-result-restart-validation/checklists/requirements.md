# Requirements Checklist: Result, Restart & Final Validation

## Quality Gate

### Spec Completeness
- [ ] All user stories include priority labels (P1/P2)
- [ ] Every acceptance scenario uses Given/When/Then format
- [ ] Independent test exists for each user story
- [ ] Edge cases documented
- [ ] Functional requirements enumerated (FR-001 through FR-009)
- [ ] Success criteria measurable and time-bound
- [ ] Assumptions clearly stated
- [ ] All `[NEEDS CLARIFICATION]` markers resolved before implementation

### FR → Acceptance Scenario Mapping
- [ ] FR-001: Auto-end when all guessers guess correctly → US1 AS1
- [ ] FR-002: Manual end round by drawer → US1 AS2
- [ ] FR-003: Secret word revealed to all in result → US2 AS1
- [ ] FR-004: Display word, scores, history → US2 AS2, AS3
- [ ] FR-005: Host-only restart endpoint → US3 AS1
- [ ] FR-006: Preserve participants on restart → US3 AS2
- [ ] FR-007: Clear round state on restart → US3 AS3
- [ ] FR-008: Auto-detect result on GamePage → US4 AS1
- [ ] FR-009: Reject non-host restart → US3 AS4

## Implementation Checklist

### Backend
- [ ] T001: RoomStatus includes "result"
- [ ] T002: endRound(), restartGame(), auto-end in submitGuess
- [ ] T003: endRoundSchema, restartGameSchema
- [ ] T004: POST /:code/end-round, POST /:code/restart
- [ ] T005: toRoomSnapshot reveals secretWord in result state

### Frontend
- [ ] T006: RoomSnapshot.status includes "result"
- [ ] T007: endRound(), restartGame() API methods
- [ ] T008: endRound(), restartGame() store methods
- [ ] T009: ResultView component
- [ ] T010: Result view CSS
- [ ] T011: GamePage result detection + End Round button

### Build
- [ ] T012: Backend build passes
- [ ] T012: Frontend build passes
