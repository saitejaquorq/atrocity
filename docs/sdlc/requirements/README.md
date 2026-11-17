# Product Requirements

> Maintained by QuorQ AI SDLC. Text between `sdlc:auto` markers is regenerated; anything outside them is yours and is kept.

<!-- sdlc:auto:start narrative -->
## Summary

### REQ-STACK · approved · source: requirements:technology stack

Product owner approved a fixed technology stack: FastAPI backend, AWS cloud, PostgreSQL database, React 19 frontend (server-rendered).

Backend: FastAPI 0.115 -- chosen to serve read-heavy filtered lists to visitors and give admins typed, reliable import/edit/override endpoints. Cloud: AWS -- single account covering app/database hosting, S3 for cover art, and CloudFront as CDN, with pay-for-use pricing suited to a free informational site at modest scale. Database: PostgreSQL 16 -- handles faceted filtering across title, platforms, genres, publisher/developer, release date, and supports flexible/partial dates (TBA, quarter-only, year-only); will not bottleneck at hundreds of games/year. Frontend: React 19 served via Next.js-style server rendering -- gives snappy client-side filtering by platform/genre/publisher/date while server-rendering year roundup pages (e.g. 'Releases of 2026') for SEO indexing. Design must use exactly these, no substitutions.

### REQ-SERVICES · approved · source: requirements:services

Two external services approved: Amazon S3 for cover art storage and IGDB (Twitch API) for catalogue seeding.

File storage: Amazon S3 (saas) -- env vars AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_REGION, S3_BUCKET; stores cover art durably and serves it via CDN, keeping images off the application server. Games catalog: IGDB (Twitch/IGDB API) (custom) -- env vars IGDB_CLIENT_ID, IGDB_CLIENT_SECRET; used by admins to search and import games to seed the catalogue, requires Twitch client credentials for each import. Full API key list required by the app: AWS_ACCESS_KEY_ID, AWS_REGION, AWS_SECRET_ACCESS_KEY, IGDB_CLIENT_ID, IGDB_CLIENT_SECRET, S3_BUCKET.

### REQ-SCOPE · approved · source: requirements:scope

Scope covers public year-based browsing/filtering, game detail pages, and an admin area for manual curation and IGDB import; visitor accounts and social features are explicitly excluded.

In scope: per-year release roundup pages (e.g. 'Releases of 2026'); public catalogue browsing with no visitor account; game detail pages with cover, platforms, genres, publisher and date; admin area to add/edit/remove game records; flexible release dates (TBA, quarter or year-only); filtering and sorting by platform, genre, publisher and date; protected admin sign-in; admin catalogue overview of curated games; email/password sign-in for admins; cover art stored on Amazon S3. Out of scope -- not to be designed or built: visitor accounts, follows, personal watchlists, release reminders/notifications, comments/ratings. Delivery scope noted as 'lightweight'; requirements version 1, status approved.

### REQ-FEAT-001 · approved · source: requirements:Browsing by year

Visitors can open per-year roundup pages listing curated games and navigate between years with no account required.

Year release roundup page: a visitor opens a page for a given year (e.g. 'Releases of 2026') and sees every curated game released that year, each showing at least title and cover art, no account needed; a year with no games shows a clear empty-state message rather than an error. Navigate between years: from any year roundup a visitor can move to other years containing curated games; requesting a year with no page/games responds gracefully with an empty state or clear not-found page rather than a broken page.

### REQ-FEAT-002 · approved · source: requirements:Filtering & sorting

Visitors can filter a year's games by platform, genre and publisher, sort by date (with flexible dates handled predictably), and the applied filters/sort are reflected in a shareable URL.

Filter by platform, genre and publisher: within a year roundup a visitor narrows the list by any combination of these, with multiple filters combining (AND logic) so only games matching all remain; no matches show a clear 'no matching games' message plus a way to clear filters, and clearing restores the full year list. Sort games by release date: a visitor sorts a year's games earliest/latest first; games with flexible dates (TBA, quarter-only, year-only) are placed in a consistent, predictable position rather than dropped. Shareable filtered view: applied filters and sort are reflected in the page URL so copying/reopening the link reproduces the same filtered and sorted list.

