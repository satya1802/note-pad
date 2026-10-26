<!-- sdlc:auto:start adr -->
# ADR-0004: Use AWS for Cloud

Status: Accepted  
Date: 2026-10-26

## Context and Problem Statement

Which Cloud technology does the project use? Decided in architecture v1.

## Considered Options

- AWS
- GCP
- Azure

## Decision Outcome

Chosen option: AWS, because Approved stack. Hosts the static SPA (S3/CloudFront), the FastAPI service, and managed PostgreSQL (RDS) cheaply with room to grow, with TLS and encryption at rest for the stored credentials and notes.

## Consequences

Managed services carry a small baseline cost but remove ops burden for a free-to-run app.
<!-- sdlc:auto:end adr -->