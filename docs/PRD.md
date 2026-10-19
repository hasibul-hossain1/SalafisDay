# Salafis Day — Product Requirements Document (PRD)

**Product:** Salafis Day  
**Status:** Product direction approved; Phase 1 requirements defined; unresolved decisions explicitly identified  
**Audience:** Product owner, designers, developers, reviewers, and AI coding agents  
**Related technical reference:** [`TRD.md`](TRD.md)

> **Document policy:** This PRD is the product-level source of truth for *what* to build and *why*. The TRD is the technical source of truth for approved technologies and *how* to implement them. Neither document proves that a feature is already implemented. No document version numbers or version-history sections are required.

---

## 1. Executive Summary

**Salafis Day** is an Islamic daily utility application, designed first for users in Bangladesh while maintaining an architecture that can support other countries in the future. The product should be useful, trustworthy, easy to navigate, privacy-conscious, and dependable when internet connectivity is limited.

Development will follow **owner-approved, incremental phases**. Only the requirements explicitly authorized for the active phase may be implemented. Each subsequent phase will be defined by the product owner at that time. **Do not invent or pre-approve later-phase features, delivery dates, or roadmaps.**

### Current Phase: Phase 1

**Only two user-facing feature areas are in scope:**

1. **Salat (prayer) times** — display the five daily prayers for a selected location and date, with essential context needed to interpret the times correctly.
2. **Sahri and Iftar times** — display the applicable Ramadan fasting timetable for a selected location and date, with clear distinctions between Fajr/Sahri cutoff conventions and Maghrib/Iftar time.

Minimum supporting capabilities—such as location selection, data provenance, offline retention, error/empty states, and restricted timetable administration—are allowed **only when necessary to deliver these two features safely and reliably**. They are not permission to introduce unrelated product features.

---

## 2. Product Vision and Goals

### 2.1 Vision

Offer a clean and dependable way to check daily salat times and Ramadan Sahri/Iftar times, with reliable location context, transparent data sources, and useful behavior when offline. Establish a maintainable foundation that can be extended through future, explicitly scoped phases.

### 2.2 Primary goals

| ID | Goal | Product interpretation |
|---|---|---|
| G-01 | **Trustworthy time information** | Never show invented or unreviewed times as authoritative. |
| G-02 | **Fast, simple access** | Users can find today's relevant times without navigating unrelated sections. |
| G-03 | **Bangladesh-first usability** | Prioritize validation, place selection, and timetable coverage relevant to Bangladesh. |
| G-04 | **Global-ready foundation** | Support location/time-zone concepts needed to extend beyond Bangladesh without assuming worldwide data coverage at launch. |
| G-05 | **Offline-first experience** | Previously available and valid schedule data remains readable without an internet connection. |
| G-06 | **Guest-first privacy** | Basic use does not require account registration or unnecessary personal information. |
| G-07 | **Controlled growth** | Ship only the approved phase, validate it, then seek direction for the next phase. |
| G-08 | **Maintainable quality** | Prioritize accessibility, testability, clear ownership of data, and reversible releases. |

### 2.3 Non-goals for the current phase

This PRD does **not** authorize a Quran reader, Hadith library, du'a content, Qibla compass, tasbih, audio streaming, articles, fatwas, social feed, messaging, tracking journals, gamification, advertisements, payments, or a public SEO website. These are not future commitments: the owner may or may not select them in later phases.

**Technology approval does not imply feature approval:** Expo Notifications is an approved library, but end-user reminder/adhan functionality is **not a Phase 1 delivery requirement** unless the owner separately expands the Phase 1 product scope. Similarly, R2 availability does not justify adding audio/media features.

---

## 3. Users and Product Positioning

### 3.1 Intended audience

