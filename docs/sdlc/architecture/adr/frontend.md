<!-- sdlc:auto:start adr -->
# ADR-0001: Use React for Frontend

Status: Accepted  
Date: 2026-11-17

## Context and Problem Statement

Which Frontend technology does the project use? Decided in architecture v1.

## Considered Options

- React
- SvelteKit
- Vue/Nuxt

## Decision Outcome

Chosen option: React, because React 19 rendered through Next.js server-side rendering produces indexable HTML for 'Releases of YYYY' and detail pages (NFR-005) while giving snappy client-side filtering/sorting; it reads the API base URL from NEXT_PUBLIC_API_BASE_URL and sends Authorization: Bearer tokens for admin calls.

## Consequences

SSR adds a Node runtime to operate, but it is required for SEO and is modest to run at this scale.
<!-- sdlc:auto:end adr -->