# ADR-0005: Manejo de entornos de ejecución

- **Estado:** Aceptada
- **Fecha:** 19-10-2026

## Contexto

El proyecto debe ejecutarse en entornos de desarrollo, pruebas y producción. Cada
entorno tiene variables de configuración distintas, pero el código fuente debe
ser el mismo. La configuración de Spring Boot debe ser flexible y segura, y la
aplicación debe poder ejecutarse localmente sin depender de servicios externos.

## Decisión

Usar Archivos YAML separados para cada perfil de Spring Boot (`application-dev.yml`,
`application-test.yml`, `application-prod.yml`) y un archivo común (`application.yml`). Cada perfil define variables de conexión a bases de datos, URLs de servicios y
credenciales.

## Alternativas descartadas

- Un único archivo YAML unificado: complica la gestión de variables y aumenta el riesgo de errores al cambiar de entorno.
- Modificar variables de entorno del sistema operativo: menos transparente y más propenso a errores humanos.
- Usando Perfiles de Maven: agrega complejidad al proceso de construcción y despliegue, y no es necesario para el alcance actual.

## Consecuencias

### Positivas (Beneficios)
- **Separación clara de entornos:** Facilita la gestión y el mantenimiento de las configuraciones específicas de desarrollo, pruebas y producción sin mezclar código ni credenciales.
- **Portabilidad y desacoplamiento:** El uso de perfiles nativos de Spring Boot permite que el mismo artefacto (`.jar` o `.war`) sea agnóstico al entorno hasta el momento de su ejecución.
- **Autonomía local:** Facilita la ejecución local (`application-dev.yml`) sin depender obligatoriamente de conectividad o disponibilidad de servicios externos de staging o producción.
- **Trazabilidad:** Los cambios en la estructura de configuración se versionan en el repositorio de código junto con la aplicación.

### Negativas (Riesgos o Costes)
- **Riesgo de seguridad (Credenciales):** Almacenar credenciales sensibles de producción en archivos `application-prod.yml` dentro del repositorio es una mala práctica. Las contraseñas y tokens críticos **deben ser inyectadas mediante variables de entorno** en el servidor/contenedor y mapeadas en el YAML mediante la sintaxis `${VARIABLE_NAME}`.
- **Complejidad en el despliegue:** Requiere asegurar que el comando de arranque de la aplicación incluya explícitamente el parámetro activo correcto (ej. `--spring.profiles.active=prod`).