- Primary: Muslims in Bangladesh who want clear daily prayer times and Ramadan Sahri/Iftar information.
- Secondary: Muslims outside Bangladesh where the product can supply a verified or approved method/source of time information.
- The Salafis Day name conveys the product's intended positioning; this phase must not introduce unsupported religious rulings or suggest that a single calculation preset is universally accepted by Salafi scholars.

### 3.2 Key user needs

| Persona / situation | Need | Product response |
|---|---|---|
| Daily user | “What time are today's five prayers?” | A readable schedule with location, date, and provenance. |
| Ramadan user | “When does Sahri end and when is Iftar?” | Clearly labeled fasting times for the right date and place. |
| Traveler or relocated user | “Am I seeing the correct city's times?” | Explicit, correctable location and time-zone context. |
| Offline user | “Can I view my previously available timetable?” | Local persisted data and a visible validity/freshness state. |
| New user | “Can I use this without signing up?” | Guest-first flow and a simple path to obtain valid schedules. |
| Schedule administrator | “Can I correct a timetable without silently changing public data?” | Reviewed, authorized changes with publication records and an audit trail. |

---

## 4. Phase-Based Delivery Policy

### 4.1 Rules for every phase

1. One phase is **active** at a time. **Phase 1 is active now.**
2. Only the owner defines or approves the next phase's feature scope.
3. Do not plan implementation work for Phase 2 or later based on assumptions or examples from past conversations.
4. Separate **features**, **supporting infrastructure**, and **technical options**. Approval of one does not approve the others.
5. Keep changes small, reviewable, tested, documented, and backward-compatible where practical.
6. Every phase must have acceptance criteria, dependency decisions, testing evidence, and a deliberate release decision.
7. Update this PRD when the owner approves a product requirement; update the TRD when the owner approves a technology or technical architecture change.

### 4.2 Phase status

| Phase | Status | Scope |
|---|---|---|
| **Phase 1** | **Active** | Salat times and Ramadan Sahri/Iftar times, plus strictly required supporting behavior. |
| **Later phase(s)** | **Not defined** | Await the owner's separate explanation and explicit approval. No implied features or deadlines. |

### 4.3 Phase change procedure

Before starting any future phase, prepare a short proposal with: user problem, outcome, in-scope/out-of-scope capabilities, dependencies, risks, acceptance criteria, and effects on offline data, privacy, accessibility, and existing users. Ask the owner to approve the proposal. Only after approval should the relevant PRD sections, any technology decisions in the TRD, and the implementation backlog be updated.

---

## 5. Phase 1 Scope and Product Behavior

### 5.1 Feature F-01: Salat Times

**Outcome:** A user can reliably identify the day's prayer times for the chosen location.

**Functional requirements**

| ID | Requirement | Priority |
|---|---|---|
| PR-01 | Display **Fajr, Dhuhr, Asr, Maghrib, and Isha** for the relevant civil date and chosen location. | Must |
| PR-02 | Display a clear **location name** and **date** alongside the schedule. | Must |
| PR-03 | Let a user **manually select or correct** the location; do not make GPS permission the only path. | Must |
| PR-04 | Represent the applicable **IANA time zone** correctly and avoid treating device time zone as the selected place's time zone. | Must |
| PR-05 | Show which approved source or calculation profile produced the displayed times, using understandable wording. | Must |
| PR-06 | Distinguish valid/current, locally cached, stale/expired, and unavailable data without false certainty. | Must |
| PR-07 | Preserve the selected location and other approved user preferences across app restarts. | Must |
| PR-08 | Handle change of day and selected location without displaying a previous city's schedule as the current city's schedule. | Must |
| PR-09 | Display sunrise as optional supplementary information **only if verified and approved as part of the prayer screen**; it is not a sixth prayer. | Should / confirm with owner |

**Data source gate:** The calculation engine, jurisprudential Asr setting, Fajr/Isha convention, reference timetable source, and country/region defaults remain **TBD** in the TRD. No implementation may fabricate times or silently select a method. If the approved strategy uses published data rather than device calculation, the product must be honest about its available dates and supported regions.

