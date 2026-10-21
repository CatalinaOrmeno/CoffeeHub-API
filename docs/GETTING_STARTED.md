# Guía de configuración e inicio

## 1. Prerrequisitos y beneficios

Instala Java 17 o 21 LTS, Docker Desktop y Git. El GitHub Student Developer Pack puede proporcionar IntelliJ IDEA Ultimate, créditos de MongoDB Atlas y opciones de hosting; estos beneficios son opcionales para ejecutar el proyecto localmente.

## 2. Crear el proyecto base

En [Spring Initializr](https://start.spring.io/) selecciona Java 17/21, Spring Boot 3.x y Gradle o Maven. Las dependencias previstas son:

- Spring Web y Spring Security.
- OAuth2 Resource Server.
- Spring Data JPA y Spring Data MongoDB.
- PostgreSQL Driver.
- Spring Boot Actuator.
- Springdoc-OpenAPI.

## 3. Registrar Microsoft Entra ID

1. Crea un App Registration llamado `coffeehub-dev`.
2. Define los App Roles `ROLE_ADMIN` y `ROLE_CLIENT` para usuarios o grupos.
3. Configura el redirect URI de desarrollo para el flujo OIDC.
4. Obtén Tenant ID y Client ID. El backend valida JWT emitidos por Entra ID y no debe depender de secretos en código fuente.
5. Proporciona los valores de autenticación mediante variables de entorno del servicio.

Spring Security valida los JWT usando el issuer del tenant y convierte los App Roles en authorities. Nunca copies un client secret en `application.yml` versionado.

## 4. Levantar las dependencias

Desde la raíz:

```bash
docker compose up -d
docker compose ps
```

Conexiones de desarrollo:

| Servicio | Dirección |
| --- | --- |
| PostgreSQL | `jdbc:postgresql://localhost:5432/coffeehub_db` |
| MongoDB | `mongodb://localhost:27017/coffeehub` |

## 5. Ejecutar y validar

Ejecuta el backend con el wrapper del proyecto cuando esté disponible:

```bash
./mvnw spring-boot:run
# o
./gradlew bootRun
```

Después verifica el health endpoint y la documentación OpenAPI en las rutas configuradas por la aplicación. La ruta habitual de Swagger UI es `/swagger-ui.html`.

## 6. Publicar la documentación

```bash
pip install mkdocs-material
mkdocs build -f docs/mkdocs.yml --strict
mkdocs serve -f docs/mkdocs.yml
```

El workflow de GitHub Actions publica GitHub Pages al hacer push a `main`.
