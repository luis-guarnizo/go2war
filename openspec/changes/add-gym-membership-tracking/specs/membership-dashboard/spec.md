# Spec Delta

## Purpose

Da al personal una vista rápida de quién está al día, a quién se le vence el plan pronto y cuánto se ha recaudado.

## ADDED Requirements

### Requirement: Estado del miembro
El sistema SHALL calcular el estado de cada miembro activo según su membresía no anulada más reciente: "Activo" si está vigente y vence en más de 5 días (o le quedan más de 2 entrenos), "Por vencer" si vence en 5 días o menos (o le quedan 2 entrenos o menos), "Vencido" si ya no tiene vigencia y "Sin plan" si nunca ha comprado uno.

#### Scenario: Por vencer por fecha
- **WHEN** hoy es 10 de noviembre y la membresía de Ana vence el 14 de noviembre
- **THEN** Ana aparece con estado "Por vencer"

#### Scenario: Vencido
- **WHEN** hoy es 15 de noviembre y la membresía de Ana venció el 14 de noviembre sin renovación
- **THEN** Ana aparece con estado "Vencido"

#### Scenario: Por vencer por entrenos
- **WHEN** a un miembro le quedan 2 entrenos de su paquete
- **THEN** aparece con estado "Por vencer"

### Requirement: Panel de control
La pantalla principal SHALL mostrar: número de miembros activos, la lista "Por vencer" ordenada por fecha de vencimiento, la lista "Vencidos" de los últimos 30 días y el total recaudado del mes en curso.

#### Scenario: Abrir el panel
- **WHEN** el personal inicia sesión
- **THEN** ve los contadores y listas con nombre, plan, fecha de vencimiento y teléfono de cada miembro

### Requirement: Contacto rápido por WhatsApp
Cada miembro en las listas "Por vencer" y "Vencidos" SHALL tener una acción que abra WhatsApp con un mensaje de recordatorio prellenado con su nombre, plan y fecha de vencimiento. El envío lo realiza manualmente el personal.

#### Scenario: Recordatorio
- **WHEN** se toca "WhatsApp" junto a un miembro por vencer
- **THEN** se abre WhatsApp con el número del miembro y un mensaje de recordatorio editable

### Requirement: Resumen de recaudo
El sistema SHALL mostrar el recaudo de un rango de fechas con total general, total por plan y total por medio de pago, excluyendo ventas anuladas.

#### Scenario: Recaudo del mes
- **WHEN** el administrador consulta el recaudo de octubre de 2026
- **THEN** ve el total, el desglose por plan y por medio de pago, sin incluir ventas anuladas