**Acceptance criteria**

- Given a location with a verified/approved time source, the user can see all five labeled prayer times for the selected date.
- A user can tell which location and time zone are represented and can change the location without granting GPS access.
- When the location/date changes, no mismatched previous-location results appear as valid current data.
- When required data is missing, the app says that times are unavailable instead of presenting guessed values.
- Times are consistently formatted; a chosen 12-hour/24-hour display convention, if offered, does not change the underlying instant.
- Approved Bangladesh test locations and reference schedules have been compared across representative dates before release.

### 5.2 Feature F-02: Ramadan Sahri and Iftar Times

**Outcome:** A user can identify the correct fasting-related schedule for a specific date and applicable locality.

**Functional requirements**

| ID | Requirement | Priority |
|---|---|---|
| RM-01 | Display **Sahri end** and **Iftar time** for applicable Ramadan dates and supported locations. | Must |
| RM-02 | Clearly label the underlying meaning and source for Sahri end, Fajr start, sunset, Maghrib, and any separately defined Imsak time; do not conflate them. | Must |
| RM-03 | Provide unambiguous **civil date**, **region**, and **time zone** for each displayed schedule. | Must |
| RM-04 | Use only an **approved, traceable timetable/calendar policy**, accounting for regional Ramadan start/end decisions. | Must |
| RM-05 | Display a clear state when Ramadan dates are not confirmed, the requested date is outside covered dates, or the chosen region has no verified timetable. | Must |
| RM-06 | Make corrected published schedules retrievable while preserving the previous valid copy until replacement is safely applied. | Must when server updates are used |
| RM-07 | Keep prayer and Ramadan time presentation internally consistent where they represent the same event, or explain an authorized difference in conventions. | Must |

**Source policy gate:** Approved timetable publisher(s), country/region precedence, Ramadan start/end and moon-sighting policy, local offsets (if any), and handling of differences among sources remain **TBD**. The product must not infer religious authority from a generic astronomical conversion alone.

**Acceptance criteria**

- A user with covered date/location can read both Sahri-end and Iftar times with correct labels.
- A selected location outside verified coverage produces an informative unavailable state rather than a copied Bangladesh timetable.
- If Ramadan begins on a different civil date in different jurisdictions, the correct approved regional policy is reflected or the status is marked pending.
- A released correction can supersede outdated local content only after integrity validation; no half-applied schedule is shown.
- Users are not led to interpret a voluntary early precaution/Imsak offset as the obligatory Fajr boundary without explicit labeling and reviewed rationale.

---

## 6. Phase 1 Experience and User Flows

### 6.1 Initial use (guest-first)

1. Open Salafis Day without being forced to register or sign in.
2. Choose or confirm a location manually. GPS-assisted selection, if later approved, may be offered as a convenience rather than a requirement.
3. Obtain an approved schedule or a clear explanation when a first-time offline installation has no usable data.
4. Land on a simple, time-focused screen showing the selected place and relevant information.

### 6.2 Daily prayer use

1. Open the app.
2. See the five prayer times for the correct selected location and day.
3. Inspect the location/source/freshness context as needed.
4. Change location when traveling or correct an incorrect selection.
5. When disconnected, view previously saved valid schedule data where available.

### 6.3 Ramadan use

1. Open the Ramadan time section or Phase 1's combined time screen.
2. See Sahri-end and Iftar times for the relevant region/date, with explicit source context.
3. Understand whether the timetable is verified, pending, stale, or unavailable.
4. If official Ramadan date decisions or published times change, receive the corrected schedule on the next supported sync; never hide its basis.

### 6.4 Phase 1 screen inventory (product intent, not a UI library decision)

