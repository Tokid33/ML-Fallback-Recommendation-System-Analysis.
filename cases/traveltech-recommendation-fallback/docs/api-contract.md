# API Contract Notes

Frontend should not depend on the internal recommendation source. The response must support all modes with one structure.

## Fields

- `mode`: recommendation source mode. Possible values: `ml`, `fallback`, `hybrid`.
- `reason`: optional fallback reason. Possible values: `ml_unavailable`, `ml_timeout`, `low_confidence`, `partial_low_confidence`.
- `generated_at`: response timestamp.
- `recommendations`: list of recommendation items.
- `id`: internal item identifier.
- `type`: recommendation type, for example `hotel`, `activity`, `route`, `transfer`.
- `title`: display title.
- `score`: internal ranking score.
- `confidence`: optional ML confidence, if applicable.
- `explanation`: safe user-facing explanation.

## Contract rule

ML, fallback and hybrid modes must return the same response shape. This keeps frontend simple and prevents UX breakage when the backend switches modes.