### REQ-FEAT-003 · approved · source: requirements:Game details

Visitors can open a game's detail page showing full information, including flexible release dates and a cover-art placeholder when missing.

Game detail page: shows title, cover art, platforms, genres, publisher/developer, release date and short description; a flexible date is shown in its flexible form ('TBA', 'Q3 2026', '2026') rather than a misleading exact date. Open a game from a list: selecting a game in a year roundup or filtered list opens its detail page; requesting a nonexistent or removed game returns a clear not-found response rather than a broken page. Cover art placeholder: when a game has no cover image, a placeholder is shown in its place on roundups and detail pages while the rest of the details still render.

### REQ-FEAT-004 · approved · source: requirements:Admin sign-in

Admins sign in with email and password to reach a protected admin area, with generic failure messages, session expiry, and sign-out.

Admin sign in: email/password sign-in to reach the protected admin area; an incorrect email or password is refused with a generic error that does not reveal which field was wrong. Protected admin area: all admin pages/actions are reachable only when signed in; unauthenticated users are denied and directed to sign in; expired sessions require re-sign-in before further admin actions. Admin sign out: ends the session so admin pages cannot be reached again without signing in.

### REQ-FEAT-005 · approved · source: requirements:Managing games

Admins can view a catalogue overview, add/edit/remove game records, set flexible release dates, and upload cover art to S3, with admin edits preserved as overrides.

Curated catalogue overview: lists all curated games with key fields (title, year/release date, publisher) and a way to open each for editing; an empty catalogue shows an empty state with a way to add the first game. Add a game manually: admin enters title, platforms, genres, publisher/developer, release date and short description; on save the record appears in the overview and relevant year roundup; saving without a title is blocked with a validation message naming the missing field. Edit and override a game: admin can edit any field (manual or imported); updates are saved and reflected publicly; an edit to an imported field becomes an override not overwritten by later imports of the same game. Remove a game: admin removes a game after confirming, so it disappears from the overview and all public pages. Flexible release dates: admin records an exact date, quarter, year-only, or TBA; the stored precision assigns the game to the correct year roundup or an unscheduled/TBA grouping; public pages display the flexible form (e.g. 'Q2 2026') and sort consistently within that year. Upload cover art: admin uploads a cover image, stored in Amazon S3 and displayed as the game's cover publicly.

### REQ-FEAT-006 · approved · source: requirements:Importing from IGDB

Admins can search IGDB and import games as a starting record, with imports strictly admin-triggered and graceful handling of IGDB failures.

Search and import from IGDB: admin searches IGDB by title, chooses a result, and imports it to create a game record pre-filled with available fields (title, cover art, platforms, genres, publisher/developer, release date, description); the record appears in the overview for further curation, and later admin edits are saved as overrides. Admin-triggered imports only: imports run only when an admin searches or imports; no background sync adds/changes games automatically; re-importing an existing game preserves existing admin overrides rather than overwriting them. Handle IGDB problems gracefully: missing/invalid credentials or an unreachable service produce a clear error message with no partial or corrupt record created, so the admin can still curate manually.

### NFR-001 · approved · source: requirements:NFR-001

Admin passwords must be stored only as a hash, never in plain text.

[security] So a person inspecting the database cannot read any admin's password.

### NFR-002 · approved · source: requirements:NFR-002

The admin area and all its actions must be reachable only within a valid signed-in session.

[security] Sessions expire, and signing out ends the session so admin pages can no longer be reached without signing in again.

### NFR-003 · approved · source: requirements:NFR-003

A failed admin sign-in must show a generic error that does not reveal which field (email or password) was wrong.

[security] Applies to every admin sign-in attempt.

### NFR-004 · approved · source: requirements:NFR-004

