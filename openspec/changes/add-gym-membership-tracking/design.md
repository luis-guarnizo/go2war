# Design

## Context

Repositorio vacío (proyecto nuevo). Ver proposal.md - Why. Los usuarios son 1–3 personas del gym que operan principalmente desde el celular en recepción; el volumen esperado es de cientos de miembros y unas decenas de pagos por día, así que no hay retos de escala. Lo crítico es que el registro sea rápido, que los datos no se pierdan y que las fechas de vencimiento sean correctas en hora de Colombia (America/Bogota, UTC-5, sin horario de verano).

## Goals / Non-Goals

**Goals:**
- Una sola aplicación web full-stack, fácil de desplegar y mantener por una persona.
- Instalable en el celular como PWA (ícono en pantalla de inicio), sin publicar en tiendas.
- Reglas de negocio (vigencia, estados, recaudo) en un módulo de dominio puro y probado con pruebas unitarias.

**Non-Goals:**
- Funcionamiento sin conexión (offline-first).
- Multi-sede / multi-gimnasio.
- Integraciones de pago en línea o envío automático de mensajes.

## Decisions

### 1. Next.js (App Router) + TypeScript
Una sola base de código para UI y servidor (Server Components + Server Actions), sin API separada. Alternativas: SPA React + API Express (dos proyectos que mantener); Laravel/Django (también válido, pero TypeScript permite compartir tipos y reglas de dominio entre servidor y UI).

### 2. PostgreSQL con Prisma ORM
Base de datos relacional por la naturaleza de los datos (miembros ↔ membresías ↔ pagos) y para transacciones al vender planes pareja. Prisma da migraciones versionadas y tipos. Producción: Postgres administrado (Neon o Supabase, plan gratuito). Desarrollo: Postgres en Docker. Alternativas: SQLite (no sirve en hosting serverless sin capa extra), Firebase (modelo documental, más difícil para reportes de recaudo).

### 3. Modelo de datos
- `User` (nombre, usuario, hash de contraseña, rol `ADMIN|RECEPTION`, activo).
- `Plan` (nombre, tipo `TIME|CLASS_PACK`, `durationUnit` `DAY|MONTH`, `durationValue`, `classCount?`, `validityDays?`, `peopleCount`, `priceCop` entero, activo, orden).
- `Member` (nombre, teléfono, documento único opcional, fecha nacimiento, correo, contacto emergencia, notas, activo, `searchText` normalizado sin tildes).
- `Sale` (fecha de pago, plan, `planSnapshot` JSON con nombre/precio/duración al momento de la venta, valor cobrado, medio de pago, nota, usuario, `voidedAt?`, `voidReason?`).
- `Membership` (venta, miembro, fecha inicio, fecha fin, `classesTotal?`, `classesUsed`). Un plan pareja = 1 `Sale` + 2 `Membership`.
- `CheckIn` (miembro, membresía?, fecha/hora, `day` DATE en hora de Colombia, origen `QR|STAFF`, `withoutValidPlan` bool, usuario? si fue manual). Índice único (`memberId`, `day`) para garantizar un ingreso por día incluso con escaneos simultáneos.
- `MemberDevice` (miembro, hash del token, creado, último uso) para recordar el celular del miembro.

Montos como enteros en COP (sin decimales). Fechas de membresía como `DATE` (sin hora) para evitar errores de zona horaria; "hoy" se calcula siempre en America/Bogota.

### 4. Estado calculado, no almacenado
El estado (Activo / Por vencer / Vencido / Sin plan) se calcula en consulta a partir de las membresías no anuladas, en vez de guardarse con un job diario. Evita desincronizaciones y tareas programadas. Con el volumen esperado, la consulta es trivial. Umbrales (5 días / 2 entrenos) como constantes configurables.

### 5. Módulo de dominio puro (`src/domain`)
Funciones sin dependencias de BD: `computeEndDate`, `computeStartDate` (renovación encadenada), `memberStatus`, `formatCop`, `normalizeSearch`. Probadas con Vitest; cubren los escenarios de las specs (Semanal, 31 de enero → 28 de febrero, renovación anticipada, etc.).

