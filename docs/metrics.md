# Metrics

## Технические метрики

- `fallback_rate` — доля ответов, где был использован fallback.
- `ml_timeout_rate` — доля запросов, где ML не ответил за 3 секунды.
- `low_confidence_rate` — доля ML-ответов ниже порога уверенности.
- `ml_avg_response_time` — среднее время ответа ML.
- `fallback_avg_response_time` — среднее время ответа fallback engine.
- `gateway_avg_response_time` — среднее время ответа Recommendation Gateway.
- `circuit_breaker_open_count` — сколько раз открывался circuit breaker.

## Продуктовые метрики

- `recommendation_ctr` — кликабельность рекомендаций.
- `booking_conversion` — конверсия из рекомендации в бронирование.
- `save_to_trip_rate` — доля рекомендаций, добавленных в поездку.
- `hide_rate` — доля скрытых рекомендаций.
- `complaint_rate` — доля жалоб или негативных реакций.
- `post_trip_feedback_score` — оценка рекомендаций после поездки.

## Как использовать метрики

Если fallback rate высокий, проблема может быть в ML-инфраструктуре или качестве модели.

Если ML mode даёт высокий CTR, а fallback сильно хуже, нужно улучшать fallback ranking.

Если fallback даёт близкий CTR к ML, это хороший признак: резервный контур реально сохраняет продуктовую ценность.

Если low confidence часто возникает в определённых городах или типах поездок, это сигнал для дообучения или улучшения признаков ML-модели.