The public site must allow browsing with no visitor account and must collect no personal data from visitors.

[privacy] No visitor accounts exist; no personal data is collected from visitors anywhere on the public site.

### NFR-005 · approved · source: requirements:NFR-005

Year roundup and game detail pages must be server-rendered so content is present in the returned HTML for search engine indexing.

[usability] Enables pages such as 'Releases of 2026' to be found on Google.

### NFR-006 · approved · source: requirements:NFR-006

Pages must adapt to screen size so narrow mobile viewports reflow to remain readable and usable without horizontal scrolling.

[usability] Applies to all public pages.

### NFR-007 · approved · source: requirements:NFR-007

Cover images must be served from object storage through a CDN so global visitors load them quickly without hitting the application server.

[performance] Backed by Amazon S3 + CloudFront per the approved services.

### NFR-008 · approved · source: requirements:NFR-008

The site must comfortably serve public, read-heavy traffic and filtered lists at a modest scale.

[scalability] Target: hundreds of games per year.

### NFR-009 · approved · source: requirements:NFR-009

IGDB import failures must not corrupt the catalogue.

[availability] When IGDB is unavailable or its credentials are invalid, imports must fail cleanly without creating partial or corrupt records, leaving the existing catalogue intact and still manageable manually.
<!-- sdlc:auto:end narrative -->

<!-- sdlc:auto:start summary -->
**Requirements version:** v1  
**Delivery scope:** lightweight  
**Approved:** 2026-11-17

**Capabilities in scope:** Per-year release roundup pages (e.g. "Releases of 2026"), Public game catalogue browsing with no visitor account, Game detail pages with cover, platforms, genres, publisher and date, Admin area to add, edit and remove game records, Flexible release dates (TBA, quarter or year-only), Filtering and sorting by platform, genre, publisher and date, Protected admin sign-in, Admin catalogue overview of curated games, Email and password sign-in for the admin team, Cover art stored on Amazon S3

**Approved technology stack:**

| Layer | Choice | Rationale |
|---|---|---|
| backend | FastAPI | The work your backend does is well-defined: serve read-heavy filtered lists to visitors, and give a small admin team endpoints to import from IGDB and add/edit/override records. FastAPI is a good fit for both — it talks to IGDB's API comfortably and its typed request handling keeps the admin edit/override logic clear and reliable at your modest scale. |
| cloud | AWS | The site is public, read-heavy and global, and stores cover images that need a CDN. AWS covers all of it in one account: hosting for the app and database, S3 for cover art, and CloudFront to serve those images fast worldwide. For a free informational site at modest scale, its pay-for-what-you-use pricing keeps the bill small. |
| database | PostgreSQL | Your records are highly structured — title, platforms, genres, publisher/developer, release date, description — and visitors filter and sort across exactly those fields. PostgreSQL handles this faceted filtering well, and its ability to store flexible or partial dates lets you model the TBA / quarter-only / year-only release dates your admins need. At hundreds of games a year it will never be the bottleneck. |
| frontend | React | Your visitors browse and filter a catalog with no sign-in, and the pages must be found on Google. React (served through Next.js-style server rendering on this platform) gives you the snappy filtering by platform, genre, publisher and date that is the core of the site, while rendering each year's roundup page on the server so search engines index 'Releases of 2026' and friends cleanly. |
<!-- sdlc:auto:end summary -->

<!-- sdlc:auto:start epics -->
## EPIC-001: Per-Year Release Roundup Pages

The site is organised primarily as year-by-year roundups (e.g. "Releases of 2026"), which form the main navigation and presentation of the curated AAA catalogue. Each year page lists the games released (or scheduled) in that year. Assumption: pages are server-rendered for SEO and responsive for mobile; scale is modest (hundreds of games per year).

### US-001: Browse a per-year release roundup

**Priority:** critical

> As a visitor, I want to open a page for a given year and see all curated AAA games for that year, so that I can see at a glance what is releasing in that year.

