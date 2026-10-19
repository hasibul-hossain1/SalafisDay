# Engineering Rules

## Scope & Authority

Read `docs/PRD.md`, `docs/TRD.md`, and this file before implementation or review. These rules apply to `app/`, `dashboard/`, and `backend/`. Follow the owner's latest explicit decisions, the PRD's active phase, and the TRD's approved technology register. Phase 1 covers Salat and Sahri/Iftar times with necessary supporting behavior; notifications and accounts are not automatic Phase 1 deliverables.

These are mandatory implementation requirements, not evidence that packages, configurations, or tests already exist. Inspect each application's manifest and runtime before configuring tools. Additional dependencies beyond approved selections still follow the TRD approval process.

## Zod Validation Across the Data Lifecycle

- All application data entering a trust or persistence boundary MUST pass an explicit Zod schema before use: form submissions, route/query parameters, request bodies, API responses, imported schedules, sync manifests, restored preferences/cache records, and serializable environment configuration. Validate relevant fields rather than attempting to parse native platform or Cloudflare binding objects.
- Validate on the server even when the client already validates. Validate outgoing API payloads against their response contracts and incoming client responses before caching, persistence, or rendering. Revalidate stored data when loading it into the domain layer, including after migrations.
- Treat external values as `unknown`; use `safeParse` for expected validation failures and use only its successful `data` result. Handle malformed JSON before schema parsing. Handle any thrown `parse` errors at the boundary. Type assertions, TypeScript interfaces, and `any` are not runtime validation.
- Name schemas clearly, e.g. `locationSchema` and `ramadanScheduleSchema`; derive corresponding types with `z.infer`. Reuse domain schemas where runtime compatibility permits. Already validated internal values need not be parsed repeatedly on every render; validate again after untrusted input or a relevant transformation.
- Constrain strings, array sizes, numeric ranges, enums, required fields, and nullable/optional values deliberately. Reject unexpected fields on privileged write payloads; document unknown-field policy for other contracts. Do not silently coerce empty strings into valid numbers, invent defaults, or hide invalid values with fallbacks.
- Validate domain relationships as well as shape: real civil dates, supported location IDs, valid IANA zones, coordinate ranges when used, region/date coverage, source identity, and publication state. Check referenced records and current permissions separately. Schema validation does not prove religious accuracy or grant authorization.
- Reject invalid input before writes, publication, notification scheduling, or other side effects. Return consistent, field-specific errors without secrets or raw sensitive payloads. Invalid downloads/restored records must produce a safe recovery or unavailable state while preserving the last valid dataset.
- Test every boundary schema with valid cases, missing fields, wrong types, unknown fields, limits, malformed dates, unsupported regions, and cross-field failures. Test that rejection causes no write or publication. Use reviewed reference fixtures for actual timetable accuracy.

## Testing & Linting Guidelines (Full-Stack / UI)

### Approved Tools

| Application | Test framework | UI library | Test lint plugin |
|---|---|---|---|
| `dashboard/` | Vitest | `@testing-library/react` | `@vitest/eslint-plugin`, registered as `vitest` |
| `backend/` | Vitest | Not applicable | `@vitest/eslint-plugin`, registered as `vitest` |
| `app/` | Jest | `@testing-library/react-native` | `eslint-plugin-jest`, registered as `jest` |

Use `eslint-plugin-testing-library` for UI tests. The current package for the requested Vitest ESLint plugin is `@vitest/eslint-plugin`. Verify compatibility with each app's ESLint, React, Expo, and Workers versions before setup. DOM environments, Expo presets, Workers adapters, coverage providers, and E2E/network-mocking packages require approval if not already registered in the TRD.

### Required ESLint Configuration

- Keep existing application lint rules. Add test-only flat-config overrides for `**/*.{test,spec}.{ts,tsx,js,jsx}` and appropriate `__tests__/**` files; avoid enabling runner globals across production source.
- Enable Vitest's `configs.recommended` or Jest's `configs['flat/recommended']` for the relevant runtime. Apply explicit severity overrides **after** recommended presets.
- Enable Testing Library's React recommended preset (`configs['flat/react']`) for web UI tests. For React Native, verify rule support against the installed library and enable compatible recommended rules plus the requirements below; do not invent a `react-native` preset or apply DOM-only rules blindly. If a rule cannot enforce native behavior, document the gap and review it manually rather than claiming enforcement.

