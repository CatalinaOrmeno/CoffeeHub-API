# ADR-0003: Frontend SPA con React y Vite

- **Estado:** Aceptada
- **Fecha:** 19-10-2026

## Contexto

Clientes, visitantes y operadores necesitan experiencias distintas, pero el
proyecto debe mantener un único frontend moderno y modular, sin duplicar
aplicaciones.

## Decisión

Usar una SPA React construida con Vite y Tailwind CSS. React Router define `/`,
`/portal/*` y `/admin/*`; `ProtectedRoute` autoriza según `ROLE_CLIENT` y
`ROLE_ADMIN`. La autenticación usa `@azure/msal-react` con PKCE y la SPA
consume la API REST del backend bajo `/api/v1/**`.

## Alternativas descartadas

- Thymeleaf/HTMX y Vaadin: dividen la experiencia en dos tecnologías y
  dificultan la reutilización de componentes.
- Next.js: Vite ofrece un empaquetado SPA más simple para el alcance actual.
- Vue.js: React presenta mayor demanda y ecosistema para el objetivo del caso
  de estudio.

## Consecuencias

Se obtiene una interfaz consistente, reutilizable y desplegable como sitio
estático en Vercel. Se agrega la responsabilidad de mantener dos proyectos y
de coordinar CORS, variables `VITE_*`, tokens y la URL pública de la API.