| ID | Given | When | Then |
|---|---|---|---|
| AC-001 | a year page exists for 2026 with curated games assigned to it | a visitor opens the "Releases of 2026" page | the page lists every game whose release falls in 2026, each showing at least its title and cover art, with no visitor account required |
| AC-002 | a year that has no curated games | a visitor opens that year's roundup page | the page loads and shows a clear empty-state message rather than an error |
| AC-003 | the year page is server-rendered | the page is requested | the game list content is present in the returned HTML so it is indexable by search engines |

### US-002: Navigate between years

**Priority:** high

> As a visitor, I want to move between the available release years, so that I can explore roundups for past and upcoming years.

| ID | Given | When | Then |
|---|---|---|---|
| AC-004 | multiple years contain curated games | a visitor is on any year roundup page | the page offers navigation to the other available years |
| AC-005 | a visitor requests a year that has no page or games | the page is opened | the site responds gracefully (empty state or clear not-found) without a broken page |

### US-003: View roundup pages on any device

**Priority:** medium

> As a visitor, I want the year roundup pages to adapt to my screen size, so that I can browse comfortably on mobile or desktop.

| ID | Given | When | Then |
|---|---|---|---|
| AC-006 | the responsive web app | a visitor opens a year roundup on a narrow mobile viewport | the layout reflows to remain readable and usable without horizontal scrolling |

## EPIC-002: Public Catalogue Filtering and Sorting

Rich browsing and filtering is the core value of the site. Within a year, visitors can filter and sort the curated catalogue by platform, genre, publisher, and date. Assumption: filtering/sorting applies within a year's roundup; no visitor account is involved.

### US-004: Filter games by platform, genre and publisher

**Priority:** critical

> As a visitor, I want to narrow a year's games by platform, genre and publisher, so that I can focus on the releases that matter to me.

| ID | Given | When | Then |
|---|---|---|---|
| AC-007 | a year roundup containing games across several platforms, genres and publishers | a visitor applies a platform filter | only games available on the selected platform remain visible |
| AC-008 | a visitor has applied a filter | they additionally select a genre and a publisher | the list shows only games matching all applied filters combined |
| AC-009 | a filter combination that matches no games | it is applied | the page shows a clear "no matching games" message and a way to clear filters |
| AC-010 | a visitor has applied one or more filters | they clear the filters | the full year list is shown again |

### US-005: Sort games by date within a year

**Priority:** high

> As a visitor, I want to sort a year's games by release date, so that I can see the earliest or latest releases first.

| ID | Given | When | Then |
|---|---|---|---|
| AC-011 | a year roundup with games on various dates | a visitor chooses to sort by date | games are ordered by their release date within the year |
| AC-012 | games with flexible dates (TBA, quarter-only or year-only) | the list is sorted by date | those games are ordered in a consistent, predictable position relative to exact-dated games rather than being dropped from the list |

### US-006: Filter state reflected in the page

**Priority:** low

> As a visitor, I want the applied filters and sort to be reflected in the page URL, so that I can share or revisit a filtered view.

| ID | Given | When | Then |
|---|---|---|---|
| AC-013 | a visitor has applied filters and a sort on a year page | they copy the page URL and open it again | the same filters and sort are applied to the displayed list |

## EPIC-003: Public Game Catalogue Browsing and Detail Pages

Visitors with no account can browse the curated catalogue and open a detail page for any game showing its full information. Assumption: each game record holds title, cover art, platforms, genre(s), publisher/developer, release date (supporting TBA/quarter/year-only), and a short description.

### US-007: View a game detail page

**Priority:** critical

> As a visitor, I want to open a game's detail page, so that I can see its cover, platforms, genres, publisher/developer, release date and description.

