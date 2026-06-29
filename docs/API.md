# API Specification (v2 — planned)

The v2 backend is a **FastAPI** service exposing a small, versioned REST API with auto-generated OpenAPI docs at
`/docs`. v1 needs no API (it runs entirely in the browser). This contract is the target the frontend and the
Python engine port are designed against.

> Base URL (free-tier example): `https://decode-your-pattern.onrender.com/api/v1`
> Auth: bearer JWT from OAuth (Google/GitHub) or a signed guest token. All responses are JSON.

## Conventions

- Versioned under `/api/v1`. Breaking changes bump the version.
- Errors use a consistent envelope: `{ "error": { "code": "string", "message": "string" } }`.
- Input is validated with Pydantic models; 422 on validation failure.
- Rate-limited per token. Idempotent where noted.

## Endpoints

### Auth
| Method | Path | Purpose |
|---|---|---|
| `GET` | `/auth/{provider}/login` | Start Google/GitHub OAuth |
| `GET` | `/auth/{provider}/callback` | OAuth callback → issues JWT |
| `POST` | `/auth/guest` | Issue an anonymous guest token |
| `GET` | `/auth/me` | Current user (or guest) profile |

### Assessments & reports
| Method | Path | Purpose |
|---|---|---|
| `GET` | `/quiz/templates` | List quiz templates + difficulty levels |
| `GET` | `/quiz/{template}/questions` | Randomised question set for a run |
| `POST` | `/assessments` | Submit answers → create assessment |
| `GET` | `/assessments/{id}/report` | Get the generated report |
| `GET` | `/reports` | List the user's reports (for the timeline) |
| `GET` | `/reports/compare?from={id}&to={id}` | Trend deltas between two reports |
| `GET` | `/reports/{id}/explain/{insightId}` | Why-this + confidence for one insight |
| `GET` | `/reports/{id}/pdf` | Server-rendered PDF (optional; v1 does this client-side) |

### AI Coach
| Method | Path | Purpose |
|---|---|---|
| `POST` | `/coach/{reportId}/message` | Ask the coach (LLM, report-scoped context) |
| `GET` | `/coach/{reportId}/history` | Conversation history |

### Analytics (admin only)
| Method | Path | Purpose |
|---|---|---|
| `GET` | `/admin/metrics?range=30d` | Aggregate, anonymous metrics |

## Example — submit an assessment

```http
POST /api/v1/assessments
Authorization: Bearer <token>
Content-Type: application/json

{
  "template": "default",
  "responses": [
    { "question_id": "awareness_3", "dimension": "awareness", "score": 8, "max": 8 },
    { "question_id": "patience_1",  "dimension": "patience",  "score": 3, "max": 7 }
  ]
}
```

```http
201 Created
{
  "assessment_id": "8f2c…",
  "report_url": "/api/v1/assessments/8f2c…/report"
}
```

## Example — explain an insight

```http
GET /api/v1/reports/8f2c…/explain/blind_spot
→ 200
{
  "type": "blind_spot",
  "observation": "Patience tends to be your most stretched area.",
  "why": "Driven mainly by responses to patience_1 and patience_4, which favoured quick exits.",
  "confidence": 0.78
}
```

## AI Coach contract (responsible-use)

The coach is **stateless per request**, receives only the report as context, and is system-prompted to refuse
therapy/diagnosis/professional advice. The LLM key lives **only** in backend environment variables — never in
the repo or the client. See [DEPLOYMENT.md](DEPLOYMENT.md) and [EXPLAINABILITY.md](EXPLAINABILITY.md#7-for-the-ai-coach-v2).
