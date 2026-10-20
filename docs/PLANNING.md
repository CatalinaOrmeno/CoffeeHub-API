# Planificación Lean y WBS

## Enfoque

El trabajo se organiza en seis semanas con una dedicación estimada de 10 a 12
horas semanales. Cada fase produce un incremento verificable y su
documentación vive en el repositorio. El orden prioriza primero las decisiones
que desbloquean el resto del diseño, pero permite validar temprano la
infraestructura.

## WBS

```mermaid
mindmap
  root((CoffeeHub))
    1. Alcance e identidad
      1.1 Dominio y criterios
      1.2 Entra ID y App Roles
      1.3 ADR-0001
    2. Plataforma
      2.1 Spring Boot
      2.2 PostgreSQL y MongoDB
      2.3 Docker Compose
    3. Backend
      3.1 Catálogo e inventario
      3.2 Suscripciones
      3.3 Eventos internos
      3.4 Auditoría
    4. Frontend
      4.1 React y Vite
      4.2 Portal, cliente y administración
      4.3 MSAL y rutas protegidas
    5. Calidad
      5.1 OpenAPI
      5.2 Testcontainers
      5.3 Actuator
    6. Entrega
      6.1 Dockerfile
      6.2 CI/CD
      6.3 README y publicación
```

## Cronograma

```mermaid
gantt
  title CoffeeHub — hoja de ruta de seis semanas
  dateFormat  YYYY-MM-DD
  axisFormat  %d/%m
  section Alcance
  Investigación, dominio y SCOPE       :w1, 2026-01-05, 7d
  Entra ID y ADR-0001                   :w1b, after w1, 3d
  section Plataforma
  Spring Boot y modelos SQL              :w2, 2026-01-12, 7d
  Docker Compose local                  :w2b, after w2, 3d
  section Backend
  Catálogo y suscripciones              :w3, 2026-01-19, 7d
  Eventos y auditoría                  :w3b, after w3, 4d
  section Frontend
  React, Vite y vistas                 :w4, 2026-01-26, 5d
  MSAL, roles y rutas protegidas       :w4b, after w4, 5d
  section Calidad
  Swagger y pruebas de integración     :w5, 2026-02-02, 7d
  section Entrega
  Docker, CI/CD y presentación          :w6, 2026-02-09, 7d
```

Las fechas son una referencia relativa para el plan de seis semanas; el
seguimiento real se realiza mediante issues y pull requests.

## Entregables por fase

| Semana | Fase | Actividades | Entregable |
| --- | --- | --- | --- |
| 1 | Investigación y alcance | Dominio, Entra ID, alcance y ADR | `SCOPE.md`, ADR-0001 |
| 2 | Infraestructura y datos | Spring Boot, Compose y entidades PostgreSQL | Proyecto compilable y bases locales |
| 3 | Backend y REST API | Catálogo, suscripciones, listeners, MongoDB y Swagger | Endpoints REST funcionales |
| 4 | Frontend y auth | React, Vite, MSAL, vistas y rutas protegidas | SPA conectada a la API |
| 5 | Pruebas y calidad | Testcontainers y flujos OIDC del frontend | Suite y tokens validados |
| 6 | DevOps y despliegue | Dockerfile, Render/Vercel, README y diagramas | Repositorio listo para portafolio |

## Definición de terminado

Una tarea se considera terminada cuando el código compila, las pruebas
relevantes pasan, la documentación relacionada está actualizada y existe una
revisión mediante pull request. Los cambios de arquitectura requieren un ADR
nuevo o una actualización explícita del ADR correspondiente.