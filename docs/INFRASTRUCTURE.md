# Arquitectura e infraestructura

## Vista de despliegue

CoffeeHub se distribuye como un backend Spring Boot y una SPA React/Vite
independiente. El backend expone la API REST y las bases de datos se mantienen
como servicios separados; el frontend se despliega como sitio estático.

```mermaid
flowchart TB
  subgraph Local["Desarrollo local"]
    Compose[Docker Compose]
    PG[(PostgreSQL 15)]
    MG[(MongoDB 6)]
    Compose --> PG
    Compose --> MG
    App[CoffeeHub Spring Boot API] --> PG
    App --> MG
    Web[React + Vite SPA] --> App
  end
  subgraph Cloud["Nube gratuita / créditos estudiantiles"]
    PaaS[Render o Railway<br/>backend Docker]
    Vercel[Vercel<br/>frontend estático]
    Neon[(Neon / Supabase<br/>PostgreSQL)]
    Atlas[(MongoDB Atlas M0)]
    PaaS --> Neon
    PaaS --> Atlas
    Vercel --> PaaS
  end
  Identity[Microsoft Entra ID] --> Web
  Identity --> App
  Identity --> PaaS
```

## Componentes y responsabilidades

| Componente | Responsabilidad | Persistencia |
| --- | --- | --- |
| `users` | Identidad, usuarios y authorities desde claims JWT | PostgreSQL |
| `catalog` | Productos, precios, stock y umbral mínimo | PostgreSQL |
| `subscriptions` | Planes, cobros, estado y fechas de corte | PostgreSQL |
| `audit` | Eventos y trazabilidad sin bloquear el flujo principal | MongoDB |
| React/Vite | Portal público, cliente y administración | Consume la API REST |
| MSAL React | Login, PKCE, adquisición y envío de tokens | Microsoft Entra ID |

## Flujo transaccional

```mermaid
sequenceDiagram
  actor C as Cliente
  participant P as React SPA
  participant S as Subscriptions
  participant B as Spring Event Bus
  participant I as Catalog
  participant A as Audit
  participant DB as PostgreSQL
  participant M as MongoDB

  C->>P: Solicitar o renovar plan
  P->>S: REST con Bearer JWT
  S->>DB: Guardar suscripción pagada
  S->>B: SubscriptionPaidEvent
  B->>I: @EventListener
  I->>DB: Descontar inventario
  alt Stock bajo
    I->>B: LowStockAlertEvent
  end
  B->>A: Registrar evento
  A->>M: Persistir documento BSON
```

El bus de eventos es el mecanismo de desacoplamiento entre módulos. La SPA
consume alertas mediante endpoints REST o el mecanismo de actualización que se
defina en la API. No es un
sustituto de un sistema de mensajería distribuido: si en el futuro se requiere
escalado independiente, el contrato de los eventos permitirá migrar esta
comunicación a un bróker.

## Configuración local

`docker-compose.yml` debe proveer:

```yaml
services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: coffeehub_db
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    ports: ["5432:5432"]
  mongo:
    image: mongo:6.0
    ports: ["27017:27017"]
```

Las credenciales anteriores son exclusivamente de desarrollo. En nube se
reemplazan por secretos del proveedor y conexiones TLS.

## Observabilidad y operación

- Spring Boot Actuator expone health checks para el runtime.
- Los eventos auditables se conservan en MongoDB con su tipo, actor, fecha y
  payload relevante.
- Los logs no deben incluir tokens, secretos ni datos sensibles.
- Los health checks deben probar conectividad con las dependencias antes de
  declarar la aplicación lista.

## Evolución

El límite por módulo permite separar `subscriptions`, `catalog` o `audit` en
servicios independientes si el volumen lo justifica. Esa evolución requeriría
un contrato de eventos durable, observabilidad distribuida y una revisión de
costos; no forma parte del alcance actual.