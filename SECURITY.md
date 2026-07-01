# Security & Privacy Policy

## Reporting a vulnerability

If you find a security issue, please **do not open a public issue**. Email the maintainer (via the GitHub
profile [@AntaraBarman](https://github.com/AntaraBarman)) with details and steps to reproduce. You'll get an
acknowledgement as soon as possible.

## Privacy posture

### v1 (today)
- Runs **entirely in the browser**. The assessment, scoring, and report never leave your device.
- The only external call is the **optional feedback form** at the end of the report: if submitted, it sends
  your star rating, helpful/not-helpful answer, comments, feature suggestion, and a timestamp to a third-party
  form service (Formspree). No name, email, or report content is included, and nothing is sent unless you
  press "Submit Feedback." A local copy is also kept in `localStorage` as a delivery safety net.
- No accounts, no cookies for tracking, no third-party analytics.

### v2 (planned)
- **Secrets never live in the repo.** OAuth and LLM keys are backend environment variables only.
- **Analytics is aggregate and anonymous** — counts and rollups only, never raw responses or identities.
- **Guest mode stores no PII.** Raw responses can be purged after report generation (configurable retention).
- **CORS** is restricted to known origins; all input is validated server-side (Pydantic).
- The **AI Coach** receives only the generated report as context and is constrained from giving therapy,
  diagnosis, or professional advice.

## Responsible-use disclaimer

Decode Your Pattern is a **self-reflection tool**, not a medical, psychological, legal, or financial service.
It must not be used to make consequential decisions about a person (e.g., hiring, clinical, or legal
decisions). Insights are observations derived from user responses, not assessments of fact.
