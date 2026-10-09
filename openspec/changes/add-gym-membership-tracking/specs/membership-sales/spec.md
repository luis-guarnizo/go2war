# Spec Delta

## Purpose

Registra cada venta o renovación de un plan con su pago y calcula automáticamente el período de vigencia de la membresía resultante.

## ADDED Requirements

### Requirement: Registrar venta de plan
El sistema SHALL permitir registrar la venta de un plan activo a un miembro, indicando fecha de pago, medio de pago (efectivo, transferencia/Nequi/Daviplata, tarjeta u otro), valor cobrado y nota opcional. El valor cobrado SHALL sugerirse con el precio vigente del plan y podrá modificarse (descuentos).

#### Scenario: Venta de Mensualidad en efectivo
- **WHEN** recepción vende "Mensualidad" a Ana el 15 de octubre de 2026 en efectivo
- **THEN** queda registrado un pago de $135.000 en efectivo y una membresía del 15 de octubre al 14 de noviembre de 2026

#### Scenario: Valor con descuento
- **WHEN** se registra la venta de "Trimestre" con valor cobrado $330.000
- **THEN** el pago se guarda por $330.000, sin cambiar el precio del plan

### Requirement: Cálculo de vigencia para planes por tiempo
Para planes por tiempo, la fecha de vencimiento SHALL ser la fecha de inicio más la duración, menos un día. Los meses SHALL contarse como meses calendario; si el día de inicio no existe en el mes destino, la membresía vence el último día de ese mes.

#### Scenario: Plan Semanal
- **WHEN** un plan "Semanal" inicia el 1 de octubre de 2026
- **THEN** vence el 7 de octubre de 2026

#### Scenario: Mes con fin de mes
- **WHEN** una "Mensualidad" inicia el 31 de enero de 2027
- **THEN** vence el 28 de febrero de 2027

### Requirement: Inicio de la membresía en renovaciones
La fecha de inicio SHALL ser por defecto la fecha de pago; si el miembro tiene una membresía por tiempo vigente, el inicio SHALL ser el día siguiente a su vencimiento, de modo que no se pierdan días pagados. El usuario SHALL poder cambiar la fecha de inicio.

#### Scenario: Renovación anticipada
- **WHEN** un miembro con Mensualidad que vence el 14 de noviembre renueva otra Mensualidad el 10 de noviembre
- **THEN** la nueva membresía inicia el 15 de noviembre y vence el 14 de diciembre

#### Scenario: Renovación después de vencido
- **WHEN** un miembro cuya membresía venció el 1 de octubre paga Mensualidad el 20 de octubre
- **THEN** la nueva membresía inicia el 20 de octubre

### Requirement: Plan para varias personas
Al vender un plan que cubre más de una persona (Plan pareja), el sistema SHALL exigir seleccionar exactamente ese número de miembros, registrar un solo pago y crear una membresía con el mismo período para cada miembro.

#### Scenario: Plan pareja
- **WHEN** se vende "Plan pareja" a Ana y Luis el 1 de noviembre de 2026
- **THEN** se registra un pago de $250.000 y ambos quedan activos del 1 al 30 de noviembre de 2026

#### Scenario: Plan pareja con un solo miembro
- **WHEN** se intenta vender "Plan pareja" seleccionando un solo miembro
- **THEN** el sistema no registra la venta e indica que el plan requiere 2 personas

### Requirement: Historial de pagos
El sistema SHALL listar los pagos con fecha, miembro(s), plan, valor, medio de pago y usuario que lo registró, filtrables por rango de fechas, plan y medio de pago.

#### Scenario: Pagos del mes
- **WHEN** se filtran los pagos de octubre de 2026
- **THEN** se muestran solo los pagos con fecha de pago en octubre y el total recaudado

### Requirement: Anulación de ventas
Solo el administrador SHALL poder anular una venta indicando un motivo. Una venta anulada MUST NOT contar en el recaudo ni otorgar vigencia, pero SHALL seguir visible en el historial marcada como anulada.

#### Scenario: Anular venta por error
- **WHEN** el administrador anula una venta con motivo "Registrada al miembro equivocado"
- **THEN** la membresía asociada deja de contar para el estado del miembro y el pago sale del total recaudado
