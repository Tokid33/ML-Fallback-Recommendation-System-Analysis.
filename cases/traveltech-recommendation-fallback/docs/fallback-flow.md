# Fallback Flow

## Main flow

1. User opens recommendations for a trip.
2. Frontend sends request to Recommendation Gateway.
3. Gateway collects context:
   - user profile;
   - trip dates;
   - destination;
   - route;
   - booked items;
   - group participants;
   - budget and schedule constraints.
4. Gateway calls ML recommendation service with a 3-second timeout.
5. Gateway validates response:
   - service availability;
   - response time;
   - confidence;
   - response structure.
6. If ML is healthy and confidence is enough, Gateway returns ML recommendations.
7. If ML is unavailable, slow or low-confidence, Gateway calls fallback engine.
8. Fallback engine builds rule-based recommendations from internal data.
9. Gateway returns recommendations in a unified contract.
10. Gateway logs mode, reason, timing and quality signals.

## Late ML response handling

If fallback has already been shown and ML later returns good recommendations, the UI should not reorder the list automatically. A soft update option is safer.

Possible UI text:

"We found more relevant options. Refresh recommendations?"
