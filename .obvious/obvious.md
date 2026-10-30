# OpenIndustries — Agent Guidance

**Repo:** isayahc/OpenIndustries — "OpenIndustries" (`open-industries` v0.1.1)
Spatial workbench for hardware and manufacturing-system planning: arrange equipment in 3D, animate workflows, export/review scenes.

## Stack

- **Frontend:** React 19 + Three.js 0.180 + Vite 6, TypeScript (strict, `tsc --noEmit` in build)
- **Backend/API:** Express 5 (Node ESM) in `server/` — MCP servers, Form generation bridge, game/simulation services
- **Runtimes:** Node >=22.16.0 (engines-enforced), Python 3.11+ **optional** (only for local Form generation bridge, `requirements-form.txt`)
- **Package manager:** npm (`package-lock.json` committed; a stray `bun.lock` exists but is untracked)
- **Cloud (optional at dev time):** Supabase — browser workbench runs fully local without sign-in; `VITE_CLOUD_STORAGE_ENABLED=false` by default; game runtime uses local SQLite (`.astra/game/runtime.sqlite`)

## Sandbox environment notes

- System `node` is v20 — **prepend `~/.local/opt/node-v22.20.0-linux-x64/bin` to PATH** for all npm/node work (engines requires >=22.16).
- `node_modules/` and `public/vendor/` were root-owned on first boot; `sudo chown -R user:user node_modules public` was applied. If a fresh clone hits EACCES on install, do the same.
- `npm install` runs `postinstall` → `scripts/prepare-vendor.mjs` (copies occt-import-js into gitignored `public/vendor/`).
- Dev servers run in a detached tmux session (`dev`), logs at `/tmp/openindustries-dev.log`.

## Commands

| Task | Command |
| --- | --- |
| Start dev (API + web) | `npm run dev` — API `http://127.0.0.1:8787`, web `http://127.0.0.1:5173` |
| API only | `npm start` (serves `server/index.mjs` on `PORT`=8787) |
| Typecheck | `npx tsc --noEmit` |
| Full build | `npm run build` (typecheck ×3 + simulation build + vite build) |
| E2E smoke (needs build + `npm start`) | `npm test` (Playwright; writes screenshots to `test-results/`) |
| Unit tests (Node-only) | `npm run test:project-files` (Forma/OI round trips) |
| Simulation tests | `npm run test:simulation` |
| Health check | `curl http://127.0.0.1:8787/api/health` |

No lint script is defined in `package.json`; typecheck is the de-facto lint gate. No local Postgres/Redis — state is SQLite files + optional Supabase.

## Env vars

Copy `.env.example` → `.env` (defaults work for local dev). Required-by-default: none (all have local-safe defaults). Notable: `PORT` (8787), `VITE_SUPABASE_URL`/`VITE_SUPABASE_PUBLISHABLE_KEY` (empty OK locally), `ASTRA_GAME_PORT` (8790), `ASTRA_SCENE_BACKEND` (`local` for no-cloud scenes), `OPENAI_API_KEY`/`LLM_PROVIDER` only for live Form generation (optional, unverified upstream).

## Codebase map

See [codebase-map.md](codebase-map.md).

## Local verification

1. `npm run dev` (tmux) → Vite ready on 5173, API on 8787.
2. `curl /api/health` returns 200 JSON (Form generation reports `available:false` without Python — expected; file imports work independently).
3. Headless Chromium (Playwright): workbench loads with UI controls; `/?scene=cleanroom` loads bundled 4-room demo; **zero console/page errors**. Screenshots: `test-results/onboarding/{workbench-home,cleanroom-scene}.png`.
4. `npx tsc --noEmit` → pass. `npm run test:project-files` → PASS.

### Local Verification Summary

- `dev_stack_healthy: true`
- Verified: 2026-10-30
- Primary flow evidence: browser load of workbench + cleanroom demo scene, `/api/health` 200, zero console errors, screenshots in `test-results/onboarding/`
- Typecheck: pass · Unit: pass (`test:project-files`)
- Blockers: none. Optional gaps (not blockers): Python/Form generation bridge not installed (demo fallback works); `npm test` full E2E not run (requires production build + separate `npm start`).

## Sandbox snapshot

- **Snapshot ID:** `gix94kquvty2moni5oho:default`
- **Captured:** 2026-10-30T16:03:09.453Z
- Captured after dev stack verified healthy (dev servers were running in tmux at snapshot time).
