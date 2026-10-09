# Proposal

## Why

GO2WAR GYM lleva hoy el control de mensualidades de forma manual, lo que hace difícil saber quién está al día, a quién se le vence el plan esta semana y cuánto se recaudó en el mes. Con la nueva lista de "Planes Año 2026" (planes por tiempo, paquetes de entrenos y plan pareja) se necesita una app sencilla, usable desde el celular, que registre cada pago y calcule automáticamente la vigencia de cada miembro.

## What Changes

- Nueva aplicación web (mobile-first, en español) para el personal del gym, protegida con usuario y contraseña.
- Catálogo de planes precargado con la lista 2026, editable por el administrador:

  | Plan | Tipo | Duración / cupo | Precio (COP) |
  |---|---|---|---|
  | Mensualidad | Por tiempo | 1 mes | $135.000 |
  | Bimestre | Por tiempo | 2 meses | $255.000 |
  | Trimestre | Por tiempo | 3 meses | $365.000 |
  | Semestre | Por tiempo | 6 meses | $690.000 |
  | Año | Por tiempo | 12 meses | $1.300.000 |
  | Semanal | Por tiempo | 7 días | $50.000 |
  | Quincenal | Por tiempo | 15 días | $85.000 |
  | 8 Entrenos | Paquete de entrenos | 8 entrenos, vigencia 30 días | $90.000 |
  | 12 Entrenos | Paquete de entrenos | 12 entrenos, vigencia 30 días | $115.000 |
  | Plan pareja | Por tiempo, 2 personas | 1 mes | $250.000 |

- Registro de miembros (datos de contacto, estado).
- Registro de pagos/ventas de planes: al vender un plan se crea una membresía con fecha de inicio y vencimiento calculadas, y queda el pago registrado con su medio de pago.
- Ingreso de miembros por código QR: un QR impreso en la entrada abre una página donde el miembro se identifica con su cédula (el celular lo recuerda para la próxima vez); el ingreso queda registrado, ve si su plan está vigente y, si tiene paquete de entrenos, se descuenta uno. Quien no está registrado es enviado a recepción.
- Panel de control: miembros activos, ingresos de hoy, por vencer (próximos 5 días), vencidos y recaudo del período.

**Supuestos registrados** (confirmar con el gym):
- El precio "$90.0000" de la imagen se interpreta como $90.000.
- Plan pareja: cubre a 2 miembros durante 1 mes, $250.000 en total.
- Paquetes de entrenos: vigencia de 30 días desde la compra; vence al agotar los entrenos o al cumplirse la vigencia, lo que ocurra primero.

## Capabilities

### New Capabilities
- `staff-access`: Ingreso del personal del gym a la app y roles (administrador / recepción).
- `plan-catalog`: Catálogo de planes con tipo, duración o número de entrenos, cantidad de personas y precio vigente.
- `member-registry`: Alta, edición, búsqueda y consulta de miembros con su historial.
- `membership-sales`: Venta/renovación de planes, cálculo de vigencia, registro del pago y anulaciones.
- `member-check-in`: Ingreso de miembros escaneando el QR de la entrada, registro manual por recepción y saldo de entrenos de los paquetes.
- `membership-dashboard`: Estado de cada miembro (activo, por vencer, vencido), ingresos de hoy y resumen de recaudo.

### Modified Capabilities
- Ninguna (proyecto nuevo).

## Impact

- Repositorio nuevo: se crea la aplicación web completa (frontend, backend y base de datos).
- Nuevas dependencias: framework web, ORM y base de datos relacional (ver design.md).
- Datos personales de miembros (nombre, documento, teléfono): requiere acceso autenticado; la página pública de ingreso solo muestra al miembro su propio nombre y estado.
- Fuera de alcance en esta versión: pagos en línea, app nativa, recordatorios automáticos por WhatsApp/SMS, control de acceso físico (torniquete/huella; el QR registra el ingreso pero no bloquea la puerta), QR rotativo anti-fraude, múltiples sedes, pagos parciales/abonos.
