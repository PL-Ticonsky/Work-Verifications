# System Overview

## Objetivo
Construir una **aplicación web privada** (solo para admins) que permita **validar acceso** a un lugar usando tres métodos:
1. **QR** (lee un JSON directo que contiene `nid`)
2. **Cédula** (digitada manualmente)
3. **Código** (digitado manualmente)

La validación es **binaria**: **sí / no** (existe o no existe en la lista de permitidos).

---

## Alcance (MVP)
### Lo que SÍ hace
- Login con **usuario (username) + contraseña** para 6 admins predefinidos (seed).
- Una sola vista después del login: **pantalla de validación**.
- Validación por:
  - **QR**: escanea y extrae `nid` desde un JSON como `{"nid":"1074088011"}`
  - **Cédula**: input manual
  - **Código**: input manual
- Si está permitido, muestra:
  - ✅ **AUTORIZADO**
  - **Nombre** y **Grupo**
- Si no está permitido, muestra:
  - ❌ **NO AUTORIZADO**

---

## Fuera de alcance (no se implementa en este proyecto)
- No se controla cuántas veces entró una persona.
- No se registran intentos ni auditoría de quién validó.
- No hay tipos de acceso (VIP, staff, etc.).
- No hay panel admin para cargar datos desde UI.

---

## Fuente de datos de permitidos
La lista de permitidos se mantiene **en el código**, en un archivo:
- `data/permitidos.csv`

Para actualizar la lista:
1. Editar `data/permitidos.csv`
2. Commit + push
3. Deploy
4. Ejecutar seed/import (actualiza DB con upsert)

---

## Requisitos de ejecución
- Requiere internet (web app).
- Debe responder rápido (meta: validación ~ < 1s).
- Soporta pocos validadores concurrentes (≈ hasta 5).

---

## Formatos de entrada
### QR
- El QR contiene un string JSON:
  - `{"nid":"1074088011"}`

### Manual
- Cédula: número sin ceros a la izquierda
- Código: número sin ceros a la izquierda

> Internamente se guardan como **string** para evitar problemas de longitud/formato.