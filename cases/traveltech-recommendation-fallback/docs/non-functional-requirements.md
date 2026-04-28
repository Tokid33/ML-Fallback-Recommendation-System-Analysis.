# Non-Functional Requirements

## Performance

- Recommendation Gateway must wait for ML no longer than 3 seconds in the user-facing flow.
- Fallback generation should be fast enough to keep the recommendation block usable.
- Frontend should show skeleton/loading while the response is being prepared.

## Reliability

- ML service failure must not break the recommendation block.
- Circuit breaker should open after repeated ML failures.
- Gateway should avoid repeated calls to a known unstable ML service until recovery.

## Privacy

- Fallback can use the user's own internal data.
- Fallback can use aggregated anonymous popularity.
- Fallback must not expose behavior of specific other users.
- User-facing explanation must be privacy-safe.

## Compatibility

- ML, fallback and hybrid modes must return the same response format.
- Frontend should not need to know internal recommendation source details.

## Observability

- Log response mode.
- Log fallback reason.
- Log response time.
- Log ML confidence.
- Log product quality metrics.
