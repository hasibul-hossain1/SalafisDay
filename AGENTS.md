# Repository Guidelines

## Requirements & Agent Instructions

Read `docs/TRD.md` in full before planning, implementing, or reviewing changes. Follow its decision precedence and approved scope. Verify implementation against code. Before introducing unapproved technology or resolving TBD decisions, obtain explicit owner approval and update the TRD.

Also read `docs/PRD.md` for active-phase scope and follow `docs/RULES.md` for mandatory Zod validation, testing, linting, and completion requirements.

## Project Structure & Module Organization

- `docs/`: product requirements in `PRD.md`, technical requirements in `TRD.md`, and engineering rules in `RULES.md`.
- `app/`: Expo/React Native mobile app; entry point `App.tsx`, reusable UI in `components/`, images in `assets/`, NativeWind styles in `global.css`.
- `dashboard/`: React/Vite admin app; code in `src/`, shadcn components in `src/components/ui/`, utilities in `src/lib/`, assets in `src/assets/` and `public/`.
- `backend/`: Hono/Cloudflare Workers service; entry point `src/index.ts`, runtime configuration in `wrangler.jsonc`.

Each app has its own manifest and lockfile. No root scripts or shared workspace exist. The TRD's `apps/*` layout is proposed; do not relocate directories automatically.

## Build, Test, and Development Commands

Run from root using pnpm:

- `pnpm --dir app start`: start Expo; use `android`, `ios`, or `web` for platform launch.
- `pnpm --dir dashboard dev`: start Vite; `build` type-checks and produces production assets; `preview` serves that build.
- `pnpm --dir backend dev`: start local Wrangler development.
- `pnpm --dir backend cf-typegen`: regenerate Worker binding types.
- `pnpm --dir app lint` and `pnpm --dir dashboard lint`: check frontend code.

Both frontends provide `format`. Backend `deploy` publishes to Cloudflare and requires deployment approval.

## Coding Style & Naming Conventions

Preserve TypeScript and two-space indentation. Use PascalCase for React components and camelCase for functions/variables. Follow local formatting: mobile uses single quotes and semicolons; dashboard uses double quotes and no semicolons. Both use ESLint and Prettier with Tailwind class sorting. Match surrounding backend style.

## Testing Guidelines

Follow `docs/RULES.md`: approved Vitest (web/backend), Jest (mobile), Testing Libraries, and lint plugins. Test scripts remain unconfigured; no coverage threshold is approved. Use `*.test.ts`/`*.test.tsx`. Report results, environments/devices, and skipped checks.

## Commit & Pull Request Guidelines

Follow existing imperative commit subjects, such as `Add .gitignore file`. Keep changes focused. PRs should explain behavior, reference TRD requirements and issues, list verification results and unresolved decisions, and include screenshots for UI changes.

## Security & Configuration

Never commit secrets or expose privileged D1/R2 credentials in Expo/Vite clients. Production migrations and destructive data operations require explicit owner authorization and a recovery plan.
