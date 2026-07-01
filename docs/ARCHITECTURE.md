# Architecture

Decode Your Pattern is built in two deliberate phases so it can be **free and live today** while leaving a
clean path to a **full production stack** tomorrow. The boundary between the two is the
*Behavioral Intelligence Engine* interface — everything else can change around it.

- [v1 — the live single-file app](#v1)
- [The Behavioral Intelligence Engine](#engine)
- [Request / report sequence](#sequence)
- [v2 — the planned full-stack platform](#v2)
- [Key design decisions](#decisions)

---

## <a name="v1"></a>v1 — the live single-file app (today, $0, zero-setup)

v1 is intentionally **dependency-light and serverless**. It runs entirely in the browser and deploys as static
files on GitHub Pages. This keeps it free and trivially shareable; the only thing that ever leaves the device
is an optional feedback submission (see below).

```mermaid
flowchart TD
  subgraph Browser["🌐 Browser (no server)"]
    direction TB
    Q[Quiz Engine<br/>randomised, weighted, resumable] --> SC[Scoring<br/>normalise → 10 dimensions]
    SC --> BIE[Behavioral Intelligence Engine<br/>rule-based · explainable · modular]
    BIE --> REP[Report Composer]
    REP --> VIS[Visualisations<br/>SVG radar/bars/gauge · Canvas card]
    REP --> EXP[Explainability layer]
    REP --> REC[Recommendation Engine]
    REP --> EXTRA[Badges · Wrapped · Avatar]
    REP --> FB[Optional feedback form]
    AUD[Web Audio API<br/>generative focus music]
  end
  Browser --> CDN[(CDN libs:<br/>jsPDF · html2canvas · fonts)]
  Browser --> PAGES[[GitHub Pages<br/>static hosting]]
  FB -.->|rating, comments, timestamp only| FORMSPREE[(Formspree)]
```

**Module map (within `decode-your-pattern_6.html`)**

| Concern | Responsibility |
|---|---|
| Quiz Engine | Question bank + random sampling, weighted scoring functions, progress, branching hooks |
| Scoring | Aggregates per-dimension raw/score, normalises to 0–10 and a 300–900 Pattern Score |
| Behavioral Intelligence Engine | Pure functions: `buildInsights`, `buildPatternPoints`, `buildScenarios`, `buildAvatar`, `buildDNA`, domains, recruiter mapping |
| Recommendation Engine | Maps weakest dimensions → books, talks, podcasts, habits, meditations, apps (each with a *why*) |
| Explainability | Frames every output as a tendency and surfaces the responses that drove it |
| Visualisation | Inline SVG (radar, bars, gauge) + Canvas (colourful share card) |
| Feedback | Optional end-of-report star rating + comments, posted to a third-party form service |

---

## <a name="engine"></a>The Behavioral Intelligence Engine

The engine is the heart of the product and is built as a set of **pure, testable functions** that take a
`dimScores` object and profile inputs and return structured, explainable output. This is the seam that lets v2
swap in ML/LLM scoring **without touching the UI**.

```mermaid
flowchart LR
  IN[dimScores 0–10<br/>+ profile + triggers] --> ENG{{Intelligence Engine}}
  ENG --> O1[Styles<br/>decision/comm/stress/learning]
  ENG --> O2[Pattern Notice<br/>point-by-point]
  ENG --> O3[Avatar + Story]
  ENG --> O4[Pattern DNA]
  ENG --> O5[Life Domains]
  ENG --> O6[Recruiter competencies]
  ENG --> O7[Scenarios + Action Plan]
  classDef e fill:#0e7490,stroke:#04EEDD,color:#fff
  class ENG e
```

**Interface (conceptual):**

```ts
interface IntelligenceEngine {
  analyze(input: AssessmentInput): Report;        // v1: rules · v2: rules + ML
  explain(insightId: string): Explanation;        // why-this + confidence
}
```

---

## <a name="sequence"></a>Request / report sequence

```mermaid
sequenceDiagram
  actor User
  participant Quiz as Quiz Engine
  participant Score as Scoring
  participant Engine as Intelligence Engine
  participant Report as Report Composer
  participant FB as Feedback Form

  User->>Quiz: answer questions (randomised)
  Quiz->>Score: per-dimension responses
  Score->>Engine: normalised dimScores + profile
  Engine->>Engine: rule evaluation (pure functions)
  Engine->>Report: insights + explanations + recs
  Report-->>User: render report
  User->>FB: optional rating + comments
  FB-->>User: thank-you message (no redirect)
```

---

## <a name="v2"></a>v2 — the planned full-stack platform (free-tier)

v2 introduces accounts, persistence across devices, an LLM AI Coach, and an anonymous analytics dashboard —
all deployable on **free tiers** (Vercel + Render + Supabase/Neon + a free LLM key).

```mermaid
flowchart TD
  subgraph FE["Frontend — Vercel / Pages (free)"]
    R[React + TypeScript + Tailwind<br/>Framer Motion · Recharts/D3]
  end
  subgraph BE["Backend — Render / Fly (free tier)"]
    API[FastAPI<br/>REST + OpenAPI docs]
    ENG2[Intelligence Engine<br/>same interface, Python port]
    COACH[AI Coach service<br/>LLM, report-scoped context]
  end
  DB[(PostgreSQL — Supabase/Neon<br/>SQLite for local dev)]
  LLM[[Free-tier LLM<br/>Groq / Gemini]]
  AUTH[[OAuth<br/>Google · GitHub · guest]]
  ANALYTICS[[PostHog / GA<br/>aggregate, anonymous]]

  R -->|HTTPS / JSON| API
  R --> AUTH
  API --> ENG2
  API --> COACH
  COACH --> LLM
  API --> DB
  R --> ANALYTICS
```

**Why this shape stays $0:** static frontend (Pages/Vercel free), a single small FastAPI service on a free
container host, a free managed Postgres, and a free-tier LLM key held **only** server-side (never in the public
repo). See **[DEPLOYMENT.md](DEPLOYMENT.md)**.

---

## <a name="decisions"></a>Key design decisions

| Decision | Rationale | Trade-off |
|---|---|---|
| **Single-file v1** | Zero build, zero cost, instant share, total privacy | Harder to unit-test in isolation; addressed by pure functions + Node test harness |
| **Rule-based engine first** | Explainable, deterministic, free, no key | Less "magic" than an LLM; mitigated by a swappable interface for v2 |
| **`localStorage` for history** | Private by default, no backend needed | Per-device only; v2 accounts add cross-device sync |
| **Inline SVG + Canvas** | No chart library to ship; full control; renders in PDF | More manual code; isolated in render helpers |
| **Randomised question draw** | Replayable, reduces gaming/memorisation | Scores vary slightly run-to-run; documented as a feature, normalised in scoring |
| **Explainability as a first-class layer** | Responsible framing; builds trust | More copy to maintain; centralised in the engine |
