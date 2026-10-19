# Salafis Day — Technical Requirements Document (TRD)

**Document status:** Active requirements and owner-approved technology decisions  
**Product:** Salafis Day  
**Intended readers:** Project owner, engineers, reviewers, and AI coding agents  
**Project approach:** Offline-first mobile application with a separate administrative web application and backend API

> **Important:** This document records decisions, requirements, and open questions. It is not evidence that a feature, package, migration, or deployment already exists. Engineers and AI agents must inspect the actual repository before claiming something is implemented.

---

## 1. Purpose and Authority

This TRD defines the technical boundaries, approved stack, planned behavior, implementation constraints, verification requirements, and decision process for **Salafis Day**.

### 1.1 Decision precedence

When instructions conflict, use the following order:

1. The project owner's latest **explicit** decision or instruction.
2. The **Approved Technology Register** and approved product scope in this TRD.
3. Verified repository code and configuration for determining **what exists today**.
4. Other project documentation, only where it does not conflict with the above.

If the codebase and approved decisions disagree, report the discrepancy and ask the owner before changing architecture or adding a dependency. The presence of a package in an inherited repository does **not** mean it has been approved for expanded use.

### 1.2 Requirements terminology

- **MUST / MUST NOT:** Mandatory engineering requirement.
- **SHOULD:** Strong recommendation, with any exception explained and reviewed.
- **MAY:** Optional and does not itself approve a technology or feature.
- **Approved:** Explicitly selected by the project owner.
- **TBD:** Decision remains open. Do not silently choose an implementation.
- **Proposed:** An option for discussion, **not** permission to install or implement it.

---

## 2. Product Goals and Approved Scope

Salafis Day is an Islamic daily-utilities app with **Bangladesh-first** product priorities and support for users **worldwide**. Its initial approved feature areas are **prayer times** and **Ramadan schedules**.

### 2.1 Approved geographic scope

- Prioritize Bangladesh in initial UX, location coverage, testing, regional timetable review, and operational support.
- Architect for global usage, including IANA time zones, different geographic coordinates, international location selection, and regional prayer-time settings.
- Do **not** assume every country follows one prayer calculation convention or one Ramadan moon-sighting decision.
- Default display language, translation policy, precise supported locations, and launch-country rollout are **TBD**. Geographic priority alone must not be treated as approval for an internationalization package.

### 2.2 Approved feature areas

| Feature area | Approved capability and direction | Implementation caveat |
|---|---|---|
| Prayer times | Daily prayer timetable, current/selected location, next prayer display, preferences, and reminders | **Calculation library and exact methods: TBD** |
| Ramadan | Suhoor/Fajr and Iftar/Maghrib schedules, region/date awareness, suitable reminders | Authoritative schedules, lunar-date policy, and adjustments: **TBD** |
| Offline behavior | Store locally available preferences and validated schedules; retain usable data when the connection drops | Fresh installation without data or an approved calculation engine must show an honest unavailable state |
| Accounts | Guest access by default; account creation/sign-in is optional for users | Authentication provider, sync identities, and login flow: **TBD** |
| Administration | Protected operations for scheduling data, regional settings, publishing, review, and audit | Admin authentication method and final workflow: **TBD** |

**Do not enlarge scope without explicit approval.** Quran reading, Hadith collections, du'a, Qibla compass, audio recitation, community/social features, public content website, and other previously discussed ideas are **not included in this approved feature scope**. They are not rejected forever; they require a separate product decision before design or implementation. Approval of R2 does not by itself approve an audio library.

### 2.3 Product principles

1. **Correctness before convenience:** Never display invented prayer times, moon-sighting confirmations, or adjustments.
2. **Offline-first:** Daily use should not routinely depend on an API request once valid local data is available.
3. **Transparent provenance:** Users and admins must be able to identify how a time was obtained and whether data is current.
4. **Privacy by default:** Optional accounts, least-permission location access, and minimal server collection.
5. **Maintainability:** A simple, modular backend and explicit ownership of local versus server data.
6. **Owner-controlled technology:** No unapproved libraries, services, or major infrastructure substitutions.

---

## 3. Approved Technology Register

**Only owner-approved selections appear in this register.** “Approved” denotes an architectural choice, not verification that it is already installed or deployed.