### 6. Autenticación propia simple
Auth.js (NextAuth) con proveedor de credenciales, contraseñas con bcrypt, sesión JWT de 12 horas. Autorización por rol en cada Server Action (no solo en la UI). El admin inicial se crea con un script `npm run create-admin` que pide usuario y contraseña. Alternativa: Clerk/Supabase Auth (dependencia externa innecesaria para 1–3 usuarios).

### 7. UI
Tailwind CSS + componentes shadcn/ui, mobile-first, colores de la marca (negro y azul GO2WAR). Navegación inferior: Panel · Miembros · Vender · Pagos. Formato de moneda con `Intl.NumberFormat('es-CO')` sin decimales. Manifest PWA para instalar en el celular.

### 8. Ingreso por QR
- El QR fijo apunta a `https://<dominio>/ingreso`; el admin lo descarga como PDF imprimible (librería `qrcode`) desde Configuración.
- `/ingreso` es una ruta pública fuera del middleware de sesión del personal. Primer uso: formulario de cédula → si existe un miembro activo, se crea el `CheckIn` y un `MemberDevice` con token aleatorio de 32 bytes guardado en cookie `httpOnly`, `secure`, de 1 año; en BD solo se guarda su hash. Escaneos siguientes: la cookie identifica al miembro y se muestra un botón "Registrar mi ingreso" (evita registros accidentales por precarga del enlace).
- Registro de ingreso y descuento de entreno en una transacción; el índice único (`memberId`, `day`) hace idempotente el doble escaneo.
- Respuesta mínima: nombre de pila + estado. Nunca se revela si una cédula existe más allá de "No te encontramos". Límite de intentos por IP (p. ej. 10/min) para evitar enumeración de cédulas.
- Alternativa descartada por ahora: QR rotativo mostrado en una tablet en la entrada (evita ingresos desde fuera del gym, pero exige un dispositivo encendido). El diseño permite agregarlo luego validando un token de corta duración en `/ingreso`.
- "Ingresos de hoy" en el panel se refresca con polling cada 30 s (sin websockets).

### 9. WhatsApp
Enlace `https://wa.me/57<teléfono>?text=<mensaje>`; no requiere API ni costo.

### 10. Despliegue
Vercel (gratis) + Neon Postgres. `prisma migrate deploy` en el build. Seed idempotente con los 10 planes 2026.

## Risks / Trade-offs

- [Pérdida de datos] → Postgres administrado con backups automáticos; exportación CSV de miembros y pagos como respaldo manual.
- [Errores de zona horaria en vencimientos] → fechas `DATE` sin hora y "hoy" calculado en America/Bogota; pruebas unitarias de bordes de mes.
- [Supuestos de plan pareja / vigencia de paquetes incorrectos] → son datos del plan (`peopleCount`, `validityDays`) editables por el admin, no lógica fija.
- [Ingreso desde fuera del gym con foto del QR] → aceptado por ahora: un ingreso por día limita el abuso a descontar entrenos propios y los ingresos sin plan quedan señalados; evolución prevista a QR rotativo.
- [Celular compartido o prestado] → botón "No soy yo" y recepción puede eliminar ingresos del día.
- [Enumeración de cédulas en la página pública] → mensaje genérico y límite de intentos por IP.
- [Dependencia de conexión a internet en recepción] → aceptado (non-goal offline); la app funciona con datos móviles.
- [Plan gratuito de hosting con límites] → el volumen del gym está muy por debajo de los límites; se puede migrar a plan pago sin cambios de código.

## Migration Plan

Proyecto nuevo: no hay migración de datos en esta versión. Si el gym tiene un registro previo (Excel/cuaderno), la carga se hace registrando miembros y su venta vigente con fecha de pago/inicio original. Rollback: desactivar el despliegue; los datos permanecen en la BD.

## Open Questions

- Confirmar con el gym los supuestos de la propuesta (Plan pareja = 1 mes para 2 personas; paquetes de entrenos con vigencia de 30 días). Cambiarlos solo ajusta los datos semilla, no el diseño.
- Texto exacto del mensaje de recordatorio por WhatsApp.
