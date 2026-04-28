# TravelTech Recommendation Fallback Case

## Context

TravelTech is a trip planning service. A user can search and book tickets, hotels, transfers and activities, then keep the whole trip in one interface. The product also supports group trips, schedule sync, notifications, feedback and personalized recommendations.

The recommendation block suggests hotels, activities and routes. In the main scenario it uses an ML model. The risk is simple: if the model fails, responds too slowly or returns a weak result, a key product scenario becomes unstable.

This case describes a fallback recommendation scenario that keeps the user experience working without pre-caching ML predictions.

## Business problem

Personalized recommendations affect engagement, trip planning depth and booking conversion. If the recommendation block breaks, the user may see an empty state, wait too long or lose trust in the product.

Business risks:

- lower CTR on recommendations;
- lower conversion to bookings;
- more exits from the trip planning flow;
- dependency of a visible UX block on one ML service;
- weaker trust in personalization.

## My role

I acted as a system / business analyst for the recommendation flow.

My scope:

- described failure scenarios for the ML recommendation service;
- designed fallback rules based on internal product data;
- defined ML, fallback and hybrid modes;
- described UX behavior during graceful degradation;
- prepared API response example;
- wrote acceptance criteria in Given/When/Then format;
- defined logging, metrics, privacy and non-functional requirements.

## Fallback triggers

Fallback mode starts when one of these conditions is true:

1. ML service is unavailable:
   - HTTP 5xx;
   - network error;
   - service unavailable;
   - circuit breaker is open.

2. ML response takes more than 3 seconds.

3. ML returns low confidence, for example confidence < 0.6.

The user should not see a technical error. The frontend should still receive recommendations in the same contract.

## Key constraint

The system must not pre-cache ML predictions. Travel interests can change fast, especially after the user changes the destination, budget, dates, hotel or group composition.

Fallback recommendations must be generated at request time from internal data.

## Proposed solution

Introduce a Recommendation Gateway between frontend and recommendation sources.

The gateway hides whether recommendations came from ML, fallback or hybrid mode. Frontend always receives one response format.

Recommendation Gateway responsibilities:

- receive recommendation requests from frontend;
- collect user and trip context;
- call ML service with a 3-second timeout;
- validate ML availability and confidence;
- call fallback engine when ML is not reliable enough;
- return one unified response to frontend;
- log mode, fallback reason, response time and quality signals.

## Data used by fallback

Fallback uses only internal data and aggregated anonymous popularity.

User data:

- profile interests;
- preferred budget;
- trip history;
- views, saves and bookings;
- post-trip feedback;
- preferred hotel/activity categories.

Current trip context:

- destination;
- dates and trip duration;
- selected tickets, hotel and activities;
- free time windows;
- route and location context;
- time zones;
- solo, couple, family or group trip.

Aggregated anonymous data:

- popular activities by city and season;
- popular hotels by destination;
- rating and review count;
- anonymous popularity among users with similar trip context;
- booking frequency by destination, trip type and date range.

## Fallback ranking logic

Each candidate receives a rule-based score.

Possible score factors:

- match with user interests;
- budget match;
- distance to hotel or current route;
- no schedule conflict;
- anonymous popularity;
- rating and review count;
- availability for selected dates;
- match with trip composition;
- diversity of the recommendation list.

Example:

If the user usually chooses museums, calm walks and 4-star hotels, and the current trip is a family trip, fallback should not rank night clubs or extreme activities first. It should rank museums, city walks, family activities, restaurants near the hotel and options that fit the schedule.

For group trips, fallback can use a combined profile. It should look for overlapping interests and downgrade options that conflict with a participant's restrictions or schedule.

## Recommendation modes

### ML mode

Used when ML responds within 3 seconds and confidence is above the threshold.

### Fallback mode

Used when ML is unavailable, too slow or returns low confidence.

### Hybrid mode

Used when part of ML output is good enough. Strong ML recommendations remain in the list. Weak positions are replaced with fallback candidates.

## Smooth UX switching

The UX goal is graceful degradation. The user should not feel that the product broke.

Rules:

- show skeleton/loading while recommendations are being prepared;
- if ML does not respond in 3 seconds, show fallback recommendations;
- if ML responds later, do not abruptly reorder the list already visible to the user;
- optionally show a soft update message: "We found more relevant options" with a refresh button;
- do not show internal reasons like ml_timeout or low_confidence to the user;
- use a neutral explanation: "Selected based on your profile and traveler popularity".

## API response example

See [`api-response-example.json`](./api-response-example.json).

Example fields:

- `mode`: ml / fallback / hybrid;
- `reason`: fallback reason;
- `generated_at`: response generation time;
- `recommendations`: recommendation items;
- `score`: internal ranking score;
- `explanation`: user-friendly explanation.

## Logging and metrics

Log technical and product quality signals:

- response mode: ml, fallback, hybrid;
- fallback reason: ml_unavailable, ml_timeout, low_confidence;
- ML response time;
- fallback response time;
- ML confidence;
- recommendation count;
- CTR;
- booking conversion;
- hide/complaint events;
- post-trip feedback.

Key metrics:

- fallback rate;
- average response time;
- timeout rate;
- low confidence rate;
- recommendation CTR;
- booking conversion;
- post-trip feedback score.

## Non-functional requirements

Performance:

- ML waiting time must not exceed 3 seconds for the user-facing flow.
- Fallback should return results fast enough to keep the recommendation block usable.

Reliability:

- ML failure must not break the trip planning flow.
- Circuit breaker should prevent repeated calls to an unstable ML service.

Privacy:

- fallback uses only internal user data and aggregated anonymous popularity;
- no behavior of specific other users should be exposed.

Compatibility:

- ML, fallback and hybrid modes return the same frontend contract.

Observability:

- mode, reason, timing and quality metrics must be logged.

## Acceptance criteria

Detailed criteria are in [`acceptance-criteria.md`](./acceptance-criteria.md).

## Expected business effect

The solution protects a key product scenario from ML instability.

Expected effect:

- users keep receiving recommendations even when ML fails;
- recommendation block does not become an empty state;
- booking conversion is less exposed to ML downtime;
- product team can compare ML and fallback quality;
- failures become measurable through fallback rate, timeout rate and conversion metrics;
- personalization remains safer because fallback uses internal and anonymized data.

## STAR summary

Situation: the TravelTech recommendation flow depended on an ML service. When ML failed, responded slowly or returned low-confidence results, the user could receive a broken recommendation block.

Task: design a fallback scenario that keeps UX continuous without pre-caching ML predictions.

Action: I designed a Recommendation Gateway, fallback engine, ML/fallback/hybrid modes, 3-second timeout, confidence threshold, ranking logic, unified API response, privacy rules, metrics and acceptance criteria.

Result: the product keeps showing useful recommendations during ML failures, while the business protects engagement and booking conversion and can measure the quality of both ML and fallback results.

## Resume snippet

TravelTech Recommendation Fallback Case: designed a fallback scenario for ML recommendations when the ML service is unavailable, responds slower than 3 seconds or returns low confidence. Described fallback ranking on internal data, ML/fallback/hybrid modes, API response, UX switching, privacy rules, logging, metrics and acceptance criteria.