| Area | Approved technology | Intended use / approval boundary |
|---|---|---|
| Mobile application | **React Native + Expo** | Mobile app platform |
| Mobile styling | **NativeWind** | React Native styles; started by owner |
| Mobile offline database | **Expo SQLite** | Device-local durable storage |
| Mobile and backend ORM | **Drizzle ORM** | Type-safe persistence for Expo SQLite and Cloudflare D1 |
| Schema and SQL migration generation | **Drizzle Kit** | Generate/review migrations and support schema management |
| D1 migration execution | **Wrangler Migrations** | Apply reviewed migrations to Cloudflare D1 |
| Notifications | **Expo Notifications** | Local reminders and supported notification capabilities |
| Admin frontend | **React + Vite** | Separate authenticated admin SPA |
| Admin component library | **shadcn/ui** | Admin UI components, following verified repository setup |
| Backend API framework | **Hono** | API routing and request handling |
| Backend runtime and deployment | **Cloudflare Workers** | Host the Hono API |
| Server database | **Cloudflare D1** | Managed SQLite-based server database |
| Object/file storage | **Cloudflare R2** | Approved object store, used only for approved requirements |
| State management | **Zustand** | Local/client UI and session-adjacent state where appropriate |
| Server-state fetching/cache | **TanStack Query** | Request lifecycle, cache, refetch, and mutations |
| HTTP transport | **Axios** | API client transport |
| Data validation | **Zod** | Data shapes and validation at trust boundaries |
| Web/backend unit and integration tests | **Vitest** | Admin and Hono/API behavior; verify runtime compatibility |
| Mobile unit/component tests | **Jest** | React Native/Expo behavior; verify Expo compatibility |
| Web UI tests | **@testing-library/react** | Admin component behavior and accessible queries |
| Mobile UI tests | **@testing-library/react-native** | Mobile component behavior and accessible queries |
| Vitest test linting | **@vitest/eslint-plugin** | Current package for the requested Vitest ESLint plugin; recommended config and explicit rules in `docs/RULES.md` |
| Jest test linting | **eslint-plugin-jest** | Recommended config and explicit rules in `docs/RULES.md` |
| UI test linting | **eslint-plugin-testing-library** | Framework-compatible recommended rules and explicit overrides in `docs/RULES.md` |
| Package manager / workspace foundation | **pnpm** | Dependency management and, if used, pnpm workspaces |

**Scope clarification:** Existing use of a technology must be verified against each app. Approval of `Drizzle ORM` does not imply the same server and device schemas, nor does it make D1 and Expo SQLite auto-sync. Approval of `Expo Notifications` does not approve another push vendor or custom native alarm library.

### 3.1 Technologies and tooling still pending (not approved)

| Decision area | Status / restrictions |
|---|---|
| Prayer-time calculation package, formula, presets, and adjustments | **TBD** — no engine/preset selected |
| Location permission, geocoding, GPS, compass, or map packages | **TBD** — choose only when needed |
| Mobile routing/navigation package | **TBD** — inspect current Expo project first |
| Admin routing package | **TBD** — inspect current Vite project first |
| Account/authentication service and session library | **TBD** — do not infer a provider |
| Admin authorization library, identity provider, email/SMS provider | **TBD** |
| Ramadan lunar calendar service/data source and timetable publisher | **TBD** — source agreement required |
| Additional testing infrastructure | Core unit/component stack approved in Section 3; E2E tools, network-mocking packages, runtime adapters/presets, DOM environments, and coverage providers remain pending unless separately approved |
| Monorepo task runner, including Turborepo | **TBD** — pnpm approval does not include a task runner |
| Analytics, logging/monitoring SaaS, error tracking, rate-limit service | **TBD** |
| Background job/queue service, cache, external storage provider | **TBD** |
| CI provider, mobile release/build service, admin hosting arrangement | **TBD** except Workers API deployment |
| Additional CSS/UI libraries, icons, charting, internationalization libraries | **TBD** except selected styling/component solutions |
| Application-wide language conversion (JavaScript/TypeScript), if required | **Inspect existing code; ask before conversion or enforcing a new language policy** |

Existing transitive dependencies required by an approved framework may be used as part of its verified installation, but **do not use that exception to introduce a new standalone tool or framework**. Check before adding such dependencies.

---

## 4. Mandatory New-Technology Approval Protocol

> **Non-negotiable for AI coding agents:** If a task would introduce an unapproved technology, **stop and ask the project owner before installing, generating config, migrating, integrating, or deploying it**. After explicit approval, update this TRD's register **before** implementation.

### 4.1 Approval-required changes

- Adding any new direct project dependency, SDK, library, framework, or ORM.
- Introducing a cloud service, managed database, third-party API, authentication provider, storage service, or notification vendor.
- Adding a testing framework, CI tool, build/deployment tool, code generator, or monorepo orchestrator.
- Changing or replacing an approved technology.
- Adopting a materially different application architecture or a consequential major upgrade requiring a new compatibility approach.

