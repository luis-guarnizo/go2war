# Tasks

## 1. Base del proyecto

- [ ] 1.1 Crear app Next.js (App Router, TypeScript, ESLint, Tailwind) y verificar que `npm run dev` y `npm run build` funcionan
- [ ] 1.2 Agregar Prisma, Vitest, shadcn/ui y docker-compose con Postgres; verificar `docker compose up` y `npx prisma db push` contra la BD local
- [ ] 1.3 Configurar CI (lint, typecheck, test, build) y verificar que pasa en verde

## 2. Dominio y modelo de datos

- [ ] 2.1 Definir el esquema Prisma (User, Plan, Member, Sale, Membership, Attendance) y generar la migración inicial; verificar con `prisma migrate dev`
- [ ] 2.2 Implementar `formatCop` y `normalizeSearch` con pruebas (ej. 1300000 → "$1.300.000", "Pérez" → "perez")
- [ ] 2.3 Implementar `computeEndDate` para días, meses y paquetes con pruebas de los escenarios de las specs (Semanal 1→7 oct, 31 ene → 28 feb, 12 Entrenos 1→30 oct)
- [ ] 2.4 Implementar `computeStartDate` (renovación encadenada) con pruebas de renovación anticipada y después de vencido
- [ ] 2.5 Implementar `memberStatus` (Activo / Por vencer / Vencido / Sin plan, por fecha y por entrenos, ignorando anuladas) con pruebas de cada escenario
- [ ] 2.6 Crear seed idempotente con los 10 planes 2026 y verificar con una prueba que ejecutarlo dos veces deja 10 planes

## 3. Acceso del personal (staff-access)

- [ ] 3.1 Configurar Auth.js con credenciales, bcrypt y sesión de 12 h; middleware que redirige a /ingresar sin sesión; verificar con prueba e2e de acceso sin sesión y login correcto/incorrecto
- [ ] 3.2 Script `npm run create-admin` para el administrador inicial; verificar que crea un único admin y que puede ingresar
- [ ] 3.3 Helper de autorización por rol usado en Server Actions + pantalla de gestión de usuarios (solo admin); verificar con prueba que recepción recibe "No tienes permiso para esta acción"

## 4. Catálogo de planes (plan-catalog)

- [ ] 4.1 Pantalla de lista de planes con tipo, duración/entrenos, personas y precio formateado; verificar que muestra los 10 planes del seed
- [ ] 4.2 Formulario de crear/editar/desactivar plan (solo admin) con validaciones por tipo; verificar con prueba que un cambio de precio no altera ventas previas (snapshot)

## 5. Miembros (member-registry)

- [ ] 5.1 Formulario de registro/edición con obligatorios y documento único; verificar con pruebas de teléfono faltante y documento repetido
- [ ] 5.2 Búsqueda por nombre/teléfono/documento sin tildes con estado en resultados; verificar búsqueda "perez" → "Ana Pérez"
- [ ] 5.3 Ficha del miembro con estado, membresía vigente, historial de ventas y asistencias; inactivación; verificar que un inactivo no sale en listas del panel

## 6. Ventas y pagos (membership-sales)

- [ ] 6.1 Flujo "Vender plan": elegir miembro(s), plan, fecha de pago, inicio sugerido, valor sugerido editable, medio de pago; guardar Sale + Membership(s) en una transacción; verificar con prueba de integración de Mensualidad y de descuento
- [ ] 6.2 Validación de número de personas para Plan pareja (1 pago, 2 membresías); verificar ambos escenarios de la spec
- [ ] 6.3 Historial de pagos con filtros (fechas, plan, medio) y total; verificar total de octubre con datos de prueba
- [ ] 6.4 Anulación de venta (solo admin, motivo obligatorio) que excluye recaudo y vigencia; verificar con prueba de integración

## 7. Asistencia a paquetes (class-pack-attendance)

- [ ] 7.1 Botón "Registrar asistencia" en ficha y búsqueda que descuenta entreno del paquete vigente; verificar saldo 5 → 4
- [ ] 7.2 Confirmación por doble asistencia el mismo día y bloqueo de paquetes agotados/vencidos con mensaje "Paquete agotado: debe renovar"; verificar con pruebas
- [ ] 7.3 Eliminar asistencia del mismo día devolviendo el entreno; verificar saldo +1

## 8. Panel y recaudo (membership-dashboard)

- [ ] 8.1 Panel principal: contador de activos, listas "Por vencer" y "Vencidos (30 días)", recaudo del mes; verificar con datos de prueba que cada miembro cae en la lista correcta
- [ ] 8.2 Botón WhatsApp con mensaje prellenado (`wa.me/57…`); verificar el enlace generado con prueba unitaria
- [ ] 8.3 Reporte de recaudo por rango con desglose por plan y medio de pago, excluyendo anuladas; verificar con prueba de integración
- [ ] 8.4 Exportar miembros y pagos a CSV; verificar que el archivo abre en Excel con tildes correctas

## 9. PWA, despliegue y documentación

- [ ] 9.1 Manifest PWA, íconos y colores GO2WAR; verificar instalación en Android desde Chrome
- [ ] 9.2 Desplegar en Vercel + Neon con `prisma migrate deploy` y seed; documentar en README los pasos de instalación, creación de admin y respaldo; verificar siguiendo el README en un entorno limpio
- [ ] 9.3 Prueba e2e del flujo completo (ingresar → registrar miembro → vender Mensualidad → aparece activo → simular fecha → aparece por vencer) y verificar que pasa en CI
