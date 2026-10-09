# Spec Delta

## Purpose

Define los planes que vende el gym (por tiempo, paquetes de entrenos y planes para varias personas) con su precio vigente, como base para registrar ventas.

## ADDED Requirements

### Requirement: Tipos de plan
Cada plan SHALL tener nombre, tipo ("por tiempo" o "paquete de entrenos"), número de personas que cubre (1 o más) y precio en COP. Un plan por tiempo SHALL definir su duración en días o en meses; un paquete de entrenos SHALL definir número de entrenos y vigencia en días.

#### Scenario: Plan por tiempo en meses
- **WHEN** se consulta el plan "Trimestre"
- **THEN** el sistema lo muestra como plan por tiempo de 3 meses, 1 persona, $365.000

#### Scenario: Paquete de entrenos
- **WHEN** se consulta el plan "8 Entrenos"
- **THEN** el sistema lo muestra como paquete de 8 entrenos con vigencia de 30 días, 1 persona, $90.000

### Requirement: Catálogo inicial 2026
En una instalación nueva el sistema SHALL tener precargados los planes de la lista "Planes Año 2026": Mensualidad (1 mes, $135.000), Bimestre (2 meses, $255.000), Trimestre (3 meses, $365.000), Semestre (6 meses, $690.000), Año (12 meses, $1.300.000), Semanal (7 días, $50.000), Quincenal (15 días, $85.000), 8 Entrenos (8 entrenos/30 días, $90.000), 12 Entrenos (12 entrenos/30 días, $115.000) y Plan pareja (1 mes, 2 personas, $250.000).

#### Scenario: Instalación nueva
- **WHEN** se abre la lista de planes por primera vez
- **THEN** aparecen los 10 planes 2026 con sus precios y duraciones

### Requirement: Edición de planes por el administrador
El administrador SHALL poder crear planes, cambiar su precio y datos, y desactivarlos. Un cambio de precio MUST NOT alterar el valor de ventas ya registradas.

#### Scenario: Cambio de precio
- **WHEN** el administrador cambia el precio de "Mensualidad" a $140.000
- **THEN** las ventas nuevas de Mensualidad sugieren $140.000 y las ventas anteriores conservan $135.000

#### Scenario: Plan desactivado
- **WHEN** el administrador desactiva "Semanal"
- **THEN** el plan no aparece al registrar una venta nueva, pero sigue visible en el historial de las ventas existentes

### Requirement: Formato de precios
El sistema SHALL mostrar todos los valores en pesos colombianos sin decimales y con punto como separador de miles.

#### Scenario: Visualización de precio
- **WHEN** se muestra el precio del plan "Año"
- **THEN** se ve como "$1.300.000"
