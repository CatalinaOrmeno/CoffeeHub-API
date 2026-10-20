# ADR-0002: Monolito modular con eventos internos

- **Estado:** Aceptada
- **Fecha:** 2026-01-06

## Contexto

Subscriptions debe notificar a catalog y audit sin crear dependencias directas
entre sus servicios. El alcance exige costo cero y no justifica desplegar un
bróker.

## Decisión

Organizar el código por módulos de dominio dentro de un único Spring Boot y
usar `ApplicationEventPublisher` con `@EventListener`. La operación de
suscripción se persiste en PostgreSQL y luego publica `SubscriptionPaidEvent`;
catalog puede producir `LowStockAlertEvent`.

## Alternativas descartadas

- RabbitMQ/Kafka: agregan infraestructura, operación y costos fuera del
  objetivo.
- Microservicios desde el inicio: aumentan complejidad sin una demanda que lo
  justifique.
- Llamadas directas entre módulos: acoplan el núcleo transaccional a efectos
  secundarios.

## Consecuencias

El diseño es simple, testeable y permite una futura extracción de servicios.
Los eventos internos no ofrecen durabilidad ni reintentos distribuidos; si la
operación exige esas garantías deberá adoptarse un outbox y un broker.