Approval is **specific to the requested technology and stated scope**. Prior suggestions, sample code, ecosystem popularity, and silence are not approvals. Do not automatically install recommendations made in this document.

### 4.2 Required decision request

Before proposing an unapproved tool, provide the owner with:

1. **Name** and exact responsibility.
2. **Why** the existing approved tools are insufficient.
3. **Alternatives** (preferably including no new dependency where reasonable).
4. **Cost** and billing/card implications, where applicable.
5. **Compatibility** with Expo, Vite, Workers, D1, R2, and pnpm as relevant.
6. **Trade-offs**: maintenance, bundle size, performance, lock-in, licensing, and security.
7. A **direct approval question**; await an explicit answer.

Ask in the owner's preferred conversational language, while keeping **this TRD in English**.

**Reusable prompt:**

> I propose **[technology]** for **[specific task]** because **[reason]**. Alternatives are **[A]** and **[B/no new dependency]**. Estimated cost/billing requirement: **[details/unknown]**. Compatibility and risks: **[summary]**. Do you approve **[technology]** for **[scope]**? I will update `docs/TRD.md` before implementing it.

### 4.3 After a decision

- **Approved:** Add it to Section 3 with purpose and scope; update the related TBD item; document major consequences; only then implement.
- **Deferred:** Keep it out of the approved register and continue only with approved capabilities.
- **Rejected:** Do not add it. Record a concise rejection note in the relevant open-decision entry if necessary.
- **Ambiguous:** Ask a follow-up question. Do not treat ambiguity as approval.

The document must remain **free of document-version numbers and version-history sections**. Technical concepts such as content revisions, API compatibility, and database migrations are permitted and necessary; they are not document versioning.

---

## 5. System Architecture and Responsibilities

```text
                     Salafis Day

   ┌────────────────────────────┐   ┌──────────────────────────┐
   │ Mobile: React Native/Expo  │   │ Admin: React + Vite      │
   │ NativeWind                 │   │ shadcn/ui                │
   │ Zustand                    │   │ Zustand                 │
   │ TanStack Query + Axios     │   │ TanStack Query + Axios   │
   │ Expo SQLite + Drizzle      │   │ Zod input validation    │
   │ Expo Notifications         │   │ Protected management UI │
   └────────────┬───────────────┘   └──────────────┬───────────┘
                │ HTTPS API                         │ HTTPS API
                └──────────────────┬─────────────────┘
                                   ▼
                        ┌────────────────────┐
                        │ Hono API            │
                        │ Cloudflare Workers │
                        │ Zod + authorization│
                        └──────────┬─────────┘
                                   │
                         ┌─────────┴──────────┐
                         ▼                    ▼
                 ┌──────────────┐     ┌──────────────┐
                 │ Cloudflare D1│     │ Cloudflare R2│
                 │ Drizzle ORM  │     │ Object files │
                 └──────────────┘     └──────────────┘
```

### 5.1 Mobile responsibilities

- Render daily prayer/Ramadan information with a clearly indicated location and date.
- Persist device settings and available schedules in Expo SQLite.
- Use Zustand for local UI/state concerns; do not use in-memory Zustand as a replacement for durable records.
- Use TanStack Query for remote data orchestration, Axios for transport, and Zod for validating important external payloads.
- Schedule authorized local notifications with Expo Notifications.
- Maintain useful behavior during network loss; communicate when unavailable/stale.

### 5.2 Admin responsibilities

- Manage country/region/location reference data, reviewed prayer profiles and Ramadan schedules where approved.
- Provide protected draft/review/publish and controlled rollback flows for time-related data.
- Show who changed a value, why it changed, when it applies, and its authoritative source.
- Display publication and synchronization status where supported.
- **Do not** place D1/R2 credentials or privileged logic inside the Vite client bundle.

### 5.3 Backend responsibilities

- Offer public read endpoints, separately secured administrative write endpoints, and optional account endpoints once an auth design is approved.
- Enforce authentication, per-action authorization, and Zod validation at the API boundary.
- Access D1 through Drizzle ORM and R2 through Workers bindings.
- Publish approved location/time metadata and downloadable data only through clearly defined contracts.
- Avoid calculating prayer times on demand unless an implementation and method have been explicitly approved.

### 5.4 Technology boundaries

- D1 and Expo SQLite are **two separate databases**, despite sharing a SQLite family of engines.
- Drizzle is a database query/schema tool, not a sync engine.
- TanStack Query's in-memory cache is not automatically durable offline content.
- R2 stores objects, not primary relational records.
- The mobile app must not connect directly to privileged D1 or R2 bindings.

---

## 6. Repository and Module Organization