| Rule | Vitest test files | Jest test files |
|---|---|---|
| No focused tests | `vitest/no-focused-tests: error` | `jest/no-focused-tests: error` |
| Require assertions | `vitest/expect-expect: error` | `jest/expect-expect: error` |
| Valid assertions | `vitest/valid-expect: error` | `jest/valid-expect: error` |
| Unique test titles | `vitest/no-identical-title: error` | `jest/no-identical-title: error` |
| Flag skipped tests | `vitest/no-disabled-tests: warn` | `jest/no-disabled-tests: warn` |

Required UI-test rule overrides:

```json
{
  "testing-library/await-async-queries": "error",
  "testing-library/await-async-utils": "error",
  "testing-library/no-debugging-utils": "warn",
  "testing-library/prefer-screen-queries": "error",
  "testing-library/no-await-sync-queries": "error",
  "testing-library/no-wait-for-side-effects": "error"
}
```

Await every `findBy*`/`findAllBy*`, `waitFor`, and other asynchronous utility. Query via `screen`; use `within` for explicitly scoped regions. Prefer accessible roles and names over test IDs. Keep interactions outside retrying `waitFor` callbacks. Do not leave `screen.debug()`, `.only`, `fit`, or `fdescribe` in committed tests. Every test must assert meaningful behavior; document skipped tests with a reason and follow-up.

### Test Design & Execution

- Name tests `*.test.ts` for domain/API behavior and `*.test.tsx` for components, beside the feature or under `__tests__/`. Test public behavior and observable outcomes rather than component internals or only snapshots.
- Add regression tests for fixes and relevant success, failure, and edge cases for changed behavior. Include schema rejection, API authorization/status/error contracts, and persistence failure cases where applicable.
- Keep tests deterministic: control clock, selected location/time zone, network outcomes, and storage; restore mocks/timers and isolate database state. Unit/component tests must not require live third-party services or production credentials. Validate Workers-specific behavior in a compatible runtime; a Node-only pass does not establish D1/R2 compatibility.
- Add per-app test scripts when implementing the approved setup. Run relevant tests in non-watch mode, lint, type checks, and applicable builds before declaring completion. Until configured, explicitly report missing scripts; do not invent successful commands. A suite with no discovered tests is not a passing verification.
- No numeric coverage target is approved. Cover the changed branches and critical failure paths; do not substitute a percentage for correctness. Record actual commands, results, environment/device, skipped checks, and remaining limitations.

## PRD Acceptance Scenarios

| Requirement | Required verification when affected |
|---|---|
| PR-01–08; US-01–03 | Five labeled prayers, visible place/date/zone/source, manual location selection, persisted preferences, guest access, and no previous-location results shown as current during races. |
| RM-01–07; US-04–05 | Covered region/date, honest pending/stale/unavailable states, explicit Sahri/Fajr/Imsak and Iftar/Maghrib meanings, approved source policy. |
| US-06–07; PRD §8 | Offline reopen with valid data; fresh offline install without data; expired/corrupt records; retries; persisted preferences; no fabricated times. |
| US-08–09; PRD §10 | Server-enforced authentication/authorization, draft → reviewed → published, audit records, correction/rollback, atomic sync retaining the previous valid dataset on interruption or validation failure. |
| PRD §11.1 | Bangladesh reference cases across seasons, midnight/year/leap-day transitions, selected versus device zone, DST and regional Ramadan boundaries. |
| PRD §6.5, §11.4 | Accessible labels/roles, keyboard operation on web, text scaling, contrast, loading/error/empty states, and screen-reader usability; include manual/device checks. |

Use approved or clearly synthetic test fixtures; synthetic times must never ship as authoritative content. Never invent a calculation method, calendar convention, supported location list, or timing offset to satisfy a test. Wrong place/date/zone combinations and unreviewed timetable data block release. Test notifications only when their product scope is approved; then include deduplication, permission denial, rescheduling, and real-device constraints from the TRD.

## Security & Completion Rules

Enforce admin permissions on the server, independently of Zod validation. Keep credentials and privileged D1/R2 bindings out of clients and logs. Review migrations and recovery behavior; destructive or production data changes require explicit owner authorization. Keep PRD, TRD, these rules, and implementation evidence aligned; documentation-only changes do not establish runtime enforcement.

## Tool References

- [Zod parsing and inferred types](https://zod.dev/basics)
- [Vitest ESLint plugin and recommended config](https://github.com/vitest-dev/eslint-plugin-vitest)
- [Jest ESLint plugin and test-file configuration](https://github.com/jest-community/eslint-plugin-jest)
- [Testing Library ESLint presets and rules](https://github.com/testing-library/eslint-plugin-testing-library)
