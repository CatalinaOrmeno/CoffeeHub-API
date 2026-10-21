# Declaración de Alcance — CoffeeHub API

## Propósito

CoffeeHub es un caso de estudio de un club de café de especialidad. El sistema
demuestra cómo construir un backend empresarial para suscripciones y despacho
en Latinoamérica, manteniendo una arquitectura modular dentro de un único
ejecutable Spring Boot.

## Objetivos

1. Implementar un flujo trazable de suscripción pagada, consumo de inventario y
   auditoría.
2. Separar los dominios mediante módulos y eventos internos de Spring.
3. Proteger el acceso con Microsoft Entra ID, OIDC/OAuth 2.0 y App Roles.
4. Exponer una API REST segura para clientes y administración.
5. Mantener el costo de infraestructura en $0 USD usando servicios gratuitos,
   Docker local y beneficios del GitHub Student Developer Pack.
6. Documentar decisiones, planificación y criterios de aceptación junto al
   código fuente.

## Incluido

- Gestión de usuarios y autoridades `ROLE_CLIENT` y `ROLE_ADMIN`.
- Catálogo de cafés, formatos, precios y stock.
- Planes mensual y trimestral, fechas de corte y estado de cobro.
- Descuento de inventario ante `SubscriptionPaidEvent`.
- `LowStockAlertEvent` cuando el stock queda bajo el umbral configurado.
- Persistencia transaccional en PostgreSQL.
- Persistencia de auditoría/eventos en MongoDB BSON.
- Backend REST protegido bajo `/api/v1/**`.
- OpenAPI/Swagger UI, Actuator, pruebas con JUnit 5 y Testcontainers.
- Docker Compose para el entorno local y pipeline de documentación.

## Fuera de alcance

- Integración con RabbitMQ, Kafka u otro bróker externo.
- Aplicación móvil nativa.
- Procesamiento real de pagos, facturación fiscal o integración bancaria.
- Gestión de transportistas externos y optimización de rutas.
- Multi-tenancy, marketplace o internacionalización completa.
- Alta disponibilidad, escalado horizontal y soporte de producción 24/7.
- Construcción de una plataforma de microservicios.

Estas exclusiones son deliberadas: el objetivo es demostrar diseño y
operación de un caso funcional sin sobreingeniería ni costos de infraestructura.

## Restricciones y supuestos

- Java 17/21 LTS y Spring Boot 3.x.
- Un único App Registration en Microsoft Entra ID.
- PostgreSQL y MongoDB son servicios independientes, aunque se ejecuten en el
  mismo `docker compose` durante el desarrollo.
- Los eventos internos entregan consistencia eventual entre módulos; la
  transacción principal de suscripción se confirma en PostgreSQL.
- Los secretos se entregan por variables de entorno o un gestor seguro, nunca
  por archivos versionados.

## Criterios de aceptación

| ID | Criterio verificable |
| --- | --- |
| AC-01 | Un usuario autenticado con `ROLE_CLIENT` puede consultar planes y crear o renovar una suscripción. |
| AC-02 | Una suscripción pagada se guarda en PostgreSQL y publica `SubscriptionPaidEvent`. |
| AC-03 | El consumo de inventario se ejecuta al recibir el evento y no permite stock negativo. |
| AC-04 | El stock bajo genera `LowStockAlertEvent` y queda registrado en la auditoría. |
| AC-05 | La API protege los endpoints bajo `/api/v1/**` y aplica roles de acceso mediante JWT de Entra ID. |
| AC-06 | El entorno local inicia PostgreSQL y MongoDB con `docker compose up -d`. |
| AC-07 | La documentación se compila con MkDocs y sus enlaces de navegación son válidos. |
| AC-08 | La suite de integración usa contenedores reales para PostgreSQL/MongoDB cuando corresponda. |

## Entregables

- Código fuente modular y configuración reproducible.
- `Dockerfile` multi-stage y `docker-compose.yml`.
- Documentación técnica y de gestión en `docs/`.
- ADRs para las decisiones arquitectónicas principales.
- Workflow de publicación de documentación en GitHub Pages.