**Proposed layout; confirm the actual repository structure before moving files.** pnpm is approved, but no extra task runner is approved.

```text
salafis-day/
├── apps/
│   ├── mobile/                 # Expo + NativeWind
│   ├── admin/                  # React + Vite + shadcn/ui
│   └── api/                    # Hono + Cloudflare Workers
├── packages/                  # Only shared code that is actually needed
├── docs/
│   └── TRD.md
├── pnpm-workspace.yaml        # When using a pnpm workspace
└── package.json
```

Suggested separation of concerns within each app (directories only; not technology decisions):

- `features/prayer` — prayer UI and domain services.
- `features/ramadan` — Ramadan UI and domain services.
- `data` or `repositories` — local/remote persistence interfaces.
- `lib/api` — Axios clients and TanStack Query hooks.
- `db` — Drizzle schemas, migrations, and database initialization.
- `notifications` — permission handling and scheduling.
- `admin` / `modules` — privileged management behavior in the API.

Avoid creating premature shared abstractions. Shared Zod contracts or plain utility modules are reasonable where both clients actually require them, provided dependency/runtime compatibility is verified.

---

## 7. Prayer-Time Technical Requirements

### 7.1 Inputs and outputs

The application MUST handle, as applicable:

- A calendar date and the user's selected location.
- Geographical coordinates or a reliable location reference.
- An **IANA time zone** distinct from the user's current device time zone where appropriate.
- Calculation method/profile, juristic Asr configuration, and any reviewed adjustment rules **after approval**.
- A known source and validity range for any published timetable.
- Fajr, Sunrise, Dhuhr, Asr, Maghrib, and Isha values where available.
- Next-prayer and countdown state, including the next calendar day when needed.

### 7.2 Prayer calculation is unresolved

**No prayer-time calculation library, algorithm, preset, or default Fajr/Isha/Asr setting is currently approved.**

Until approved, developers MUST NOT:

- Install a calculation package (including Adhan JS or alternatives) without asking.
- Declare a country-wide preset as universally correct.
- Introduce arbitrary minute offsets, hardcoded fabricated times, or hidden fallback calculations.
- Claim offline computation works before it has been implemented and validated.

**Permitted planning approaches, not approved implementations:** device-side calculations after approval, verified published timetables synced for offline use, or a reviewed hybrid. Ask the owner to choose the engine, default calculation profile(s), and validation reference before implementing this domain logic.

### 7.3 Location and global behavior

- A user MUST have a way to choose or correct their location without granting GPS permission; exact UI and supporting dependency are TBD.
- GPS-based automatic detection is **proposed**, not approved as a new package.
- Keep both location and associated zone/method selection visible and user-correctable.
- Cover Bangladesh regional variations first; design data models to accept additional countries without schema redesign.
- Reevaluate cached schedules after relevant location, date, time-zone, or method changes.
- Carefully handle daylight-saving transitions and travel across zones.

### 7.4 Data integrity and presentation

- Show a meaningful status for **verified**, **calculated**, **cached**, **stale**, or **unavailable** time data, without implying false certainty.
- Never present a cached timetable as newly verified.
- Require comparison against reviewed regional reference schedules across seasons before release.
- Preserve the original source, profile identity, adjustment metadata, and review timestamp for published overrides.

---

## 8. Ramadan Timetable Technical Requirements

### 8.1 Required information

- Region-aware daily Suhoor/Fajr and Iftar/Maghrib information.
- Correct date and time-zone presentation.
- Ramadan beginning/end status based on the **approved** calendar or announcement source.
- Explicit distinction between **Fajr start**, **Sunrise**, **Maghrib/sunset**, and any optional **Imsak** convention; do not silently treat them as identical.
- Clear handling for days that are not in Ramadan or for regions lacking a verified timetable.

### 8.2 Source and review policy

- Choose official/authoritative source(s), country/region precedence, and Hijri date policy with the owner **before publishing**.
- Store source identification, region applicability, reviewer, review status, effective dates, and publication status.
- Treat moon-sighting announcements or regional calendar updates as **reviewable changes**, not assumptions inferred from a fixed date conversion formula.
- Never invent a schedule for an unsupported region.
- If using derived times in a future approved hybrid system, distinguish them from reviewed published schedules.

### 8.3 Admin update requirements

- Stage changes as **draft → reviewed → published**.
- Require an authenticated, authorized administrator and record an audit event for publication or rollback.
- Keep the last valid published timetable available while a new one is reviewed.
- Define how corrections reach mobile devices, including previously downloaded/offline schedules.
- Support a prominent correction/notice mechanism if published times are subsequently found wrong; exact implementation TBD.

