# Planning

> Maintained by QuorQ AI SDLC. Text between `sdlc:auto` markers is regenerated; anything outside them is yours and is kept.

<!-- sdlc:auto:start narrative -->
### EPIC-001 · approved · source: planning:EPIC-001

Organise the site primarily as year-by-year release roundups forming the main navigation and presentation of the curated catalogue.

Assumption noted: pages are server-rendered for SEO and responsive for mobile; scale is modest (hundreds of games per year).

**Related:** US-001, US-002, US-003

### EPIC-002 · approved · source: planning:EPIC-002

Provide rich browsing via filtering and sorting of a year's curated catalogue by platform, genre, publisher and date -- the core value of the site.

Assumption noted: filtering/sorting applies within a year's roundup; no visitor account is involved.

**Related:** US-004, US-005, US-006

### EPIC-003 · approved · source: planning:EPIC-003

Let account-free visitors browse the curated catalogue and open a detail page for any game showing its full information.

Assumption noted: each game record holds title, cover art, platforms, genre(s), publisher/developer, release date (supporting TBA/quarter/year-only), and a short description.

**Related:** US-007, US-008

### EPIC-004 · approved · source: planning:EPIC-004

Give a small trusted admin team email/password sign-in to a protected admin area for curating the catalogue.

Assumption noted: admins are a small trusted team; 'AAA' classification is an admin judgment call, not an automated rule. Session security stated as outcomes: passwords stored hashed, sessions expire, and signing out ends the session.

**Related:** US-009, US-010, US-011

### EPIC-005 · approved · source: planning:EPIC-005

Let admins manually add, edit, remove and curate game records in the protected admin area, applying their own judgment of what counts as 'AAA'.

Records support flexible release dates and cover art stored on Amazon S3. Assumption noted: cover images are stored on S3 and served via object storage/CDN; the relational database holds the records.

**Related:** US-012, US-013, US-014, US-015, US-016, US-017

### EPIC-006 · approved · source: planning:EPIC-006

Let admins seed the catalogue by importing records from IGDB as a starting point, then curate them manually.

Assumption noted: the external catalog is IGDB (Twitch/IGDB API) requiring a client ID and secret; imports are admin-triggered searches, not a continuous automatic sync. Credentials are held as placeholders such as <igdb-client-id>/<igdb-client-secret>, never real values.

**Related:** US-018, US-019, US-020
<!-- sdlc:auto:end narrative -->