| Screen / state | Purpose | Phase 1 boundary |
|---|---|---|
| **Today / Salat Times** | Primary five-prayer schedule, place and date. | Required |
| **Ramadan Times** | Sahri-end and Iftar information. | Required for covered Ramadan dates |
| **Location Selection** | Choose/correct region with no mandatory GPS. | Required |
| **Source / Schedule Information** | Explain data source, method or reviewed timetable and freshness. | Required; may be a lightweight detail panel rather than a separate screen |
| **Offline / Error / No Coverage** | Clear next action, not fabricated content. | Required UI states |

Do not expand into additional sections such as dashboards, content feeds, profiles, or trackers without explicit product approval. Visual navigation approach and routing libraries remain technical decisions governed by the TRD.

### 6.5 UX, copy, and accessibility principles

- The two key time categories must be understandable at a glance; avoid visual clutter and unnecessary taps.
- Time figures must be legible and not distinguishable by color alone.
- Explain what date/time zone is being displayed; do not use a misleading "live" or "verified" badge without matching data evidence.
- Make tap targets, contrast, screen-reader labels, text resizing, and loading/empty/error states usable on common target devices.
- Avoid dark-pattern permission prompts or forced account creation.
- Default language, translated terminology, numeral style, 12/24-hour format, and launch-country localization remain **TBD**; prioritize Bangladesh needs without silently approving a translation library.
- UX designs should be reviewed against accessibility and real user tasks before implementation; do not invent product-specific dimensions or performance numbers as approved requirements.

---

## 7. Geography, Data Authority, and Religious Accuracy

### 7.1 Geographic strategy

- **Bangladesh-first** means first priority for region coverage, human validation, quality assurance, and launch communication—not a claim of complete global timetable coverage.
- Model country, place/region, coordinates where appropriate, IANA zone, source eligibility, and timetable validity independently.
- Global-ready does not mean Phase 1 must support every country or depend on GPS.
- Initial city/district coverage and the first non-Bangladesh markets require explicit product-owner confirmation.

### 7.2 Time authority and source selection

- **Prayer data:** calculation method versus verified published schedules remains open; select only after review and owner approval.
- **Ramadan data:** approved institution/publisher, month-start policy, date range, and local correction process remain open.
- **No unauthorized offsets:** a guessed minute adjustment is never an acceptable fallback.
- Preserve the distinction between **source**, **calculation/profile**, **effective date**, **verified/reviewed status**, and **last update**.
- If different legitimate methods yield different times, provide transparency and a documented product rule rather than concealing the difference.
- Never attribute a jurisprudential ruling or timetable convention to a particular scholar or institution without checking the source.

### 7.3 Correction handling

A known incorrect prayer or Ramadan schedule is a **time-sensitive content issue**. The team must be able to mark the source as under review, prevent publishing unreviewed replacements, apply a reviewed correction, preserve an audit trail, and explain to users when previously saved content is outdated. Specific user-alert mechanics need separate product/technology approval if they introduce a new feature or dependency.

---

## 8. Offline and Data Freshness Product Requirements

**Goal:** The app remains useful offline without pretending it has data it never obtained.

| Situation | Expected behavior |
|---|---|
| Previously downloaded valid schedule, now offline | Show saved schedule with appropriate source/validity indicator. |
| Fresh installation without connectivity or an approved on-device calculation capability | Explain that time data is not yet available; offer retry when online. |
| Selected place changed, no data for new place | Do not display old-place schedule as valid for new place. |
| Local schedule outside supported dates | Explicit expired/unavailable state; no silent reuse. |
| Interrupted update or invalid download | Retain last valid dataset; do not show partially applied data. |
| Corrected timetable published | Safely replace/mark obsolete local content upon a supported sync. |
| Device clock/time-zone altered | Recheck displayed day and offset; avoid stale day rollover. |

The exact cache lifetimes, update frequency, downloadable coverage, manifest shape, and availability of offline **calculation** depend on pending technical and data-source decisions. They must not be promised before validation.

---

## 9. Accounts, Permissions, and Privacy

### 9.1 Guest-first product policy

