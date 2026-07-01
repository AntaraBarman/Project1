# Changelog

All notable changes to this project are documented here.
The format is based on [Keep a Changelog](https://keepachangelog.com/), and this project aims to follow
[Semantic Versioning](https://semver.org/).

## [1.5.1] — 2026-07 — Feedback: handle the free-tier monthly cap
### Fixed
- Formspree's free plan caps submissions at 50/month; once hit, it returns a 429. The feedback form now saves its local `localStorage` backup **before** attempting the network call (not just on generic failure), and specifically detects a 429 to show an honest message — "we've hit our feedback limit for this month, try again in a few days" — instead of a generic "try again" that wouldn't actually help until the cap resets.

## [1.5.0] — 2026-07 — Feedback, UI polish, purple removed
### Added
- **"Help Us Improve" feedback form** at the end of every report: a 1–5 star rating and Yes/Partially/No helpfulness question (both required), plus optional comments and a feature-suggestion field. Submits inline via AJAX with no page redirect, shows a simple thank-you message, and is fully keyboard/screen-reader accessible (native radio inputs styled as stars and choice pills). No name or email is ever collected.
- Feedback submissions POST to a free [Formspree](https://formspree.io) endpoint (see [DEPLOYMENT.md](docs/DEPLOYMENT.md) for the one-time setup) and are also mirrored into `localStorage` as a local delivery safety net.
### Changed
- The three report-action buttons (**Retake with new questions**, **Shareable Card**, **Download Your Report**) are now a uniform width instead of sizing to their own label length; on narrow screens they stack full-width.
- Replaced the last purple accent color (`#a855f7`, used for the Self-trust dimension throughout the radar chart, dimension bars, DNA blocks, badges, and related insight text) with orange (`#f97316`) for better visual distinction from the rest of the palette.
### Removed
- The **"Progress" note** ("last time you scored X, now Y…") shown under badges, along with its underlying `localStorage`-based score history — the last remaining piece of the score-tracking feature (the chart itself was removed in 1.4.0). Each report is now fully self-contained.
- Several dead CSS rules discovered during this cleanup: unused `section-card` accent classes (`accent-nature`, `accent-context`, `accent-pattern`, `accent-strength`, `accent-improve`, `accent-brain`, `accent-watch`, `accent-sticky`, `accent-triggers` — none were referenced by any element) and the leftover `.deliver-grid` styles from the EmailJS delivery feature removed in 1.3.0.

## [1.4.1] — 2026-07 — PDF export reliability fix
### Fixed
- **PDF download could hang indefinitely or fail silently.** `downloadPdf()` opened with an unguarded double-`requestAnimationFrame` wait that never resolves in a backgrounded/hidden browser tab — now raced against a short timer so it can never block forever.
- Each report section capture (`html2canvas`) now has a hard 20-second timeout, and the whole build has a 60-second overall deadline, so one slow or stuck section can no longer freeze the entire download.
- A section that fails or times out is now skipped rather than aborting the whole report; the final status message tells you if any sections were left out, instead of leaving the button stuck at "Building your PDF…" with no feedback.
- If every section fails, the export now surfaces a clear error instead of silently producing an empty or corrupt file.

## [1.4.0] — 2026-07 — Copy & UX cleanup
### Changed
- Removed all "game" framing from the assessment's on-screen copy, insight text, and internal code (result eyebrow, avatar-reveal headline, insight/recommendation strings, function names) in favour of neutral, pattern-focused language.
- Simplified the Pattern Score description on the landing page (removed the "but for your behaviour" qualifier).
- Renamed the report actions: **"Download my report as PDF"** → **"Download Your Report"**, **"Get my shareable card"** → **"Shareable Card"**.
- Renamed the primary CTAs for consistent, direct phrasing: **"Get my report"** → **"Get Your Report"**, **"Decode my pattern"** → **"Decode Your Pattern"**, **"See my report"** → **"See Your Report"**, **"Start the assessment"** → **"Start The Assessment"**.
### Removed
- The **Pattern Score history timeline** ("your last N attempts" chart) and its supporting code.
- The standalone **"Save report as file"** (plain HTML download) button and its now-unused helper functions.

## [1.3.0] — 2026-06 — Production-hardening audit
### Security
- Removed a **self-XSS vector**: user-entered goal/obstacle text is now rendered via `textContent`, never `innerHTML`, in the action plan.
- Dropped the unused **EmailJS** dependency and its placeholder config (one fewer third-party script and no dangling keys).
### Performance
- One fewer external script load on the assessment page (EmailJS removed).
### SEO / sharing
- Added **Open Graph + Twitter Card** meta and a dedicated `social-preview.png` (1200×630) so links render a rich preview on LinkedIn/X. Added `theme-color` and `canonical`.
### Accessibility
- Landing now respects `prefers-reduced-motion` and adds visible `:focus-visible` outlines.
### DevOps
- CI now runs the **backend test suite** (`pytest`) on every push; added a backend `.dockerignore`.

## [Unreleased]
### Planned (v2)
- React + TypeScript + Tailwind frontend; FastAPI backend; Postgres/SQLite.
- OAuth (Google/GitHub) + guest mode; cross-device report history.
- LLM-powered **AI Coach** (report-scoped, free-tier key).
- **Decision Simulator**, cross-user **benchmarks**, and an anonymous **analytics dashboard**.

## [1.2.0] — 2026-06
### Added
- **Behavioral DNA** signature, **Life Domains**, **Recruiter Mode**, **Symbolic Avatar**, **Story Mode**,
  **Mind Wrapped**, and a **Pattern Score timeline**.
- Premium **style insights** (decision-making, communication, stress, learning, hidden potential, blind spots).
- **Badges** + on-device **progress tracking**.
- **Growth toolkit**: book, talk, podcast, daily habit, meditation, app — matched to weakest dimensions.
- **Colourful share card** (Canvas → PNG) for LinkedIn/WhatsApp.
- Production documentation suite: architecture (+Mermaid), case study, explainability, data model, API, deployment.

### Changed
- Questions now **drawn at random each run** from an expanded pool (~47 per run) — no two playthroughs alike.
- Brighter, higher-contrast theme; per-question colour "zones"; checkpoint celebrations.

### Fixed
- PDF export contrast for the honest-mirror banner, scenario titles, and footer.
- Profile-question render crash; readability of locked badges and progress note.

## [1.1.0] — 2026-06
### Added
- Credit-score-style **Pattern Score** gauge, **radar** + colour **bars**, point-by-point **Pattern Notice**,
  difficulty **scenarios**, animated **brain**, **focus music**, and **PDF export**.

## [1.0.0] — 2026-06
### Added
- Interactive **"Life Is a Game" 14-rule** article with unlockable levels and a gated Final Truth.
- Core **assessment** scoring 10 behavioural dimensions and rendering a living game board.
- LinkedIn launch assets (carousel, article, cover).
