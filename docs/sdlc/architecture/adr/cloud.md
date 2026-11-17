<!-- sdlc:auto:start adr -->
# ADR-0004: Use AWS for Cloud

Status: Accepted  
Date: 2026-11-17

## Context and Problem Statement

Which Cloud technology does the project use? Decided in architecture v1.

## Considered Options

- AWS
- Google Cloud
- Azure

## Decision Outcome

Chosen option: AWS, because One account hosts the app and database, S3 stores cover art, and CloudFront serves it fast worldwide; pay-for-what-you-use pricing keeps a free informational site cheap.

## Consequences

Single-vendor coupling, acceptable for a lightweight site with no multi-cloud requirement.
<!-- sdlc:auto:end adr -->