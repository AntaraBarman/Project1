# Contributing to Decode Your Pattern

Thanks for your interest! This is an open, educational project — issues, ideas, and pull requests are welcome.

## Ways to contribute

- 🐛 **Report a bug** — open an issue with steps to reproduce and a screenshot.
- 💡 **Suggest a feature** — open an issue describing the idea and who it helps.
- ✍️ **Improve copy or docs** — typos, clearer explanations, better examples.
- 🎨 **Design/UX** — accessibility, contrast, motion, mobile polish.
- 🧠 **Engine rules** — propose new, well-explained behavioural observations (must follow the responsible-framing rules below).

## Ground rules (important)

This product makes **no clinical or scientific claims**. Any contribution to the intelligence engine must:

1. Phrase output as a **tendency/observation**, never a fact or diagnosis ("tends to", "suggests", "you may").
2. Include a **why** — what response pattern drove the observation.
3. Avoid deterministic predictions, shaming comparisons, and professional (medical/legal/financial) advice.

See [docs/EXPLAINABILITY.md](docs/EXPLAINABILITY.md).

## Local development

v1 is a single HTML file — no build step:

```bash
git clone https://github.com/AntaraBarman/Decode_Your_Pattern.git
cd Decode_Your_Pattern
# open index.html (article) or decode-your-pattern_6.html (assessment) in a browser
```

**Testing the engine (Node):** the intelligence functions are pure, so they can be exercised in Node by
extracting them into a small harness (see the patterns in the project history). Phase B adds `pytest`,
`vitest`, and `playwright`.

## Pull request checklist

- [ ] The change is focused and described clearly.
- [ ] Engine changes follow the responsible-framing rules.
- [ ] On-screen (dark) **and** PDF (light) themes both remain readable.
- [ ] No secrets, keys, or personal data committed.
- [ ] Docs updated if behaviour changed.

## Code style

- v1: vanilla JS/HTML/CSS, 2-space indent, descriptive names, small pure functions for engine logic.
- Phase B: ESLint + Prettier (frontend), Black + Ruff (backend), enforced by pre-commit hooks and CI.

## Code of conduct

Be kind, be constructive, assume good faith. Harassment or discrimination of any kind is not tolerated.
