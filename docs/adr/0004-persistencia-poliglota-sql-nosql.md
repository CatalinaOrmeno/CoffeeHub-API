# ADR-0004: Persistencia políglota SQL y NoSQL

- **Estado:** Aceptada
- **Fecha:** 19-10-2026

## Contexto

Suscripciones e inventario requieren integridad referencial y transacciones.
Los eventos de auditoría tienen estructura variable y se consultan como
documentos históricos.

## Decisión

Usar PostgreSQL para usuarios, catálogo, inventario y suscripciones; usar
MongoDB para auditoría y payloads de eventos en BSON. Spring Data JPA y Spring
Data MongoDB abstraen cada modelo sin mezclar sus responsabilidades.

## Alternativas descartadas

- Solo PostgreSQL: posible, pero menos flexible para eventos variables.
- Solo MongoDB: debilita las garantías relacionales del inventario y cobros.
- DocumentDB: alternativa de nube válida, pero MongoDB Atlas M0 es suficiente
  para el caso de estudio y su objetivo de costo cero.

## Consecuencias

Cada base se usa donde aporta más valor, a costa de operar dos conexiones,
probar dos tecnologías y no disponer de una transacción ACID distribuida. La
auditoría es secundaria al commit de negocio y debe tolerar consistencia
eventual.