- **Phase 1 must work for guests.** A user account cannot be a prerequisite for viewing supported prayer or Ramadan data.
- Optional user accounts are an approved **overall product direction**, not a mandatory Phase 1 screen or backend integration.
- Introducing end-user sign-in, identity synchronization, password reset, or account-linked data is a separate scope and technology decision.
- Administrative access may be required to manage source data but must remain separate from the public user experience.

### 9.2 Privacy requirements

- Require the least information necessary to provide a schedule.
- Provide manual location selection; no forced GPS permission.
- Do not collect or upload precise location history without an explicit, approved need and appropriate disclosure.
- Do not embed privileged Cloudflare bindings, admin credentials, or private keys in client applications.
- Define retention and deletion rules if persistent personal accounts or analytics are introduced later.

---

## 10. Minimal Administrative Workflow Supporting Phase 1

The **React + Vite + shadcn/ui** admin application supports the two approved features. It is not permission to build a general-purpose CMS.

### Allowed supporting operations (where needed by the approved data strategy)

1. Manage source references, eligible regions, and coverage dates.
2. Enter/import applicable reviewed prayer profiles or published timetable records.
3. Manage Ramadan timetable entries and corrections.
4. Require a controlled **draft → reviewed → published** workflow before official updates become visible.
5. Retain audit data for publication, corrections, and rollbacks.
6. Show whether schedule data is available and published for a particular region/date.

**Guardrails:** Admin data changes must be authenticated and authorized on the server. Authentication provider and the detailed roles/session design remain **TBD** under the TRD and require owner approval before implementation. For a purely on-device approved prayer calculation strategy, some schedule-management screens may be unnecessary: implement only what the chosen data model needs.

---

## 11. Phase 1 Quality and Nonfunctional Requirements

### 11.1 Correctness and reliability

- A wrong location/time-zone/time-data combination is a release-blocking defect.
- Display the same approved source data across mobile UI, locally retained records, and associated administration; reconcile known differences before publishing.
- Prefer safe unavailable states to plausible but unverified values.
- Handle calendar transitions, traveling, leap years, daylight-saving regions, and regional Ramadan starts as applicable.

### 11.2 Performance and resilience

- Avoid a backend request for every screen render when local data already covers the requested date/place.
- Provide loading indicators only where work is actually pending; cached content must not be silently mislabeled as fresh.
- Validate downloads, handle retries sensibly, and preserve the last valid dataset on failure.
- Concrete latency, app size, cache duration, and uptime targets remain **TBD** until measured and approved.

### 11.3 Security

- Validate untrusted server inputs and content updates.
- Enforce admin access restrictions on backend endpoints, not only in frontend navigation.
- Apply least privilege to D1, R2, and deployment credentials.
- Review data imports and dangerous schema changes before application.

### 11.4 Accessibility and inclusive design

- Support readable scaling, accessible labels, sufficient contrast, and understandable time/date formats.
- Avoid relying solely on color, icons, or vibration for critical meaning.
- Provide an understandable manual path when device permissions are denied.

### 11.5 Maintainability

- Keep prayer and Ramadan domain rules independent from view components where feasible.
- Use approved tooling and existing code conventions; do not introduce libraries for convenience without permission.
- Keep product decisions in this PRD and technical/technology decisions in the TRD.
- Document important assumptions, errors, and source provenance instead of burying them in code.

---

## 12. Confirmed Technology Constraints (Reference Only)

The following selections are already approved in the **TRD**. This is a product-context summary, **not an alternative technology register**; consult `TRD.md` for exact scope and approval rules.

| Concern | Existing approved choice |
|---|---|
| Mobile | React Native + Expo + NativeWind |
| Admin | React + Vite + shadcn/ui |
| Backend | Hono on Cloudflare Workers |
| Server data | Cloudflare D1 + Drizzle ORM |
| Local offline data | Expo SQLite + Drizzle ORM |
| Migration workflow | Drizzle Kit + Wrangler D1 Migrations |
| Object storage | Cloudflare R2 (use only if required) |
| Notifications tooling | Expo Notifications (approved technology; **not a Phase 1 feature commitment**) |
| App state | Zustand |
| API data fetching | TanStack Query + Axios |
| Validation | Zod |
| Package management | pnpm |

