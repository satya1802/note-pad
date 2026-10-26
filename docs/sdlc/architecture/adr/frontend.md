<!-- sdlc:auto:start adr -->
# ADR-0001: Use React 19 for Frontend

Status: Accepted  
Date: 2026-10-26

## Context and Problem Statement

Which Frontend technology does the project use? Decided in architecture v1.

## Considered Options

- React 19
- Vue
- Svelte

## Decision Outcome

Chosen option: React 19, because Approved stack. Powers the responsive single-page notepad with a live notes list, search box, and note editor, adapting layout across desktop and mobile browsers (NFR-004, NFR-005).

## Consequences

A SPA needs client-side route guards to redirect unauthenticated users; kept simple with a lightweight router.
<!-- sdlc:auto:end adr -->