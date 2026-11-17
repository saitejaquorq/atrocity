<!-- sdlc:auto:start adr -->
# ADR-0002: Use FastAPI 0.115 for Backend

Status: Accepted  
Date: 2026-11-17

## Context and Problem Statement

Which Backend technology does the project use? Decided in architecture v1.

## Considered Options

- FastAPI 0.115
- Django REST Framework
- Flask

## Decision Outcome

Chosen option: FastAPI 0.115, because Typed request handling keeps the admin edit/override and IGDB import logic clear and reliable, and its async HTTP support talks to IGDB comfortably while serving read-heavy filtered lists.

## Consequences

Fewer batteries-included admin tools than Django, but the admin UI lives in the React app so this is not needed.
<!-- sdlc:auto:end adr -->