---

## 9. Local Persistence and Offline-First Behavior

### 9.1 Device database

Use **Expo SQLite + Drizzle ORM** for local durable data as appropriate. Candidate data domains:

- Device preferences (selected location, display preferences, method/profile after approval).
- Published/verified timetable entries and associated provenance.
- Optional future calculated timetable cache once computation is approved.
- Notification scheduling metadata and deduplication state.
- Sync manifest/revision state and last successful verification timestamp.
- Guest settings and, after account approval, user-associated sync state.

These are **logical domains**, not approved physical tables or final schemas. The schema must be reviewed before migrations are generated.

### 9.2 Offline acceptance rules

- On **offline reopen**, display previously stored valid timetable data and indicate its coverage/freshness.
- A guest user MUST be able to use the supported local core without creating an account.
- A **fresh installation without downloaded timetable data or an approved calculation engine** may legitimately show **unavailable**; it must not invent times.
- A failed sync MUST NOT destroy the last valid locally stored dataset.
- Local settings MUST survive ordinary app restarts and transient network loss.
- Expired region-specific data MUST NOT be silently repurposed for a different location or date.

### 9.3 Synchronization contract

If server-published schedules are used, design a contract covering:

- Region identifiers and coverage dates.
- Data revision, format compatibility, publication timestamp, and source metadata.
- Checksums/integrity checks and expected download size where useful.
- Atomic apply/rollback on device and retry on failure.
- Idempotent fetches, cache invalidation, corrections, and expiration.
- Conflict handling for locally stored preferences versus optional account sync.

**The sync protocol and exact API contracts remain design decisions, not pre-approved new technologies.** Basic HTTPS/JSON and the approved Axios/Zod stack may be used without introducing another sync library.

---

## 10. Notification Requirements

**Expo Notifications is approved.** Prayer and Ramadan reminders should be device-scheduled when practical, rather than relying only on real-time push from a server.

- Request user permission at an appropriate point; handle denial gracefully.
- Allow independent enable/disable preferences for prayer and Ramadan reminders.
- Schedule from the **same validated timetable source** shown by the app; do not create a second, inconsistent time-calculation path.
- Prevent duplicate pending schedules after repeated launches or preference changes.
- Recalculate or reschedule as appropriate when location, zone, method, OS permissions, or published data changes.
- Test foreground, background, terminated app, reboot, battery optimization, and time-zone changes on actual Android/iOS devices.
- Respect platform limits and permission requirements for precise scheduled notifications.
- Do not promise guaranteed exact delivery across operating systems and device manufacturers.

A remote push provider, background task framework, and custom native scheduling extension are **not approved**. Raise a separate technology request if validation shows they are necessary.

---

## 11. Backend API Design

### 11.1 General requirements

- Host the Hono API on **Cloudflare Workers**.
- Separate public read operations from protected admin writes and optional authenticated account operations.
- Use **Zod** to validate untrusted request bodies, parameters, and important external data before persistence.
- Use **Drizzle ORM** to access **D1** through server-side bindings.
- Return predictable error shapes and meaningful status codes; do not expose internal stack traces.
- Paginate potentially large lists, prevent accidental bulk reads, and avoid unnecessary mobile requests.
- Apply per-operation authorization, abuse protection, and suitable audit logging.
- Prefer idempotent updates for retryable publishing/sync operations.

### 11.2 Illustrative endpoint families (not a finalized route contract)

```text
GET    /health
GET    /locations
GET    /prayer-profiles
GET    /ramadan/schedules
GET    /sync/manifest
POST   /admin/ramadan/schedules
POST   /admin/ramadan/schedules/{id}/publish
GET    /admin/audit-events
```

Route naming, parameters, authorization mechanism, response payloads, and compatibility policy are **TBD pending API design review**. Do not generate these routes simply because they appear here.

### 11.3 Client-side HTTP state

- Use **Axios** for HTTP transport.
- Use **TanStack Query** for server-state lifecycle, invalidation, and mutations.
- Use **Zustand** for local user-interface state and non-server UI preferences.
- Persist durable information in **Expo SQLite**, not solely in Query caches or Zustand memory.
- Keep request interceptors, cancellation, retries, token handling (once approved), and error normalization centralized.
- Never embed service credentials, private D1/R2 bindings, or admin secrets in mobile/web client code.

---

## 12. Database Architecture and Migration Policy

### 12.1 Server database: Cloudflare D1

