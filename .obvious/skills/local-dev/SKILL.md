---
name: local-dev
description: How to bring OpenIndustries up locally in this sandbox (proven on first onboarding run)
---

# local-dev — OpenIndustries onboarding record (2026-10-30)

## What worked, end to end

1. **PATH first.** System node is v20 (< engines requirement of >=22.16). Node 22.20.0 lives at `~/.local/opt/node-v22.20.0-linux-x64/bin` — export it on PATH before any npm/node command.
2. **Fix root-owned artifacts.** `node_modules/` and `public/vendor/` came root-owned; first `npm install` died with EACCES renaming `node_modules/esbuild` and postinstall failed writing `public/vendor/occt-import-js.js`. Fix: `sudo chown -R user:user node_modules public`, then rerun `npm install` (postinstall `scripts/prepare-vendor.mjs` copies occt-import-js into gitignored `public/vendor/`).
3. **Env.** `cp .env.example .env` — defaults are local-safe. No DB to create/migrate/seed; game runtime is SQLite (`.astra/game/runtime.sqlite`), scenes optional-local via Supabase or `ASTRA_SCENE_BACKEND=local`.
4. **Start.** `npm run dev` (concurrently: API `server/index.mjs` on :8787 + Vite on :5173). Run in detached tmux — a plain `nohup ... &` from a shell tool call left ambiguous process state once. Check ports free first (`ss -tlnp | grep -E ':8787|:5173'`); a stale Vite on 5173 makes concurrently kill the whole stack.
5. **Verify.**
   - `curl http://127.0.0.1:8787/api/health` → 200 JSON; `available:false` is expected without Python/Form (`caid-forma-core==0.3.5`) — file imports work independently.
   - Playwright chromium (`npx playwright install chromium`): load `/` (workbench UI renders) and `/?scene=cleanroom` (bundled 4-room demo). Both loaded with **zero console/page errors**; screenshots in `test-results/onboarding/`.
   - `npx tsc --noEmit` → pass. `npm run test:project-files` → PASS.

## Gotchas

- Many `test:*` browser scripts require a production build + `npm start`, not the dev server (`npm test` = full e2e smoke; needs Chrome and network for STEP fixture).
- Python/`requirements-form.txt` is only for the local "Build with Form" generation bridge — skip it unless that feature is under test.
- `bun.lock` in the working tree is untracked; the repo's package manager is npm.

## Canonical startup (copy-paste)

```bash
export PATH=~/.local/opt/node-v22.20.0-linux-x64/bin:$PATH
cd /home/user/work/OpenIndustries
[ -f .env ] || cp .env.example .env
npm install
npm run dev   # API :8787 · web :5173
curl -s http://127.0.0.1:8787/api/health
```
