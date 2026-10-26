# Deployment Guide

> Maintained by QuorQ AI SDLC. Text between `sdlc:auto` markers is regenerated; anything outside them is yours and is kept.

<!-- sdlc:auto:start how-it-deploys -->
Pushes to `main` run `.github/workflows/deploy.yml`, which builds the API image (if there is a backend) and deploys the SPA to AWS Amplify. Commits touching only `docs/sdlc/**` don't deploy. Deployments can also be started from the platform's Deployment tab.
<!-- sdlc:auto:end how-it-deploys -->

<!-- sdlc:auto:start environments -->
| Name | Service | Runtime | Scaling | Environment |
|---|---|---|---|---|
| Web App (static) | React SPA on S3 + CloudFront | Static assets | CDN-served, single small distribution | dev |
| API Server | FastAPI on a single AWS App Runner / small EC2 instance | Python 3.12 / Uvicorn | Single instance (individual-scale, NFR-006) | dev |
| Database | Amazon RDS for PostgreSQL 16 | PostgreSQL 16 | Single small instance, encrypted storage | dev |
<!-- sdlc:auto:end environments -->

<!-- sdlc:auto:start live -->
_Not deployed yet._
<!-- sdlc:auto:end live -->

<!-- sdlc:auto:start topology -->
```mermaid
graph LR
  n_Web_App__static_["Web App (static)<br/>React SPA on S3 + CloudFront"]
  n_API_Server["API Server<br/>FastAPI on a single AWS App Runner / small EC2 instance"]
  n_Database["Database<br/>Amazon RDS for PostgreSQL 16"]
  n_Web_App__static_ -->|"HTTPS"| n_API_Server
  n_API_Server -->|"TLS (PostgreSQL wire)"| n_Database
```
<!-- sdlc:auto:end topology -->
