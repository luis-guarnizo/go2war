# Spec Delta

## Purpose

Controla el consumo de los paquetes de entrenos (8 y 12 entrenos) registrando cada asistencia y el saldo restante del miembro.

## ADDED Requirements

### Requirement: Saldo de entrenos
Una membresía de paquete de entrenos SHALL iniciar con el número de entrenos del plan y vigencia hasta la fecha de inicio más los días de vigencia menos un día.

#### Scenario: Compra de 12 Entrenos
- **WHEN** se vende "12 Entrenos" con inicio el 1 de octubre de 2026
- **THEN** el miembro tiene 12 entrenos restantes válidos hasta el 30 de octubre de 2026

### Requirement: Registrar asistencia
El sistema SHALL permitir registrar la asistencia de un miembro con un toque desde su ficha o la búsqueda. Si el miembro tiene un paquete de entrenos vigente, cada asistencia SHALL descontar un entreno. Un miembro MUST NOT registrar más de una asistencia el mismo día salvo confirmación explícita.

#### Scenario: Asistencia con paquete
- **WHEN** se registra la asistencia de un miembro con 5 entrenos restantes
- **THEN** quedan 4 entrenos restantes y la asistencia aparece en su historial

#### Scenario: Doble asistencia el mismo día
- **WHEN** se intenta registrar una segunda asistencia el mismo día
- **THEN** el sistema pide confirmación antes de registrarla y descontar otro entreno

### Requirement: Paquete agotado o vencido
Un paquete SHALL considerarse vencido al llegar a 0 entrenos o al pasar su fecha de vigencia, lo que ocurra primero. El sistema MUST NOT descontar entrenos de un paquete vencido y SHALL advertir que el miembro debe renovar.

#### Scenario: Sin entrenos
- **WHEN** se registra asistencia a un miembro con 0 entrenos restantes
- **THEN** el sistema muestra "Paquete agotado: debe renovar" y no descuenta entrenos

#### Scenario: Vigencia vencida con entrenos sobrantes
- **WHEN** un paquete de 8 entrenos con 3 restantes pasa su fecha de vigencia
- **THEN** el miembro aparece como vencido

### Requirement: Corrección de asistencia
El sistema SHALL permitir eliminar una asistencia registrada por error el mismo día, devolviendo el entreno descontado.

#### Scenario: Eliminar asistencia
- **WHEN** se elimina una asistencia registrada hoy que descontó un entreno
- **THEN** el saldo de entrenos aumenta en 1
