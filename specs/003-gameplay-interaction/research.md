# Research: Gameplay Interaction

No NEEDS CLARIFICATION items remain after the clarify session. All decisions are documented in the spec's Clarifications section and summarized below.

## Confirmed Decisions

| Topic | Decision | Rationale |
|-------|----------|-----------|
| Canvas sync | HTTP polling (separate endpoint at 1s) | Constitution forbids WebSockets (Q2) |
| Drawer sees guesses | Yes | Increased engagement (Q1) |
| Round on correct guess | Continues; correct guesser locked out from further scoring | More participation (Q3) |
| Guess input | Free text, case-insensitive exact match | Simple, deterministic (Q4) |
| Scoring | Flat 100 pts per first correct guess per round | Simple, no time-pressure advantage (Q5) |
| Max guess length | 100 chars, reject with error | Prevents abuse (A3 remediation) |
| Error messages | Empty: "Guess cannot be empty". Too long: "Guess is too long (max 100 characters)" | Clear, actionable (A3 remediation) |

## Key Constraints

- No WebSockets, no databases, no authentication, no new top-level dependencies
- All state in-memory; game logic pure and deterministic
- TypeScript strict mode; no `any`
