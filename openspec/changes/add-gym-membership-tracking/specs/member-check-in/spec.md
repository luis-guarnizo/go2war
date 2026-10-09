# Spec Delta

## Purpose

Permite que cada miembro registre su propio ingreso al gym escaneando un código QR en la entrada, y controla el consumo de los paquetes de entrenos a partir de esos ingresos.

## ADDED Requirements

### Requirement: QR de ingreso en la entrada
El sistema SHALL ofrecer un código QR imprimible que el administrador descarga desde la app y que abre la página pública de ingreso del gym. Esa página MUST NOT requerir sesión del personal.

#### Scenario: Escanear el QR
- **WHEN** una persona escanea con su celular el QR impreso en la entrada
- **THEN** se abre la página de ingreso de GO2WAR GYM sin pedir usuario ni contraseña del personal

### Requirement: Identificación con cédula recordada en el celular
La primera vez, el miembro SHALL identificarse con su número de cédula. Tras un ingreso exitoso, el celular SHALL recordar al miembro para que los siguientes escaneos registren el ingreso sin volver a escribir la cédula. La página SHALL ofrecer "No soy yo" para olvidar el miembro recordado.

#### Scenario: Primer ingreso
- **WHEN** Ana escanea el QR por primera vez y escribe su cédula registrada
- **THEN** se registra su ingreso y el celular queda vinculado a Ana

#### Scenario: Ingresos siguientes
- **WHEN** Ana vuelve a escanear el QR desde el mismo celular otro día
- **THEN** su ingreso se registra con un solo toque de confirmación, sin escribir la cédula

#### Scenario: Celular de otra persona
- **WHEN** alguien toca "No soy yo" en un celular vinculado
- **THEN** el celular olvida al miembro y pide una cédula de nuevo

### Requirement: Cédula no registrada
Si la cédula no corresponde a ningún miembro activo, el sistema MUST NOT registrar ingreso ni crear miembros, y SHALL mostrar "No te encontramos, acércate a recepción".

#### Scenario: Persona nueva
- **WHEN** se escribe una cédula que no está registrada
- **THEN** se muestra "No te encontramos, acércate a recepción" y no se crea ningún registro

### Requirement: Resultado del ingreso según estado
Tras registrar el ingreso, la página SHALL mostrar el nombre de pila del miembro y su estado: verde "¡Bienvenido!" con la fecha de vencimiento o entrenos restantes si está activo, amarillo con aviso si está por vencer, y rojo "Tu plan venció, acércate a recepción" si está vencido o sin plan. La página MUST NOT mostrar datos de otros miembros.

#### Scenario: Miembro activo
- **WHEN** Ana, con Mensualidad vigente hasta el 14 de noviembre, registra su ingreso el 1 de noviembre
- **THEN** ve en verde "¡Bienvenida, Ana! Tu plan vence el 14 nov"

#### Scenario: Plan vencido
- **WHEN** un miembro con plan vencido escanea el QR
- **THEN** el ingreso queda registrado marcado "sin plan vigente" y ve en rojo "Tu plan venció, acércate a recepción"

### Requirement: Descuento de entrenos en paquetes
Si el miembro tiene un paquete de entrenos vigente (con entrenos restantes y dentro de su vigencia), cada ingreso SHALL descontar un entreno. Un paquete agotado o con la vigencia cumplida SHALL tratarse como vencido y MUST NOT descontar entrenos.

#### Scenario: Ingreso con paquete
- **WHEN** un miembro con 5 entrenos restantes registra su ingreso
- **THEN** le quedan 4 entrenos y la pantalla muestra "Te quedan 4 entrenos"

#### Scenario: Paquete agotado
- **WHEN** un miembro con 0 entrenos restantes escanea el QR
- **THEN** el ingreso queda marcado "sin plan vigente", no se descuenta nada y ve "Paquete agotado: debes renovar"

### Requirement: Un ingreso por día
El sistema SHALL registrar como máximo un ingreso por miembro por día calendario (hora de Colombia). Un escaneo repetido el mismo día MUST NOT crear otro ingreso ni descontar otro entreno.

#### Scenario: Doble escaneo
- **WHEN** Ana escanea el QR por segunda vez el mismo día
- **THEN** ve "Ya registraste tu ingreso hoy a las 6:05 a. m." y su saldo de entrenos no cambia

### Requirement: Ingreso manual y corrección por recepción
El personal SHALL poder registrar el ingreso de un miembro desde su ficha o la búsqueda (por ejemplo, si no tiene celular), con las mismas reglas, y eliminar un ingreso del mismo día; al eliminarlo, el entreno descontado SHALL devolverse.

#### Scenario: Ingreso manual
- **WHEN** recepción toca "Registrar ingreso" en la ficha de un miembro sin celular
- **THEN** el ingreso queda registrado igual que si hubiera escaneado el QR

#### Scenario: Eliminar ingreso
- **WHEN** recepción elimina un ingreso de hoy que descontó un entreno
- **THEN** el saldo de entrenos aumenta en 1

### Requirement: Saldo inicial de paquetes
Una membresía de paquete de entrenos SHALL iniciar con el número de entrenos del plan y vigencia hasta la fecha de inicio más los días de vigencia menos un día.

#### Scenario: Compra de 12 Entrenos
- **WHEN** se vende "12 Entrenos" con inicio el 1 de octubre de 2026
- **THEN** el miembro tiene 12 entrenos restantes válidos hasta el 30 de octubre de 2026
