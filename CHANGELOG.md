# Changelog

All notable changes to this project are documented here.
The format is based on [Keep a Changelog](https://keepachangelog.com/), and this project aims to follow
[Semantic Versioning](https://semver.org/).

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
