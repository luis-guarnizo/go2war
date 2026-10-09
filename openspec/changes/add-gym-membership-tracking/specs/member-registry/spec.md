# Spec Delta

## Purpose

Mantiene el registro de las personas que entrenan en el gym para poder asociarles planes, pagos y asistencias.

## ADDED Requirements

### Requirement: Registro de miembro
El sistema SHALL permitir registrar un miembro con nombre completo y teléfono (obligatorios), y opcionalmente número de documento, fecha de nacimiento, correo, contacto de emergencia y notas.

#### Scenario: Registro exitoso
- **WHEN** recepción registra a "Ana Pérez" con teléfono "3001234567"
- **THEN** el miembro queda creado y se muestra su ficha

#### Scenario: Falta un dato obligatorio
- **WHEN** se intenta registrar un miembro sin teléfono
- **THEN** el sistema no lo guarda e indica que el teléfono es obligatorio

### Requirement: Documento único
El sistema MUST NOT permitir dos miembros con el mismo número de documento.

#### Scenario: Documento repetido
- **WHEN** se registra un miembro con un documento que ya existe
- **THEN** el sistema rechaza el registro y ofrece abrir la ficha del miembro existente

### Requirement: Búsqueda de miembros
El sistema SHALL permitir buscar miembros por nombre, teléfono o documento, con coincidencias parciales y sin distinguir mayúsculas ni tildes.

#### Scenario: Búsqueda parcial sin tildes
- **WHEN** se busca "perez"
- **THEN** aparece "Ana Pérez" en los resultados junto con su estado de membresía

### Requirement: Ficha del miembro
La ficha del miembro SHALL mostrar sus datos, su estado actual (activo, por vencer, vencido o sin plan), la membresía vigente con fecha de vencimiento o entrenos restantes, y el historial de ventas y asistencias.

#### Scenario: Consultar ficha
- **WHEN** se abre la ficha de un miembro con Mensualidad vigente hasta el 14 de noviembre de 2026
- **THEN** se muestra estado "Activo", vence "14 nov 2026" y el historial de pagos

### Requirement: Edición e inactivación
El sistema SHALL permitir editar los datos de un miembro e inactivarlo; un miembro inactivo MUST NOT aparecer en las listas del panel pero su historial SHALL conservarse.

#### Scenario: Inactivar miembro
- **WHEN** se inactiva a un miembro
- **THEN** deja de aparecer en "Por vencer" y "Vencidos", y su historial sigue consultable desde la búsqueda
