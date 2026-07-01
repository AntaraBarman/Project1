# Roadmap

A staged path from a free, live, single-file app to a full personal-intelligence platform — kept honest about
what's $0-on-Pages versus what needs free-tier hosting.

## ✅ Phase 0 — Live MVP (done)
Interactive 14-rule article + 10-dimension assessment, Pattern Score, charts, scenarios, music, PDF — all
client-side and live on GitHub Pages.

## ✅ Phase 1 — Premium, explainable report (done)
Style insights (decision/communication/stress/learning), hidden potential & blind spots, badges,
growth toolkit (book/talk/podcast/habit/meditation/app), colourful share card.

## ✅ Phase 2 — Identity & depth (done)
Symbolic avatar, story mode + human-voice phrasing, **Pattern DNA**, life domains, recruiter mode, Mind Wrapped.
*(An on-device Pattern Score history/timeline shipped in this phase was later removed — see [CHANGELOG.md](CHANGELOG.md) — in favour of keeping each report self-contained.)*

## 🟦 Phase A — Production polish (in progress)
World-class README, architecture + sequence + ERD diagrams, case study, explainability spec, API spec,
deployment guide, contributing/security, CI workflow, changelog, license. *(This document is part of it.)*

## 🚧 Phase B — Full-stack platform (next, free-tier)
> Requires free accounts + keys (Render/Supabase/Vercel/Groq); not pure GitHub Pages.

- **Frontend:** React + TypeScript + Tailwind + Framer Motion + Recharts/D3; dark mode, skeletons, a11y.
- **Backend:** FastAPI; the engine ported to Python behind the same interface; OpenAPI docs.
- **Data:** Postgres (Supabase/Neon) + SQLite dev; the schema in [docs/DATA_MODEL.md](docs/DATA_MODEL.md).
- **Auth:** Google + GitHub OAuth + anonymous guest mode.
- **AI Coach:** LLM (free-tier key, server-side only), report-scoped, responsibly constrained.
- **Testing/CI:** pytest, vitest, playwright; ruff/black/eslint/prettier; pre-commit; GitHub Actions.

## 🔭 Phase C — Intelligence & engagement
- **Decision Simulator** — infer decision style (analytical/risk-taker/strategic…) from scenario choices.
- **Confidence meter** per insight ("based on answers 3, 14, 19…").
- **Behavioral network graph** and **Growth Simulator** (move a slider → see modelled domain effects, framed as exploration).
- **Life-domain sub-reports** with their own action plans.

## 🌍 Phase D — Community & scale (needs backend)
- **Cross-user benchmarks** ("you scored 87 vs an average of 61") from aggregate, anonymous data.
- **Cross-device Digital Twin** — persistent profile, monthly re-assessment, long-term trends.
- **Anonymous analytics dashboard** — DAU, completion rate, common categories, return rate, device split.
- Internationalisation; multiple quiz templates and difficulty levels.

## Guiding constraints
- **$0 to run.** Static where possible; free tiers where not.
- **Privacy first.** On-device by default; aggregate-anonymous analytics only.
- **Explainable always.** Every insight is a tendency with a *why* — never a diagnosis.