**TBD / unapproved:** prayer computation library and presets, GPS/geocoding package, navigation packages, auth provider, default UI language, detailed Ramadan data sources, additional testing infrastructure beyond the TRD's approved stack, build/release tooling, and any future-phase features. Do not convert recommendations to implementation decisions without explicit owner approval.

---

## 13. Phase 1 Implementation Sequence (Gated Milestones)

Milestones define an order of execution; they do **not** commit to calendar dates, team sizes, or estimates.

| Milestone | Goal | Required exit evidence |
|---|---|---|
| **M0 — Decide prerequisites** | Agree on reliable prayer-time strategy, Ramadan publisher/calendar policy, minimum locations and language, offline data coverage, and required permissions. | Written owner decisions and aligned PRD/TRD. |
| **M1 — Establish working foundation** | Verify existing Expo, Vite, Workers, D1, SQLite, and pnpm setup; define domain contracts and data boundaries. | Reproducible local setup; schema/contract review; no unapproved libraries. |
| **M2 — Deliver trustworthy time data** | Implement approved data acquisition or calculations, validation, regional coverage and safe publication where needed. | Reviewed regional test samples, provenance and correction handling. |
| **M3 — Build Phase 1 experience** | Build Salat and Ramadan views, manual location selection, clear time/date labeling, guest-first entry and error states. | Demonstrable user flows using approved data only. |
| **M4 — Offline and robustness** | Persist appropriate schedules/preferences and handle absence, corruption, date/location changes and failed updates. | Reproducible offline test evidence. |
| **M5 — Validate and release** | Run approved/available checks, device tests, usability and source reviews; fix release blockers and document limitations. | Owner reviews release checklist, defects and unresolved limitations. |

Do not skip the **M0** decision gate by choosing a popular prayer library or online timetable on behalf of the owner.

---

## 14. User Stories and Acceptance Matrix

| Story ID | User story | Minimum testable result |
|---|---|---|
| US-01 | As a guest, I want to view today's five prayer times. | Valid approved data renders without account creation. |
| US-02 | As a user, I want to select my place manually. | Selection updates the requested schedule correctly and persists. |
| US-03 | As a traveler, I want to know which place the times are for. | The place and date/time-zone context are clear; device locale does not silently override it. |
| US-04 | As a Ramadan user, I want Sahri and Iftar times. | Two correctly labeled times appear for a covered date/region. |
| US-05 | As a user, I want to know when a schedule is uncertain or outdated. | Verified/cached/stale/unavailable states are honest and useful. |
| US-06 | As an offline user, I want a previously available timetable. | Valid stored schedules remain accessible after reopening offline. |
| US-07 | As a new offline user, I want an explanation if times are missing. | Clear unavailable message; no invented fallback. |
| US-08 | As an administrator, I want to correct a timetable responsibly. | Authorized, reviewed publication with source and audit details, if server-managed data is selected. |
| US-09 | As a user, I want corrections to be safe. | An interrupted sync retains the previous valid dataset until a reviewed update is complete. |

---

## 15. Phase 1 Verification and Release Criteria

### 15.1 Minimum release checklist

