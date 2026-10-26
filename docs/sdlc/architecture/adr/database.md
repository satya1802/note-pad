<!-- sdlc:auto:start adr -->
# ADR-0003: Use PostgreSQL 16 for Database

Status: Accepted  
Date: 2026-10-26

## Context and Problem Statement

Which Database technology does the project use? Decided in architecture v1.

## Considered Options

- PostgreSQL 16
- MySQL
- SQLite

## Decision Outcome

Chosen option: PostgreSQL 16, because Approved stack. Stores users and notes relationally and covers title/body search with built-in full-text search (tsvector + GIN), so no separate search service is required (US-010).

## Consequences

Full-text ranking is simpler than a dedicated search engine, which is ample at individual scale.
<!-- sdlc:auto:end adr -->