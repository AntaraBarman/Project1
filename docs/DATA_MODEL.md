# Data Model

## v1 — on-device (today)

v1 has **no database**. The only persisted data is a private, per-device history used for the progress timeline,
stored in `localStorage` under the key `dyp-history`:

```jsonc
// localStorage["dyp-history"]  (capped to the latest 30 entries)
[
  { "d": 1750000000000, "score": 612 },   // d = epoch ms, score = Pattern Score (300–900)
  { "d": 1752600000000, "score": 655 }
]
```

Everything else (answers, dimension scores, the full report) lives only in memory for the duration of the
session and is never written to disk or sent anywhere.

**In-memory report shape (conceptual):**

```ts
type DimensionKey =
  | 'awareness' | 'action' | 'discipline' | 'resilience' | 'adaptability'
  | 'selftrust' | 'boundaries' | 'patience' | 'clarity' | 'growth';

interface Report {
  patternScore: number;              // 300–900
  band: 'High-friction' | 'Developing' | 'Solid' | 'Strong' | 'Exceptional';
  dimensions: Record<DimensionKey, number>;   // 0–10 each
  archetype: string;
  avatar: { name: string; emoji: string };
  insights: Insight[];               // styles, hidden potential, blind spots
  domains: { name: string; pct: number }[];
  competencies: { name: string; pct: number }[];
  toolkit: Recommendation[];
  actionPlan: { week: string[]; month: string[]; year: string[] };
}
```

## v2 — relational schema (planned, free-tier Postgres / SQLite)

```mermaid
erDiagram
  USER ||--o{ ASSESSMENT : takes
  ASSESSMENT ||--|| REPORT : produces
  ASSESSMENT ||--o{ RESPONSE : contains
  REPORT ||--o{ INSIGHT : contains
  REPORT ||--o{ RECOMMENDATION : contains
  USER ||--o{ COACH_MESSAGE : sends
  REPORT ||--o{ COACH_MESSAGE : grounds

  USER {
    uuid id PK
    string auth_provider "google | github | guest"
    string external_id "nullable for guest"
    string display_name
    timestamptz created_at
  }
  ASSESSMENT {
    uuid id PK
    uuid user_id FK
    string template "default | recruiter | ..."
    int question_count
    timestamptz started_at
    timestamptz completed_at
  }
  RESPONSE {
    uuid id PK
    uuid assessment_id FK
    string question_id
    string dimension
    int score
    int max
  }
  REPORT {
    uuid id PK
    uuid assessment_id FK
    int pattern_score
    string band
    jsonb dimensions
    jsonb payload "full structured report"
    timestamptz created_at
  }
  INSIGHT {
    uuid id PK
    uuid report_id FK
    string type "decision | communication | blind_spot | ..."
    text observation
    text why
    int confidence
  }
  RECOMMENDATION {
    uuid id PK
    uuid report_id FK
    string category "book | podcast | talk | habit | app"
    string title
    text why
  }
  COACH_MESSAGE {
    uuid id PK
    uuid user_id FK
    uuid report_id FK
    string role "user | assistant"
    text content
    timestamptz created_at
  }
```

**Analytics (aggregate, anonymous):** a separate `daily_metrics` rollup table (date, DAU, completion_rate,
avg_duration, top_strength, top_growth_area, pdf_downloads, return_rate, device_breakdown) populated by a
scheduled job — **never** joined back to individual `USER` or `RESPONSE` rows. See [SECURITY.md](../SECURITY.md).

**Privacy rules baked into the schema**
- Guest users have `external_id = NULL` and no PII.
- `RESPONSE` rows can be purged after the `REPORT` is generated (configurable retention).
- The analytics rollup stores **counts only**, never raw answers or identifiers.
