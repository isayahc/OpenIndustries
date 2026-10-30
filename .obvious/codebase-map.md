# Codebase Map — OpenIndustries

| Path | What it is |
| --- | --- |
| `src/` | React 19 web workbench (Vite app): `main.tsx` entry, `components/` (UI panels, room editor), `lib/` (scene/import/export/animation logic) |
| `server/` | Express 5 API + local services: `index.mjs` (main API), `form.mjs` (Form generation bridge), `astra-mcp.mjs`/`mcp-*.mjs` (MCP servers), `game-*.mjs` (SQLite tower-defense game runtime), `simulation-service.ts`, `field-lab-service.ts`, `local-scene-backend.ts` |
| `cli/` | `astra` CLI (`astra.mjs`, `session.mjs`, `local-scenes.ts`) — auth + room/scene transfer with Supabase |
| `packages/material-science/` | Published workspace package `@openindustries/material-science` with its own tsconfig/tests |
| `scripts/` | ~60 test/smoke Playwright + tsx scripts behind the `test:*` npm scripts; `smoke.mjs` (main e2e); `prepare-vendor.mjs` (postinstall vendor copy); `fixtures/`, `lib/` |
| `tests/` | Node test suites (`tests/simulation/*.test.ts`) run via `node --test`, shared `form-model-types.ts` + `helpers/` |
| `public/` | Static assets served by Vite: `examples/cleanroom/` (bundled demo scene), `default-room.json`, `step-worker.js`, gitignored `vendor/` (occt-import-js wasm/js, copied by postinstall) |
| `docs/` | Architecture and feature docs (`development.md` is the setup guide); `screenshots/`, `evidence/` |
| `supabase/` | Supabase project config + SQL migrations |
| `deploy/` | Deployment targets: `farm/`, `inspection/`, `runtime/` |
| `benchmarks/field-lab/` | Field-lab benchmark harness |
| `samples/` | Sample portable scene/project files (`.oi.json`, GIF) |
| `services/` | Auxiliary service code (supporting deploy targets) |
| `tools/` | Repo tooling utilities |