| ID | Given | When | Then |
|---|---|---|---|
| AC-014 | a curated game record | a visitor opens its detail page | the page shows the title, cover art, platforms, genre(s), publisher/developer, release date and short description |
| AC-015 | a game whose cover art is missing | its detail page is opened | a placeholder image is shown and the rest of the details still render |
| AC-016 | a game with a flexible release date (TBA, quarter or year-only) | its detail page is opened | the date is displayed in its flexible form (e.g. "TBA", "Q3 2026", "2026") rather than a misleading exact date |
| AC-017 | a detail page is server-rendered | it is requested | the game details are present in the returned HTML so the page is indexable by search engines |

### US-008: Reach a game from a roundup or filtered list

**Priority:** high

> As a visitor, I want to click a game in a year roundup or filtered list, so that I can open its full detail page.

| ID | Given | When | Then |
|---|---|---|---|
| AC-018 | a visitor viewing a year roundup or filtered list | they select a game entry | they are taken to that game's detail page |
| AC-019 | a visitor requests the detail page for a game that does not exist or was removed | the page is opened | the site returns a clear not-found response rather than a broken page |

## EPIC-004: Admin Authentication and Protected Area

A small trusted admin team signs in with email and password to reach a protected admin area for curating the catalogue. Assumption: admins are a small trusted team; 'AAA' classification is an admin judgment call, not an automated rule. Session security is stated as outcomes: passwords are stored hashed, sessions expire, and signing out ends the session.

### US-009: Admin signs in with email and password

**Priority:** critical

> As a admin, I want to sign in with my email and password, so that I can reach the protected admin area.

| ID | Given | When | Then |
|---|---|---|---|
| AC-020 | an admin account exists with valid credentials | the admin submits the correct email and password | they are signed in and taken to the admin area |
| AC-021 | an incorrect email or password is submitted | the admin attempts to sign in | sign-in is refused with a generic error that does not reveal which field was wrong |
| AC-022 | an admin password is stored | it is persisted in the database | it is stored only as a hash and never in plain text |

### US-010: Protect the admin area from unauthenticated access

**Priority:** critical

> As a admin, I want the admin area and its actions to be reachable only when signed in, so that visitors cannot add, edit or remove games.

| ID | Given | When | Then |
|---|---|---|---|
| AC-023 | a user who is not signed in | they request any admin page or admin action | access is denied and they are directed to sign in |
| AC-024 | a signed-in admin | their session has expired | further admin actions are denied until they sign in again |

### US-011: Admin signs out

**Priority:** high

> As a admin, I want to sign out of the admin area, so that my session ends when I am done.

| ID | Given | When | Then |
|---|---|---|---|
| AC-025 | a signed-in admin | they sign out | their session is ended and admin pages can no longer be accessed without signing in again |

## EPIC-005: Admin Catalogue Curation

Admins manually add, edit, remove and curate game records in the protected admin area, applying their own judgment of what counts as 'AAA'. Records support flexible release dates and cover art stored on Amazon S3. Assumption: cover images are stored on S3 and served via object storage/CDN; the relational database holds the records.

### US-012: View the curated catalogue overview

**Priority:** high

> As a admin, I want an overview list of all curated games, so that I can see what is in the catalogue and find records to edit.

| ID | Given | When | Then |
|---|---|---|---|
| AC-026 | the catalogue contains curated games | an admin opens the admin overview | the games are listed with key fields (e.g. title, year/release date, publisher) and a way to open each for editing |
| AC-027 | the catalogue is empty | an admin opens the overview | an empty-state is shown with a way to add the first game |

### US-013: Add a game record manually

**Priority:** critical

> As a admin, I want to manually add a new game record, so that I can list AAA releases based on my own judgment.

| ID | Given | When | Then |
|---|---|---|---|
| AC-028 | an admin on the add-game form | they enter title, platforms, genre(s), publisher/developer, release date and a short description and save | a new game record is created and appears in the admin overview and on the relevant year roundup |
| AC-029 | an admin tries to save a game without a title | they submit the form | the record is not saved and a validation message identifies the missing required field |

### US-014: Edit and override a game record

**Priority:** critical

> As a admin, I want to edit any field of a game record, including imported ones, so that I can correct or override details and apply my AAA judgment.

