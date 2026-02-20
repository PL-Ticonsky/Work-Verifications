# Tech Stack

## Plataforma
- **Aplicación web** privada para admins.
- Despliegue recomendado: **Vercel** (app) + **Neon** (PostgreSQL).

---

## Frontend
- **Next.js** (App Router)
- UI simple (una sola pantalla de validación).
- Acceso a cámara para escaneo QR en dispositivos móviles.

### Escaneo QR
- Librería: `@zxing/browser`
- Flujo:
  1. Leer QR → obtener texto
  2. `JSON.parse(texto)`
  3. Extraer `nid`
  4. Consultar DB por cédula = `nid`
  5. Mostrar resultado

---

## Backend
- API interna de Next.js (Route Handlers).
- Endpoints protegidos por sesión (solo admins).
- Lógica de validación:
  - Por QR: buscar por `cedula == nid`
  - Por cédula: buscar por `cedula == input`
  - Por código: buscar por `codigo == input`

---

## Base de datos
- **PostgreSQL** (Neon)
- ORM: **Prisma**
- Migraciones con Prisma Migrate.
- Seed con:
  - 6 admins (username + passwordHash)
  - Import/Upsert desde `data/permitidos.csv`

---

## Autenticación
- Sesiones para proteger rutas.
- Login con **username + contraseña**.
- Usuarios admin creados por seed (no se crean desde UI).

> Implementación posible:
- Auth.js (NextAuth) con Credentials **o**
- login propio con cookies/sesión (válido si se mantiene simple y seguro)

---

## Dev / Tooling
- Node.js 18+
- TypeScript (recomendado)
- ESLint / Prettier (opcional pero ideal)
- CSV parser (ej: `csv-parse` o `papaparse` en server)

---

## Variables de entorno (mínimas)
- `DATABASE_URL` (Neon)
- `AUTH_SECRET` (secreto para sesiones)