<!-- sdlc:auto:start adr -->
# ADR-0002: Use FastAPI 0.115 for Backend

Status: Accepted  
Date: 2026-10-26

## Context and Problem Statement

Which Backend technology does the project use? Decided in architecture v1.

## Considered Options

- FastAPI 0.115
- Flask
- Express

## Decision Outcome

Chosen option: FastAPI 0.115, because Approved stack. Implements the small, well-defined set of auth and note-CRUD/search endpoints with request validation and dependency-injected auth guards.

## Consequences

Async framework is more than this modest load needs, but the overhead is negligible and keeps the app cheap to run.
<!-- sdlc:auto:end adr -->