- **D1 + Drizzle ORM** are approved for structured backend data.
- Candidate logical domains: countries/regions/locations, prayer profiles, Ramadan schedule publications, reviewed schedule entries, administrative identity/roles (after auth approval), audit events, and synchronization metadata.
- Use indexes based on actual read paths and query plans.
- Preserve referential integrity and provenance for time-critical published records.
- Avoid storing raw passwords, authentication secrets, or unnecessary precise location histories.
- Only the server accesses D1 bindings.

### 12.2 Local database: Expo SQLite

- Maintain a **separate device schema** that mirrors only the data needed offline.
- Use Drizzle ORM with the Expo SQLite integration where verified compatible with the installed Expo environment.
- Device-side caches and preferences are not necessarily 1:1 copies of server tables.
- Review on-device migration compatibility and recovery behavior before shipping schema changes.

### 12.3 Approved migration tools and operating procedure

**Drizzle Kit and Wrangler Migrations are approved.** Use the following controlled model:

1. Review the proposed schema change and implications for stored data.
2. Update the relevant **Drizzle schema**.
3. Generate SQL migrations with **Drizzle Kit**.
4. **Review generated SQL** for unsupported changes, destructive statements, indexes, and data backfills.
5. Test the D1 migration locally using the **Wrangler D1 migrations workflow**.
6. Run the applicable schema and API checks; verify representative data remains correct.
7. Back up or plan recovery for important production data before destructive operations.
8. Apply reviewed migrations to the intended Cloudflare D1 environment using **Wrangler** only after deployment approval and environment verification.
9. Verify schema state and application compatibility after deployment.

Use **one authoritative D1 migration history**. Configure Drizzle-generated migration output in a format/layout compatible with the chosen Wrangler D1 migration process. Do **not** mix uncontrolled `drizzle-kit push`, manual production SQL, and Wrangler migration tracking.

**Expo SQLite migrations are separate from D1 migrations.** Drizzle Kit may generate local schema migrations, but the exact in-app migration application/initialization procedure must be verified against the installed Expo/Drizzle integration before implementation. **Wrangler does not migrate the user's on-device SQLite database.**

**Safety requirement:** Never apply production migrations, reset a database, delete a table, or erase user data without explicit owner authorization and a reviewed recovery plan.

---

## 13. R2 Object Storage

**Cloudflare R2** is approved as the object store.

- Use R2 only for files justified by the approved feature scope (for example, published timetable export files or approved static assets).
- Keep relevant file metadata, object identity, approval state, and references in D1 if appropriate.
- Upload, overwrite, and delete operations require authenticated and authorized backend controls.
- Validate object type, size, ownership, and permitted content before storing it.
- Decide whether each object is public, private, or access-controlled; do not make a private bucket publicly writable.
- Establish cache behavior, retention, cleanup, and access logging appropriate to actual usage.
- Track R2 storage/operation costs; a free allowance does not mean unrestricted free service.

Large Quran audio libraries and media features are **outside the currently approved feature scope**, even though R2 could support them later.

---

## 14. Authentication, Authorization, and Privacy

### 14.1 Guest-first mobile behavior

- Mobile usage MUST NOT require sign-in for the approved local prayer/Ramadan features.
- Users MAY opt into an account when a secure authentication design is explicitly approved.
- Offline guest state must remain functional without server identity.
- Define optional account synchronization, merge/conflict behavior, account deletion, and recovery **before** implementing login or cloud sync.

### 14.2 Admin requirements

- Administrative write operations MUST be protected by server-side authentication and authorization.
- Separate at least the conceptual abilities to view, edit, review, and publish schedules; final role model is TBD.
- Log important changes, sources, reviewer actions, publication events, and rollback events.
- Protect sessions/tokens, use secure transport, and prevent cross-site request forgery where applicable to the selected architecture.
- **Authentication technology remains TBD.** Do not assume a provider or install one because optional accounts were approved.

### 14.3 Privacy

- Request only necessary location permissions, with meaningful explanation and a manual alternative.
- Avoid storing exact live GPS trails or third-party tracking identifiers without an approved need and user transparency.
- Store secrets with Cloudflare's appropriate server-side secret/binding facilities; never commit secrets or place them in Vite/Expo public environment variables.
- Establish data retention, account deletion, incident response, and access revocation policies before public launch.

---

## 15. Quality Assurance and Testing

**The owner has approved Vitest for web/backend, Jest for React Native/Expo, their corresponding Testing Libraries, and the test lint plugins in Section 3.** Approval does not mean these tools are installed or configured. Follow `docs/RULES.md` for mandatory Zod validation and test/lint requirements. Additional testing infrastructure requires separate approval.

### 15.1 Testing-tool decisions