- [ ] The owner has approved the prayer-time method and its source/reference data; neither remains silently chosen by engineers or AI.
- [ ] The owner has approved the Ramadan timetable publisher, applicable region rules, and month-start policy.
- [ ] A documented list of **actually supported** Phase 1 locations and dates exists.
- [ ] Five prayer times display with correct location, date, time-zone semantics, and source information.
- [ ] Sahri/Iftar shows correct date/location, clear labels, and no ambiguous Imsak/Fajr interpretation.
- [ ] The Phase 1 user flows work without forced sign-in or GPS permission.
- [ ] Previously saved valid schedule information behaves properly offline, and fresh/offline/no-data states are truthful.
- [ ] Publication, correction, and rollback (where applicable) require server-side authorization and preserve an audit trail.
- [ ] Representative Bangladesh locations/seasons and selected edge cases have been checked against reviewed references.
- [ ] Device restarts, day rollover, missing data, changes in place/time zone, and interrupted sync have been tested.
- [ ] Security, accessibility, and user-facing copy have received appropriate review.
- [ ] Known critical timing errors, geographic mislabeling, and unreviewed religious-source discrepancies are resolved or block release.
- [ ] No unapproved feature, library, or external service has been silently introduced.
- [ ] The release owner approves the shipped scope and any documented limitations.

### 15.2 Required Testing Guidance

- Domain tests for date, location and time-zone transformations, including UTC rollover and DST outside Bangladesh.
- Data verification against a reviewed set of Bangladesh district/city schedules and representative seasons.
- Feature tests for location changes, source labels, empty/error conditions, and Ramadan date coverage.
- Offline tests for stored data, clean install with no connectivity, partial updates, and local migrations.
- Authorization and audit tests for any server-managed timetable publishing.
- Manual accessibility and real-device tests for supported Android/iOS builds.

The TRD now approves Vitest for web/backend, Jest for mobile, their corresponding Testing Libraries, and test lint plugins. Follow `docs/RULES.md` for mandatory Zod validation, rule severities, and test design. Installation and runtime enforcement must be verified separately. Additional testing infrastructure still requires approval under the TRD.

---

## 16. Product Success Criteria and Measurement

**Initial qualitative outcome:** A person in a supported Bangladesh location can quickly understand correct daily Salat times and, during Ramadan, Sahri/Iftar times even when previously saved data must be used offline.

Suggested metrics to define **after approval**, with no tracking SDK automatically implied:

| Outcome | Candidate metric | Current state |
|---|---|---|
| Correctness | Approved reference-case pass rate; timing defects by severity | Threshold TBD |
| Coverage | Number of verified supported regions and dates | Launch target TBD |
| Reliability | Rate of successful offline opens with eligible data | Threshold TBD |
| Usability | Task completion for locating today's prayer and Ramadan times | Threshold TBD |
| Operational quality | Publication/correction turnaround and incidents | Threshold TBD |
| Performance | Time to usable schedule with warm local data | Threshold TBD |

Do not introduce analytics or collect personal data merely to measure these until the owner approves the purpose, collection and tool.

---

## 17. Risks, Dependencies, and Mitigations

| Risk | Potential impact | Required mitigation |
|---|---|---|
| Prayer engine/method unresolved | Wrong or inconsistent times | Approve and validate strategy before implementing core calculations. |
| Ramadan source/calendar unresolved | Incorrect Sahri/Iftar or month dates | Approve regional authority/precedence and review publication. |
| Global ambition exceeds verified coverage | Misleading claims or time values | Explicit coverage labels and unsupported-location handling. |
| Offline data absent or expired | Unexpected missing times | Honest unavailable state and valid data retention. |
| Time zone/travel transition | Wrong local civil date or schedule | IANA-aware transformations and dedicated tests. |
| Admin account implementation undecided | Unsafe publishing | Do not ship privileged writes without approved secure auth. |
| Uncontrolled future features | Scope creep and delays | Phase gate and separate owner approvals. |
| New dependency introduced silently | Lock-in, compatibility, cost | Follow the TRD technology-approval process. |
| Unclear responsibility for corrections | Wrong data persists | Establish reviewer/publisher roles and incident workflow. |

---

## 18. Open Product Decisions — Ask the Owner Before Execution

These are **explicitly unresolved** and should be discussed when work actually depends on them. Do not silently choose defaults.

