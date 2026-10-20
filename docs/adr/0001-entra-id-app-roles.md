# ADR-0001: Microsoft Entra ID con App Roles

- **Estado:** Aceptada
- **Fecha:** 19-10-2026

## Contexto

El sistema necesita autenticar clientes y operadores sin mantener credenciales
locales. También debe aplicar autorización distinta a `/portal/**` y
`/admin/**`.

## Decisión

Usar Microsoft Entra ID como proveedor OIDC/OAuth 2.0 con un único App
Registration. Definir los App Roles `ROLE_CLIENT` y `ROLE_ADMIN`; Spring
Security valida los JWT y convierte sus claims en authorities. Un
`AuthenticationSuccessHandler` redirige según el rol.

## Alternativas descartadas

- JWT nativo: duplica responsabilidades de identidad y rotación de claves.
- Keycloak/Auth0: válidos, pero agregan otro servicio y costo operativo.
- Registros separados y scopes complejos: innecesarios para un monolito con
  dos interfaces.

## Consecuencias

Se centralizan usuarios, MFA y políticas empresariales, pero el desarrollo
depende de la disponibilidad y configuración correcta del tenant. Los secretos
deben vivir fuera del repositorio y los claims deben validarse en backend.