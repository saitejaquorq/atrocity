<!-- sdlc:auto:start adr -->
# ADR-0003: Use PostgreSQL 16 for Database

Status: Accepted  
Date: 2026-11-17

## Context and Problem Statement

Which Database technology does the project use? Decided in architecture v1.

## Considered Options

- PostgreSQL 16
- MySQL
- SQLite

## Decision Outcome

Chosen option: PostgreSQL 16, because Handles faceted filtering/sorting across title, platforms, genres, publisher and date well, and its flexible columns model TBA/quarter/year-only release dates; at hundreds of games a year it is never the bottleneck.

## Consequences

Needs a managed instance to run, but RDS keeps operations light at this scale.
<!-- sdlc:auto:end adr -->