| ID | Given | When | Then |
|---|---|---|---|
| AC-030 | an existing game record (manually added or imported) | an admin changes one or more fields and saves | the updated values are persisted and reflected on the public pages |
| AC-031 | an admin edits an imported field value | they save the override | the admin's value is kept and is not overwritten by any later import of the same game |

### US-015: Remove a game record

**Priority:** high

> As a admin, I want to remove a game record, so that non-AAA or erroneous entries do not appear on the site.

| ID | Given | When | Then |
|---|---|---|---|
| AC-032 | an existing game record | an admin removes it after confirming | the record no longer appears in the admin overview or on any public year roundup or detail page |

### US-016: Set flexible release dates

**Priority:** high

> As a admin, I want to record a release date as an exact date, a quarter, a year only, or TBA, so that I can list games whose dates are not yet firm.

| ID | Given | When | Then |
|---|---|---|---|
| AC-033 | an admin editing a game's release date | they choose a precision of TBA, year-only, quarter, or exact date | the record stores the chosen precision and the game is assigned to the correct year roundup (or an unscheduled/TBA grouping where no year is known) |
| AC-034 | a game set to a specific quarter of a year | it is displayed publicly and sorted by date | it shows its quarter form (e.g. "Q2 2026") and sorts consistently among that year's games |

### US-017: Upload cover art stored on Amazon S3

**Priority:** medium

> As a admin, I want to upload cover art for a game, so that the game shows an image on roundups and detail pages.

| ID | Given | When | Then |
|---|---|---|---|
| AC-035 | an admin editing a game record | they upload a cover image | the image is stored in Amazon S3 and the game displays that cover on the public pages |
| AC-036 | a game with no uploaded cover | it is displayed on public pages | a placeholder image is shown in place of the missing cover |

## EPIC-006: IGDB Import of Game Records

Admins seed the catalogue by importing records from the external games database as a starting point, then curate them manually. Assumption: the external catalog is IGDB (Twitch/IGDB API) requiring a client ID and secret; imports are admin-triggered searches, not a continuous automatic sync. Store the client ID/secret as placeholders such as <igdb-client-id> and <igdb-client-secret>, never real values.

### US-018: Search IGDB and import a game as a starting point

**Priority:** high

> As a admin, I want to search the external IGDB database and import a matching game into the catalogue, so that I can seed a record without typing every field by hand.

| ID | Given | When | Then |
|---|---|---|---|
| AC-037 | valid IGDB/Twitch credentials are configured (client ID and secret held as placeholders such as <igdb-client-id>/<igdb-client-secret>) | an admin searches IGDB by game title | matching results are shown for the admin to choose from |
| AC-038 | an admin has selected a search result | they import it | a game record is created pre-filled with the available fields (title, cover art, platforms, genre(s), publisher/developer, release date, description) and appears in the admin overview for further curation |
| AC-039 | an imported game has fields the admin disagrees with | the admin edits them after import | the admin's edits are saved as overrides on the record |

### US-019: Admin-triggered imports only

**Priority:** medium

> As a admin, I want imports to run only when I trigger them, so that the catalogue reflects curated judgment rather than an automatic feed.

| ID | Given | When | Then |
|---|---|---|---|
| AC-040 | the IGDB integration | no admin has triggered a search or import | no games are added or changed automatically by any background sync |
| AC-041 | an admin imports a game that already exists in the catalogue | the import completes | existing admin overrides are preserved rather than silently overwritten |

### US-020: Handle IGDB connection problems gracefully

**Priority:** low

> As a admin, I want clear feedback when the external IGDB service is unavailable or credentials are invalid, so that I know import failed and can still curate manually.

| ID | Given | When | Then |
|---|---|---|---|
| AC-042 | IGDB credentials are missing or invalid, or the service is unreachable | an admin attempts a search or import | a clear error message is shown and no partial or corrupt record is created |
<!-- sdlc:auto:end epics -->
