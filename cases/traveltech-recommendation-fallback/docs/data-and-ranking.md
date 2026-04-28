# Data and Ranking Logic

## Candidate sources

Candidates can be hotels, activities, routes or transfers available for the selected destination and dates.

## Input features

User features:

- interests;
- budget range;
- historical trip types;
- historical booking categories;
- saved places;
- post-trip feedback.

Trip features:

- destination;
- date range;
- hotel location;
- route;
- free time windows;
- group size;
- trip type;
- time zone.

Item features:

- category;
- price;
- rating;
- review count;
- location;
- availability;
- duration;
- popularity by city and season.

## Example scoring components

- Interest match: 0-1
- Budget match: 0-1
- Distance score: 0-1
- Schedule fit: 0-1
- Anonymous popularity: 0-1
- Rating score: 0-1
- Diversity penalty: 0-1

## Example formula

`score = 0.25 * interest_match + 0.15 * budget_match + 0.15 * schedule_fit + 0.15 * distance_score + 0.15 * popularity + 0.10 * rating_score - 0.05 * diversity_penalty`

The formula is intentionally simple. The purpose of fallback is not to beat ML, but to keep the user flow alive with understandable and safe recommendations.
