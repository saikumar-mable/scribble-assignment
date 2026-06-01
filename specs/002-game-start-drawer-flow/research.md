# Research: Game Start & Drawer Flow

**Phase**: 0 — Outline & Research  
**Date**: 2026-06-01

## Overview

No NEEDS CLARIFICATION markers were present in the spec. All design decisions are covered by existing patterns from Scenario 1 and the project constitution.

## Key Decisions

### Secret Word Selection — Deterministic Index

- **Decision**: Use round-index-based selection from the starter list: `STARTER_WORDS[roundNumber - 1]`. For round 1, this always selects `STARTER_WORDS[0]`.
- **Rationale**: Fully deterministic, trivially verifiable, and extendable to future rounds (round 2 → `STARTER_WORDS[1]`, etc.). Room code hashing was considered but adds unnecessary complexity — there is only one word list shared across all rooms.
- **Alternatives considered**: Hash of room code modulo word count (over-engineered), random selection (violates determinism requirement), hardcoded first word (works but less extensible to multi-round).

### Secret Word Communication — Server-Side Per-Participant Filtering

- **Decision**: The `GET /rooms/:code` endpoint (via `toRoomSnapshot`) will include a `secretWord` field only when the requesting `participantId` matches the drawer's participant ID. Guessers receive `secretWord: null` or the field is omitted entirely.
- **Rationale**: This ensures the word never leaves the server for guessers, satisfying FR-007 ("not in any data the guesser receives from the server"). The existing `toRoomSnapshot(room, viewerParticipantId)` signature already supports per-viewer filtering.
- **Alternatives considered**: Frontend role-based hiding (word would be visible in network inspector — security concern), separate endpoint for drawer-only word fetch (unnecessary round trip).

### Drawer Assignment — Host as Drawer for Round 1

- **Decision**: When `POST /rooms/:code/start` transitions the room to `"playing"`, the host's participant ID is recorded as the current drawer. The `RoomSnapshot`'s `roles` field is populated accordingly: one `"drawer"` entry (the host) and `"guesser"` for all others.
- **Rationale**: Aligns with existing `STARTER_ROLES` pattern ("drawer", "guesser") from `starterData.ts`. The order of roles in the array maps to participant order (first participant = host = drawer).
- **Alternatives considered**: Dedicated `currentDrawerId` field on Room (equivalent outcome but more explicit — recommended).

### Name Trimming — Zod Schema `.trim()` Before `.min()`

- **Decision**: Use Zod's `.trim()` chained before `.min(1)` on the `playerNameSchema`. This trims whitespace first, then validates the remaining string length is ≥1.
- **Rationale**: The schema already exists in `backend/src/api/schemas.ts` from Scenario 1. The trimming step needs to be added. This applies to both create and join flows consistently.
- **Alternatives considered**: Manual trim in the route handler (duplication), frontend-only trim (server must also validate).

## Dependencies

- Scenario 1's `POST /rooms/:code/start` endpoint must be functional.
- The existing `RoomSnapshot` model already has `roles: ParticipantRole[]` and `availableWords: string[]` — these will be populated correctly rather than using placeholder starter data.
