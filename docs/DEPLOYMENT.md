# Deployment Guide ($0 / free-tier)

Everything here can be hosted **free**. v1 is live today on GitHub Pages; v2 adds free-tier hosting for the
backend, database, and LLM.

## v1 — GitHub Pages (live now, zero setup)

1. Push the repo to GitHub (public).
2. **Settings → Pages → Source: Deploy from a branch → `main` → `/ (root)` → Save.**
3. ~1 minute later your site is live at `https://<username>.github.io/<repo>/`
   (currently **https://antarabarman.github.io/Project1/**).

`index.html` is served at the root, so the article loads first and links into the assessment. No build, no
secrets, no cost.

> Tip: the live URL is tied to the username + repo name. Don't rename either after sharing, or the link breaks.

## v2 — full stack on free tiers

| Layer | Free host | Notes |
|---|---|---|
| Frontend (React) | **Vercel** or GitHub Pages | `vercel deploy`; static output |
| Backend (FastAPI) | **Render** or **Fly.io** free tier | one small container; `Dockerfile` provided in Phase B |
| Database | **Supabase** or **Neon** (free Postgres) | `SQLite` for local dev |
| LLM (AI Coach) | **Groq** or **Google Gemini** free tier | key stored **only** as a backend env var |
| Analytics | **PostHog** (free) or GA | aggregate, anonymous events only |

### Backend environment variables (`.env`, never committed)

```ini
DATABASE_URL=postgresql://...        # Supabase/Neon connection string
JWT_SECRET=change-me
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...
GITHUB_CLIENT_ID=...
GITHUB_CLIENT_SECRET=...
LLM_API_KEY=...                      # Groq/Gemini free-tier key
LLM_MODEL=llama-3.1-8b-instant
ALLOWED_ORIGINS=https://<username>.github.io,https://<app>.vercel.app
```

A committed **`.env.example`** documents these without real values. The real `.env` is git-ignored.

### Deploy steps (Phase B)

```bash
# Backend (Render)
#   1. New Web Service → connect repo → root: /backend
#   2. Build: pip install -r requirements.txt
#   3. Start: uvicorn app.main:app --host 0.0.0.0 --port $PORT
#   4. Add the env vars above in the Render dashboard.

# Frontend (Vercel)
#   1. Import repo → root: /frontend
#   2. Build: npm run build   Output: dist/
#   3. Env: VITE_API_BASE=https://<app>.onrender.com/api/v1
```

### CI/CD

`.github/workflows/ci.yml` runs on every push: linting and validation today, extended in Phase B to
`pytest`, `vitest`, `playwright`, `ruff`, `black`, and `eslint`. Deploys can be wired to Render/Vercel's
git integration (auto-deploy on merge to `main`).

## Security checklist before going live

- [ ] LLM and OAuth secrets are in host env vars, **never** in the repo.
- [ ] `ALLOWED_ORIGINS` (CORS) is restricted to your real domains.
- [ ] Analytics is aggregate/anonymous only.
- [ ] Guest mode stores no PII.
- [ ] See [SECURITY.md](../SECURITY.md).
