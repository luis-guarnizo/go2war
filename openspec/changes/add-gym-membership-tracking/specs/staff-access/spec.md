# Spec Delta

## Purpose

Garantiza que solo el personal autorizado de GO2WAR GYM pueda ver y modificar la información de miembros, planes y pagos.

## ADDED Requirements

### Requirement: Ingreso con usuario y contraseña
El sistema SHALL exigir que el personal inicie sesión con usuario y contraseña antes de acceder a cualquier pantalla o dato.

#### Scenario: Acceso sin sesión
- **WHEN** una persona sin sesión abre cualquier pantalla de la app
- **THEN** el sistema la redirige a la pantalla de ingreso sin mostrar datos

#### Scenario: Credenciales correctas
- **WHEN** un usuario activo ingresa usuario y contraseña correctos
- **THEN** el sistema inicia la sesión y muestra el panel de control

#### Scenario: Credenciales incorrectas
- **WHEN** se ingresa una contraseña incorrecta
- **THEN** el sistema muestra "Usuario o contraseña incorrectos" sin indicar cuál de los dos falló

### Requirement: Cierre de sesión
El sistema SHALL permitir cerrar sesión y SHALL invalidar la sesión tras 12 horas sin actividad.

#### Scenario: Cerrar sesión
- **WHEN** el usuario toca "Cerrar sesión"
- **THEN** la sesión queda invalidada y se muestra la pantalla de ingreso

### Requirement: Roles de administrador y recepción
El sistema SHALL distinguir dos roles: "administrador" (acceso total) y "recepción" (puede registrar miembros, ventas y asistencias, pero no editar precios, anular ventas ni gestionar usuarios).

#### Scenario: Recepción intenta editar un precio
- **WHEN** un usuario con rol recepción intenta modificar el precio de un plan
- **THEN** el sistema rechaza la acción con el mensaje "No tienes permiso para esta acción"

#### Scenario: Administrador crea usuario
- **WHEN** un administrador crea un usuario con nombre, usuario, contraseña y rol
- **THEN** el nuevo usuario puede iniciar sesión con ese rol

### Requirement: Administrador inicial
El sistema SHALL permitir crear el primer usuario administrador durante la instalación, sin que exista una cuenta con contraseña predeterminada.

#### Scenario: Instalación nueva
- **WHEN** se instala la app y se ejecuta el proceso de creación del administrador inicial con usuario y contraseña
- **THEN** existe exactamente un administrador con esas credenciales