| Area | Suggested candidate | Reason / fit | Approval status |
|---|---|---|---|
| Backend unit and API tests | **Vitest** | Hono behavior, validation and authorization; verify Workers-runtime support separately | **Approved** |
| Admin component tests | **Vitest + @testing-library/react** | UI interaction, validation, states, and controlled data behavior | **Approved** |
| Mobile unit/component tests | **Jest + @testing-library/react-native** | React Native/Expo component behavior | **Approved** |
| Test linting | **@vitest/eslint-plugin / eslint-plugin-jest + eslint-plugin-testing-library** | Recommended configs plus required rule severities in `docs/RULES.md` | **Approved** |
| Admin end-to-end tests | **Playwright** | Browser flows for protected admin editing and publishing | **Proposed — not approved** |
| Mobile end-to-end tests | **Maestro** or **Detox** | Real-device/emulator flows; choose one after comparing compatibility | **Proposed — not approved** |
| Network mocking (only if needed) | **Mock Service Worker (MSW)** | Predictable HTTP behaviors in tests | **Proposed — not approved** |

**Setup order:** verify Expo, Vite, Workers, ESLint and test-library compatibility, configure the approved unit/component stack, then propose E2E tools only when stable flows require them. Runtime adapters/presets, DOM environments, coverage providers, and other direct supporting packages require explicit approval if not already registered. Do not claim a Node-only test validates Workers/D1/R2 behavior. No numeric coverage threshold is approved.

### 15.2 Mandatory verification scenarios (regardless of tool choice)

**Prayer and time correctness**
- Multiple Bangladesh regions, different seasons, date changes, leap days, and year boundaries.
- Correct IANA time zone, device zone mismatch, and daylight saving changes outside Bangladesh.
- Different approved prayer profiles and reviewed regional adjustments once selected.
- Missing location, denied GPS permission, unsupported region, stale data, and manual location selection.

**Ramadan**
- Date boundaries, regional month-start announcements, timetable corrections, stale schedules, and explicit Imsak/Fajr distinction.
- Admin review/publish/rollback reflected correctly in synced client data.

**Offline and sync**
- Offline reopen with a valid downloaded timetable.
- Fresh offline installation without a valid dataset shows a correct unavailable state.
- Interrupted/corrupt download, repeated sync, changed location, schema migration, and recovery without losing the previous valid dataset.

**Notifications**
- Permission acceptance/denial, duplicates, rescheduling, reboot, app termination, OS restrictions, timezone travel, and date rollovers.

**Security**
- Unauthorized admin mutations rejected by the API.
- Invalid Zod payloads rejected consistently.
- No sensitive bindings/secrets leaked to clients.
- R2 object permission enforcement and audit logging where applicable.

### 15.3 Test completion evidence

Every implementation report MUST state:

- What was changed and where.
- What tests/type checks/manual checks were actually run.
- Which environment or device was tested.
- Any failures, missing permissions, skipped checks, or unresolved decisions.

Never claim a test passed if it was not executed.

---

## 16. Environments, Deployment, and Operations

- **Mobile:** React Native + Expo. Actual build/release service and app-store pipeline: **TBD**.
- **Admin:** React + Vite. Hosting decision: **TBD**; do not confuse backend Workers approval with automatic approval of a particular admin deployment setup.
- **API:** Hono on **Cloudflare Workers**, with approved D1 and R2 bindings.
- **Package management:** pnpm; pnpm workspaces may be used where an actual monorepo exists. Turborepo or another orchestrator requires separate approval.
- Separate local, preview/staging, and production configurations as appropriate; do not mix production credentials into local client builds.
- Configure distinct environment bindings, migrations, and access policies before a production rollout.
- Keep deployment steps reversible where feasible, and document migration rollback or recovery procedures.
- Observe actual Cloudflare pricing, request quotas, D1 operation/storage limits, and R2 usage rather than assuming that “free tier” means free at any scale.
- Define monitoring, operational ownership, uptime targets, backup cadence, and incident response before public launch. New third-party monitoring tools require approval.

**Do not invent shell scripts, Wrangler bindings, bucket IDs, domain names, environment variable names, or npm scripts.** Read the actual repository and configuration first.

---

## 17. Acceptance Criteria for Initial Approved Scope

The initial prayer/Ramadan implementation is not release-ready until the following are satisfied:

1. The user can select an intended location and understand which location/time zone is in use.
2. Prayer times come from a **previously approved and validated** calculation or timetable source, with no silent arbitrary overrides.
3. Ramadan schedules identify their region, covered date(s), and provenance; month-start handling follows an approved policy.
4. Previously stored valid data remains usable during network outage, with clear stale/unavailable indicators.
5. Notifications based on the displayed validated schedule are functional within platform constraints and have been tested on target devices.
6. Admin-controlled publication requires valid credentials and server-side authorization; changes are reviewable and auditable.
7. D1 migrations have been generated, reviewed, tested, and executed through the approved Drizzle Kit + Wrangler process.
8. Schema updates do not silently lose device-local preferences or previously downloaded valid schedules.
9. No unapproved technology has been installed, integrated, or silently substituted.
10. All applicable validation/testing evidence is recorded, including unresolved limitations and outstanding product decisions.

Exact release milestones, success metrics, supported device minimums, and performance targets are **TBD**; they should be agreed with the owner rather than invented.

---

## 18. Open Decisions — Ask Before Implementing

| ID | Question / action requiring owner confirmation | Current state |
|---|---|---|
| DEC-PRAYER | Which prayer calculation engine and location-specific defaults/reference schedules will be used? | **TBD** |
| DEC-RAMADAN | Which authoritative Ramadan timetable and moon-sighting sources, and what precedence per country? | **TBD** |
| DEC-LOCATION | Which location/GPS/geocoding package (if any), manual selection UX, and initial region list? | **TBD** |
| DEC-AUTH | Which admin authentication/session mechanism, optional user auth provider, and role model? | **TBD** |
| DEC-NAV | Which mobile/admin routing approach matches the existing repositories? | **TBD** |
| DEC-TEST | Core stack approved; which additional runtime adapters, coverage providers, E2E or network-mocking tools are needed? | **Core stack approved — additional infrastructure TBD; see Section 15** |
| DEC-DEPLOY | How will the admin be hosted and the mobile app be built/distributed? | **TBD** |
| DEC-LANGUAGE | Default UI language and global language strategy; TypeScript migration policy if applicable | **TBD where not already established in code** |
| DEC-SYNC | Exact offline timetable package format, manifest contract, and sync conflict rules | **TBD** |
| DEC-METRICS | Supported OS versions, test devices, latency/SLO targets, backups, and monitoring approach | **TBD** |

### Approved decisions already reflected in the register

- React Native + Expo + NativeWind for mobile.
- React + Vite + shadcn/ui for the admin dashboard.
- Hono on Cloudflare Workers, backed by D1 and R2.
- Drizzle ORM, Drizzle Kit, and Wrangler Migrations.
- Expo SQLite for the mobile local database.
- Expo Notifications.
- Zustand, TanStack Query, Axios, Zod, and pnpm.
- Vitest (web/backend), Jest (mobile), corresponding Testing Libraries, and test lint plugins in Section 3; mandatory validation and quality rules in `docs/RULES.md`.
- Bangladesh-first, globally capable product direction.
- Prayer times and Ramadan as the currently approved feature areas.
- Optional user accounts / guest-first mobile use.

---

## 19. Required AI Agent Working Procedure

For every non-trivial coding task:

1. Read `docs/TRD.md`, `docs/PRD.md`, `docs/RULES.md`, and the relevant repository instructions (`AGENTS.md`, `CLAUDE.md`, etc. **only if they exist**).
2. Inspect the current files/configuration. Distinguish **implemented** functionality from **planned** requirements.
3. Identify the exact change and which approved feature/technology it belongs to.
4. Check whether a new technology, cloud resource, major refactor, or unapproved scope is required.
5. If yes, **ask the owner and wait**. Provide alternatives, costs, compatibility, and trade-offs.
6. After approval, update the **Approved Technology Register** and impacted requirements **before** making the code change.
7. Plan small, reversible implementation steps; do not invent architecture or migrate unrelated code.
8. Implement with validated inputs, restricted secrets, controlled permissions, and clear data provenance.
9. Run the checks actually available in the repository; do not add a testing tool without approval.
10. Summarize changed files, executed checks, results, risks, and outstanding TBD items.

### Prohibited autonomous behavior

- Installing a new framework/library because it is “best practice” without owner approval.
- Selecting a prayer calculation method, religious schedule source, or Ramadan date convention without verification and owner approval.
- Treating examples, suggestions, or a dependency already found in a lockfile as automatic approval for expanded use.
- Replacing the approved D1/Workers/R2 architecture or adding a third-party backend service without permission.
- Running destructive D1 migrations or clearing Expo SQLite data without explicit authorization.
- Claiming exact notification delivery guarantees or universal prayer-time accuracy.
- Adding out-of-scope Quran/Hadith/Qibla/social features without a product-scope decision.
- Writing document version numbers or maintaining a document version-history section in this TRD.

**This is the controlling technical requirements document. Unresolved choices stay TBD until the project owner explicitly decides otherwise.**
