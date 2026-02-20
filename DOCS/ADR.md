# Arquitectura

## Visión general
Arquitectura monolítica simple (una app) con:
- **Frontend** (UI)
- **Backend API** (validación + auth)
- **DB PostgreSQL** (admins + permitidos)

Todo en un solo repo y un solo despliegue.

---

## Componentes

### 1) Cliente (Browser)
- Pantalla de login
- Pantalla única de validación
- Funciones:
  - Captura de cámara (QR)
  - Inputs manuales
  - Mostrar resultado

---

### 2) Servidor (Next.js)
- Middleware de autenticación:
  - Bloquea todo excepto `/login`
- API de validación (privada):
  - Valida por QR / cédula / código
- Retorna resultado:
  - `authorized: true/false`
  - si `true`: `nombre`, `grupo`

---

### 3) Persistencia (PostgreSQL)
Tablas principales:

#### `admins`
- `id`
- `username` (UNIQUE)
- `passwordHash`
- `createdAt`

#### `permitidos`
- `id`
- `nombre`
- `cedula` (UNIQUE, nullable)
- `codigo` (UNIQUE, nullable)
- `grupo`
- `createdAt`
- `updatedAt`

---

## Flujos

### A) Login
1. Admin abre `/login`
2. Ingresa username + contraseña
3. Backend valida hash
4. Se crea sesión
5. Redirección a `/` (pantalla de validación)

---

### B) Validación por QR
1. Admin abre `/`
2. Click “Escanear QR”
3. Cámara lee QR → obtiene texto: `{"nid":"1074088011"}`
4. Cliente parsea JSON → `nid`
5. Cliente llama API: `/api/validate/qr` con `nid`
6. Backend consulta DB: `permitidos` donde `cedula == nid`
7. Respuesta:
   - ✅ autorizado + datos (nombre, grupo)
   - ❌ no autorizado

---

### C) Validación por cédula
1. Admin digita cédula
2. Llama `/api/validate/cedula`
3. Backend busca `permitidos.cedula`
4. Devuelve resultado

---

### D) Validación por código
1. Admin digita código
2. Llama `/api/validate/codigo`
3. Backend busca `permitidos.codigo`
4. Devuelve resultado

---

## Actualización de permitidos (sin UI)
### Fuente
- `data/permitidos.csv` en el repo.

### Proceso
1. Editar CSV
2. Commit + push → deploy
3. Ejecutar seed/import:
   - Lee CSV
   - Normaliza campos
   - Upsert por `cedula` y/o `codigo`
   - Actualiza nombre/grupo si cambian

---

## Consideraciones de seguridad (mínimas)
- Rutas protegidas por sesión.
- API privada solo para admins autenticados.
- Contraseñas hasheadas (bcrypt/argon2).
- Variables secretas en el entorno (no en el repo).
- Rate limiting básico opcional para endpoints de login.

---

## Consideraciones de rendimiento
- Índices UNIQUE en `cedula` y `codigo`.
- Consultas simples por llave → respuesta rápida (< 1s en condiciones normales).
- Concurrencia baja esperada (≈ 5 validadores).