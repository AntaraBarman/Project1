# Case Study — Decode Your Pattern

*A portfolio write-up of the product, engineering, and design decisions behind a personal-intelligence platform
that runs free, in the browser, with explainable insights.*

---

## 1. Problem statement

People obsessively track steps, sleep, calories, and even their credit score — yet almost no one has a clear,
honest read of the **behavioural patterns** quietly driving their career, money, relationships, and health.
Existing "personality tests" tend to (a) hand over a flattering label with no reasoning, (b) lock results behind
paywalls or sign-ups, or (c) over-claim scientific authority.

**Goal:** build a free, private, *explainable* tool that turns a few honest answers into a personalised map of
how someone operates — framed as reflection prompts, not diagnoses — and lets them watch it change over time.

## 2. Users & research assumptions

> Note: this is a portfolio project; the personas below are explicit *assumptions*, not validated research.

| Persona | Need | Success looks like |
|---|---|---|
| **The reflective professional** | An honest mirror, not a horoscope | "That blind-spot section was uncomfortably accurate." |
| **The self-improver** | Concrete next steps, not vibes | Uses the weekly/monthly action plan. |
| **The sharer** | Something worth posting | Downloads the share card; sends to friends. |
| **The recruiter-viewer** | Evidence of skill | Reads the repo as a production-grade project. |

**Core insight that shaped the product:** trust comes from *explanation*. People believe an insight far more when
it says *"we observed this because…"* than when it states a flat verdict. Explainability became a first-class
feature, not an afterthought.

## 3. Product decisions

1. **"Observation, never verdict."** Every insight is phrased as a tendency derived from the user's own
   responses. A persistent banner reinforces that this is reflection, not clinical assessment.
2. **Free, private, no sign-up.** v1 runs 100% client-side. This removed the biggest friction (accounts) and the
   biggest risk (storing personal data) in one move.
3. **Make it feel earned, not like a form.** Colour-coded "zones" per question, checkpoints, badges, a Pattern Score, and a
   colourful shareable card raise completion and shareability without diluting the substance.
4. **Randomise the questions.** Each run draws a fresh subset, so the tool is replayable and harder to reverse-engineer answers for.
5. **Layer depth gradually.** Headline (Pattern Score) → shape (radar/DNA) → meaning (styles, scenarios) →
   action (toolkit, plan) → share (Wrapped, card). Casual users get value early; engaged users go deep.

## 4. Technical trade-offs

| Choice | Why | Cost |
|---|---|---|
| **Single-file v1** | $0 hosting, instant load, zero build, total privacy | Testing isolation is harder → solved with pure functions + a Node harness |
| **Rule-based engine** | Explainable + deterministic + free | Less "AI magic" → mitigated by a swappable engine interface for v2 |
| **Inline SVG/Canvas over chart libs** | Smaller payload, full control, prints to PDF cleanly | More hand-written render code → isolated in helpers |
| **`localStorage` history** | Private, no backend | No cross-device sync → planned for v2 accounts |
| **Static now, full-stack later** | Ship value immediately; avoid premature infra | A second architecture to maintain → kept behind a stable engine interface |

## 5. Scalability considerations

- **v1 scales for free indefinitely** — static assets on a CDN have effectively no per-user cost.
- **v2 read/write paths** are separated so the analytics dashboard reads a pre-aggregated table rather than
  scanning raw events; the AI Coach is a stateless service that receives the report as context (no training, no
  PII retention).
- **Engine is pure and cacheable** — identical inputs yield identical outputs, enabling memoisation and trivial
  horizontal scaling.

## 6. Performance optimisation

- No framework or chart library in v1 → first paint is near-instant.
- Charts are inline SVG (no canvas reflow); the share card uses Canvas only on demand.
- Bar/score animations use CSS transforms (GPU-friendly); `prefers-reduced-motion` is respected on the board.
- Reaction counts and PDF/Canvas work are **lazy** — nothing heavy runs until the user asks.

## 7. Accessibility decisions

- Semantic headings, `aria-label`s on every chart/SVG, and keyboard-usable controls.
- Colour is never the *only* signal — scores show numbers and letter grades alongside colour.
- High-contrast text reviewed for both the dark on-screen theme and the light PDF theme.
- Motion is decorative and reducible; content is fully readable without it.

## 8. Privacy considerations

- **v1 stores nothing off-device.** No accounts, no analytics, no network calls with personal data.
- The per-rule reaction counter is the only external call and is **anonymous and aggregate** (a hit counter),
  with a graceful local fallback.
- **v2** will collect only **aggregate, anonymous** product metrics (completion rate, common categories) — never
  individual responses or identities. See [SECURITY.md](../SECURITY.md).

## 9. Lessons learned

- **Explainability is a feature, not documentation.** Adding "why we observed this" measurably changes how real
  the report feels.
- **Constraints sharpen design.** "No backend" forced a cleaner, more private, more shareable product than a
  database-first approach would have.
- **Pure functions pay off.** Because the engine is side-effect-free, it was testable in Node even though the app
  ships as one HTML file — and it's ready to be reused verbatim by the v2 Python backend.
- **Ship the honest version.** Low scores aren't softened; that candour is exactly what users said made it
  trustworthy.

## 10. What's next

See **[ROADMAP.md](../ROADMAP.md)** — v2 brings accounts, an LLM AI Coach, a decision simulator, cross-user
benchmarks, and an anonymous analytics dashboard, all on free tiers.