| ID | Decision required | Status |
|---|---|---|
| PD-01 | Device calculation, reviewed regional timetable, or approved hybrid strategy for prayer times? Which source, profile and validation rules? | **TBD** |
| PD-02 | Which verified Ramadan timetable and country/region moon-sighting policy should govern Sahri/Iftar? | **TBD** |
| PD-03 | Exactly which Bangladesh districts/cities are required at launch, and is any non-Bangladesh country committed for Phase 1? | **TBD** |
| PD-04 | Default display language, numerals, date/time formatting, and any language-switching scope? | **TBD** |
| PD-05 | How much valid data is available on first installation, and what offline date coverage is promised? | **TBD** |
| PD-06 | Is GPS-assisted location desired, or manual selection only? Any proposed package needs approval. | **TBD** |
| PD-07 | Which schedule review/publish roles, secure admin authentication approach, and data ownership will be used? | **TBD** |
| PD-08 | Is Sunrise supplementary context worth including in Phase 1, or keep only five prayer times? | **TBD** |
| PD-09 | Supported mobile OS versions, launch distribution, exact performance goals, and additional testing infrastructure? | **TBD; core test stack approved in TRD** |
| PD-10 | Should adhan/prayer or Sahri/Iftar reminders be added to a **later approved scope**? Expo Notifications approval does not pre-approve them now. | **Deferred** |

For dependencies not needed yet, avoid blocking unrelated foundation work; for time-accuracy dependencies, **do not bypass the gate**.

---

## 19. AI Agent and Team Working Contract

**Mandatory requirements when implementing this PRD:**

1. Read `docs/PRD.md`, `docs/TRD.md`, `docs/RULES.md`, and relevant repository instructions when they exist.
2. Confirm current repository structure and implemented behavior instead of assuming the documents describe existing code.
3. Work **only on the currently approved phase**. Phase 1 comprises Salat times and Sahri/Iftar times plus necessary supporting capabilities.
4. Do not turn examples, recommendations, desired best practices, or an already installed dependency into a new feature approval.
5. Do not select religiously consequential timing rules, data sources, or calendar policies without documented review and owner approval.
6. Before introducing any new direct technology/tool/service or materially changing architecture, follow the TRD's explicit approval process, then update the TRD before coding.
7. For new product behavior or scope expansion, ask for product approval, update this PRD, then implement.
8. Keep tasks small, testable, and reversible. Match existing patterns where they meet the approved requirements.
9. Run relevant existing tests/checks, and accurately report what passed, failed, or was not run.
10. Make no claim that a phase is complete until its acceptance criteria and release gate have been reviewed.
11. Ask the owner to define the next phase **only after** current-phase review; do not pre-fill later roadmaps.

### Documentation conflict resolution

- **Latest explicit product-owner instructions** override previous plans.
- **PRD** governs product goals, phase scope and user experience.
- **TRD** governs approved technology, technical constraints and the approval process.
- **Repository** establishes what has actually been implemented; discrepancies must be reported rather than silently resolved by adding scope or tools.

---

## 20. Definition of Done for a Phase 1 Task

A Phase 1 task is complete only when all applicable statements hold:

- [ ] The change addresses an approved Phase 1 user need and does not introduce unrelated features.
- [ ] Requirements and edge cases have been made testable.
- [ ] Appropriate states exist for loading, error, no coverage, stale and offline use.
- [ ] Accessibility and date/time-zone handling have been considered.
- [ ] Data origin and any religious timing convention are explicit and reviewed.
- [ ] No unapproved dependency, integration, method, or service has been introduced.
- [ ] Relevant existing tests/type checks/manual verification have been performed, or limitations stated.
- [ ] Any affected PRD/TRD requirements and operational documentation have been updated.
- [ ] The implementation notes communicate what changed, why, outstanding decisions and known risks.

**End of document. Future product phases are intentionally undefined until the owner approves them.**
