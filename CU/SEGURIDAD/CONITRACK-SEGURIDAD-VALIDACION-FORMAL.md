# CONITRACK - DOCUMENTO DE VALIDACION
# MODULO DE SEGURIDAD Y GESTION DE USUARIOS

**Sistema de Trazabilidad de Lotes Farmaceuticos**

---

| Informacion del Documento | |
|---------------------------|----------------------------------|
| **Version** | 2.1 |
| **Fecha** | 6 de Diciembre de 2025 |
| **Validado** | Contra codigo fuente |
| **Clasificacion** | Documento de Validacion |
| **Estado** | VIGENTE |
| **Tipo** | Diseno, Analisis, Testeo, Aceptacion |

---

## INDICE

1. [Introduccion](#1-introduccion)
2. [Diseno del Sistema de Seguridad](#2-diseno-del-sistema-de-seguridad)
3. [Sistema de Autenticacion](#3-sistema-de-autenticacion)
4. [Gestion de Contrasenas](#4-gestion-de-contrasenas)
5. [Sistema de Bloqueo de Cuentas](#5-sistema-de-bloqueo-de-cuentas)
6. [Roles y Permisos](#6-roles-y-permisos)
7. [Auditoria y Trazabilidad](#7-auditoria-y-trazabilidad)
8. [Configuracion de Seguridad](#8-configuracion-de-seguridad)
9. [Plan de Pruebas y Validacion](#9-plan-de-pruebas-y-validacion)
10. [Procedimientos Operativos](#10-procedimientos-operativos)
11. [Contingencias](#11-contingencias)
12. [Criterios de Aceptacion](#12-criterios-de-aceptacion)
13. [Firmas y Aprobaciones](#13-firmas-y-aprobaciones)

---

## 1. INTRODUCCION

### 1.1 Proposito

Este documento describe, analiza y valida los mecanismos de seguridad implementados en el sistema CONITRACK para garantizar:

| Principio | Descripcion |
|-----------|-------------|
| **Confidencialidad** | Solo usuarios autorizados acceden a la informacion |
| **Integridad** | Los datos no pueden ser modificados sin autorizacion ni registro |
| **Disponibilidad** | El sistema esta disponible para usuarios autorizados |
| **Trazabilidad** | Todas las acciones quedan registradas con identificacion del usuario |
| **No Repudio** | Las acciones realizadas no pueden ser negadas por el usuario |

### 1.2 Alcance

Este documento cubre la validacion de:

- Autenticacion y autorizacion de usuarios
- Gestion de usuarios y roles
- Politicas de contrasenas
- Sistema de bloqueo por intentos fallidos
- Registro de auditoria (audit trail)
- Configuracion de parametros de seguridad
- Registro de gestion de usuarios (ABM)

### 1.3 Normativa Aplicable

| Normativa | Descripcion | Requisitos Cubiertos |
|-----------|-------------|---------------------|
| ANMAT Disp. 4159/2023 | Buenas Practicas de Fabricacion | Anexo 6, Req. 2, 9, 12 |
| ANMAT Disp. 3827/2018 | Trazabilidad de Medicamentos | Identificacion de usuarios |
| GAMP 5 | Buenas Practicas de Fabricacion Automatizada | Categorias de software |

### 1.4 Definiciones

| Termino | Definicion |
|---------|------------|
| **Autenticacion** | Proceso de verificar la identidad de un usuario |
| **Autorizacion** | Proceso de verificar los permisos de un usuario autenticado |
| **Sesion** | Periodo de tiempo en que un usuario permanece autenticado |
| **Bloqueo de cuenta** | Estado en que una cuenta no puede autenticarse temporalmente |
| **Audit Trail** | Registro cronologico de todas las actividades del sistema |
| **Rol** | Conjunto de permisos asignados a un tipo de usuario |
| **ABM** | Alta, Baja y Modificacion de registros |

---

## 2. DISENO DEL SISTEMA DE SEGURIDAD

### 2.1 Arquitectura de Seguridad

El sistema implementa seguridad en capas:

| Capa | Funcion | Controles |
|------|---------|-----------|
| **Presentacion** | Interfaz de usuario | Formulario de login, tokens CSRF, gestion de sesion |
| **Seguridad** | Control de acceso | Filtros de autenticacion, verificacion de bloqueo, autorizacion por rol |
| **Negocio** | Logica de seguridad | Validacion de contrasenas, control de intentos, configuracion |
| **Datos** | Persistencia | Almacenamiento seguro de credenciales, auditoria |

### 2.2 Componentes del Sistema de Seguridad

| Componente | Funcion |
|------------|---------|
| Filtro de Bloqueo | Verifica si IP o usuario estan bloqueados antes de procesar login |
| Servicio de Usuarios | Carga y valida credenciales de usuarios |
| Manejador de Login Exitoso | Procesa acciones post-autenticacion exitosa |
| Manejador de Login Fallido | Registra intentos fallidos y actualiza contadores |
| Servicio de Intentos | Control de rate limiting por IP |
| Validador de Contrasenas | Verifica cumplimiento de politica de contrasenas |
| Servicio de Configuracion | Gestiona parametros configurables de seguridad |
| Interceptor de Auditoria | Registra accesos del rol AUDITOR |

### 2.3 Tecnologias de Seguridad Utilizadas

| Tecnologia | Uso | Beneficio |
|------------|-----|-----------|
| Spring Security | Framework de seguridad | Estandar de la industria, ampliamente auditado |
| BCrypt | Hash de contrasenas | Resistente a ataques de fuerza bruta |
| Tokens CSRF | Proteccion contra falsificacion | Previene ataques cross-site |
| Cookies HttpOnly | Proteccion de sesion | Previene robo de sesion por XSS |
| HTTPS/TLS | Encriptacion en transito | Protege datos en comunicacion |

---

## 3. SISTEMA DE AUTENTICACION

### 3.1 Flujo de Autenticacion

El proceso de login sigue estos pasos secuenciales:

| Paso | Verificacion | Accion si Falla |
|------|--------------|-----------------|
| 1 | IP no esta bloqueada | Redirige a login con mensaje de bloqueo |
| 2 | Usuario existe en sistema | Mensaje "Usuario no encontrado" |
| 3 | Usuario no ha expirado | Mensaje "La cuenta ha expirado" |
| 4 | Cuenta no esta bloqueada | Mensaje "Cuenta bloqueada por X minutos" |
| 5 | Contrasena es correcta | Incrementa contador de intentos fallidos |

### 3.2 Verificaciones Post-Autenticacion

Despues de un login exitoso, el sistema verifica:

| Condicion | Accion |
|-----------|--------|
| Usuario marcado para cambio obligatorio | Redirige a pantalla de cambio de contrasena |
| Contrasena expirada (supera dias configurados) | Redirige a pantalla de cambio de contrasena |
| Normal | Redirige a pagina principal |

### 3.3 Gestion de Sesiones

| Parametro | Valor | Configurable |
|-----------|-------|--------------|
| Timeout de sesion | 25 minutos | Si |
| Cookies HttpOnly | Habilitado | No |
| Cookies Secure | Habilitado en HTTPS | No |
| Proteccion Session Fixation | Habilitado | No |

---

## 4. GESTION DE CONTRASENAS

### 4.1 Politica de Contrasenas

| Requisito | Valor por Defecto | Configurable |
|-----------|-------------------|--------------|
| Longitud minima | 8 caracteres | Si (6-20) |
| Requiere mayuscula | Si | Si |
| Requiere minuscula | Si | Si |
| Requiere digito | Si | Si |
| Requiere caracter especial | Si | Si |
| Caracteres especiales permitidos | !@#$%^&*()_+-=[];:,.<>? | Si |
| Dias de expiracion | 180 dias | Si (0-365) |

### 4.2 Almacenamiento Seguro

Las contrasenas se almacenan utilizando:

| Aspecto | Implementacion |
|---------|----------------|
| Algoritmo | BCrypt |
| Factor de trabajo | 10 (1024 iteraciones) |
| Salt | Unico por contrasena, 16 bytes |
| Reversibilidad | Unidireccional (no se puede revertir) |
| Proteccion | Resistente a rainbow tables y fuerza bruta |

### 4.3 Expiracion de Contrasenas

| Aspecto | Comportamiento |
|---------|----------------|
| Calculo | Fecha ultimo cambio + dias configurados |
| Aplica a | TODOS los usuarios incluyendo ADMIN |
| Accion al expirar | Redirige a cambio obligatorio en proximo login |
| Valor 0 | Desactiva expiracion |

### 4.4 Cambio de Contrasena

El flujo de cambio de contrasena valida:

| Validacion | Descripcion |
|------------|-------------|
| Contrasena actual correcta | Verifica identidad del usuario |
| Nueva contrasena cumple politica | Valida todos los requisitos |
| Nueva diferente a actual | Evita reutilizacion inmediata |
| Confirmacion coincide | Previene errores de tipeo |

---

## 5. SISTEMA DE BLOQUEO DE CUENTAS

### 5.1 Mecanismo de Proteccion

El sistema implementa proteccion dual contra ataques de fuerza bruta:

| Tipo | Ambito | Persistencia | Proposito |
|------|--------|--------------|-----------|
| Rate Limiting | Por IP | En memoria (volatil) | Proteccion rapida |
| Bloqueo de Cuenta | Por usuario | En base de datos | Proteccion persistente |

### 5.2 Parametros de Bloqueo

| Parametro | Valor por Defecto | Rango | Configurable |
|-----------|-------------------|-------|--------------|
| Intentos maximos | 5 | 1-10 | Si |
| Duracion del bloqueo | 15 minutos | 1-60 | Si |

### 5.3 Estados de Cuenta

| Estado | Condicion | Puede Hacer Login |
|--------|-----------|-------------------|
| Normal | Cuenta no bloqueada | Si |
| Bloqueado Activo | Bloqueado y tiempo no expirado | No |
| Bloqueo Expirado | Bloqueado pero tiempo expirado | Si (auto-desbloqueo) |

### 5.4 Mecanismos de Desbloqueo

| Mecanismo | Disparador | Responsable |
|-----------|------------|-------------|
| Auto-desbloqueo | Transcurre tiempo de bloqueo | Automatico |
| Login exitoso | Usuario ingresa credenciales correctas | Usuario |
| Desbloqueo manual | Administrador desbloquea desde panel | ADMIN |

### 5.5 Mensajes al Usuario

| Situacion | Mensaje Mostrado |
|-----------|------------------|
| Contrasena incorrecta | "Credenciales invalidas. Te quedan X intentos." |
| Cuenta bloqueada | "Cuenta bloqueada. Intente nuevamente en X minutos." |
| IP bloqueada | "Demasiados intentos. Intente en X minutos." |

---

## 6. ROLES Y PERMISOS

### 6.1 Jerarquia de Roles por Areas

El sistema define 8 roles organizados en 3 areas funcionales (GLOBAL, CALIDAD, PLANTA):

**Area GLOBAL** (superiores/inferiores a todos):

| Rol | Nivel | Descripcion |
|-----|-------|-------------|
| ADMIN | 6 | Administrador del sistema - superior a todos |
| DT | 5 | Director Tecnico - superior a todos excepto ADMIN |
| AUDITOR | 1 | Auditor externo (solo lectura) - inferior a todos |

**Area CALIDAD** (jerarquia interna):

| Rol | Nivel | Descripcion |
|-----|-------|-------------|
| GERENTE_GARANTIA_CALIDAD | 4 | Gerente de Garantia de Calidad |
| GERENTE_CONTROL_CALIDAD | 3 | Gerente de Control de Calidad |
| ANALISTA_CONTROL_CALIDAD | 2 | Analista de Control de Calidad |

**Area PLANTA** (jerarquia interna):

| Rol | Nivel | Descripcion |
|-----|-------|-------------|
| SUPERVISOR_PLANTA | 3 | Supervisor de Planta |
| ANALISTA_PLANTA | 2 | Analista de Planta |

**Importante:** Las areas CALIDAD y PLANTA NO tienen relacion jerarquica entre si.
Un rol de CALIDAD no puede ejercer autoridad sobre roles de PLANTA y viceversa.

### 6.2 Matriz de Permisos - Compras

| Operacion | ADMIN | DT | GER_GC | GER_CC | SUP_PL | ANA_CC | ANA_PL | AUDITOR |
|-----------|:-----:|:--:|:------:|:------:|:------:|:------:|:------:|:-------:|
| Ingreso Compra | X | - | - | - | - | - | X | - |
| Devolucion Compra | X | - | - | - | - | - | X | - |

### 6.3 Matriz de Permisos - Calidad

| Operacion | ADMIN | DT | GER_GC | GER_CC | SUP_PL | ANA_CC | ANA_PL | AUDITOR |
|-----------|:-----:|:--:|:------:|:------:|:------:|:------:|:------:|:-------:|
| Dictamen Cuarentena | X | - | - | - | - | X | - | - |
| Muestreo Multi-Bulto | X | - | - | - | - | X | - | - |
| Muestreo Trazable | X | - | - | - | - | X | - | - |
| Resultado Analisis | X | - | - | X | - | - | - | - |
| Reanalisis | X | - | - | X | - | - | - | - |
| Anulacion Analisis | X | - | - | X | - | - | - | - |

### 6.4 Matriz de Permisos - Produccion

| Operacion | ADMIN | DT | GER_GC | GER_CC | SUP_PL | ANA_CC | ANA_PL | AUDITOR |
|-----------|:-----:|:--:|:------:|:------:|:------:|:------:|:------:|:-------:|
| Consumo Produccion | X | - | - | - | X | - | - | - |
| Ingreso Produccion | X | - | - | - | X | - | - | - |

### 6.5 Matriz de Permisos - Ventas

| Operacion | ADMIN | DT | GER_GC | GER_CC | SUP_PL | ANA_CC | ANA_PL | AUDITOR |
|-----------|:-----:|:--:|:------:|:------:|:------:|:------:|:------:|:-------:|
| Confirmar Lote Liberado | X | X | X | - | - | - | - | - |
| Venta Producto | X | - | - | - | - | - | X | - |
| Devolucion Venta | X | - | X | - | - | - | - | - |
| Retiro Mercado | X | - | X | - | - | - | - | - |
| Trazado | X | X | X | - | - | - | - | - |

### 6.6 Matriz de Permisos - Contingencias

| Operacion | ADMIN | DT | GER_GC | GER_CC | SUP_PL | ANA_CC | ANA_PL | AUDITOR |
|-----------|:-----:|:--:|:------:|:------:|:------:|:------:|:------:|:-------:|
| Ajuste Inventario | X | - | - | - | X | - | - | - |
| Reverso Movimiento | X | X | X | X | X | X | X | - |

### 6.7 Matriz de Permisos - Consultas

| Operacion | ADMIN | DT | GER_GC | GER_CC | SUP_PL | ANA_CC | ANA_PL | AUDITOR |
|-----------|:-----:|:--:|:------:|:------:|:------:|:------:|:------:|:-------:|
| Lista de Lotes | X | X | X | X | X | X | X | X |
| Lista de Bultos | X | X | X | X | X | X | X | X |
| Lista de Movimientos | X | X | X | X | X | X | X | X |
| Lista de Analisis | X | X | X | X | X | X | X | X |
| Lista de Trazas | X | X | X | X | X | X | X | X |

### 6.8 Matriz de Permisos - Administracion

| Operacion | ADMIN | DT | GER_GC | GER_CC | SUP_PL | ANA_CC | ANA_PL | AUDITOR |
|-----------|:-----:|:--:|:------:|:------:|:------:|:------:|:------:|:-------:|
| ABM Proveedores | X | - | - | - | - | - | X | - |
| ABM Productos | X | - | - | X | - | - | - | - |
| Configuracion Sistema | X | - | - | - | - | - | - | - |
| ABM Usuarios | X | - | - | - | - | - | - | - |

### 6.9 Permisos Especiales

#### Rol AUDITOR

| Caracteristica | Descripcion |
|----------------|-------------|
| Acceso | Solo lectura a lotes, movimientos, reportes |
| Restriccion | Sin acceso a altas, bajas ni modificaciones |
| Cuenta temporal | Puede tener fecha de expiracion |
| Proposito | Auditorias externas de ANMAT |
| Logging | Todas sus consultas se registran automaticamente |

#### Reglas de Reverso de Movimientos (con Areas)

| Quien | Puede Reversar |
|-------|----------------|
| El creador del movimiento | Si (siempre) |
| ADMIN o DT (area GLOBAL) | Si (cualquier movimiento de cualquier area) |
| Usuario de MISMA area con nivel superior | Si |
| Usuario de DIFERENTE area (CALIDAD vs PLANTA) | No (sin relacion jerarquica) |
| Usuario con mismo nivel (no creador) | No |
| Usuario con nivel inferior | No |
| AUDITOR | Nunca (solo lectura) |
| ADMIN para movimientos legacy | Si |

**Nota:** Un GERENTE_CONTROL_CALIDAD NO puede reversar movimientos de ANALISTA_PLANTA aunque tenga nivel superior (3 > 2), porque pertenecen a areas diferentes.

---

## 7. AUDITORIA Y TRAZABILIDAD

### 7.1 Registro de ABM de Usuarios (ANMAT Req. 12.3)

Cumpliendo con el Requisito 12.3 de ANMAT: *"La creacion, cambio y la cancelacion de una autorizacion de acceso debe registrarse."*

| Evento | Informacion Registrada |
|--------|------------------------|
| Creacion de usuario | Administrador, nuevo usuario, rol asignado, fecha expiracion |
| Modificacion de usuario | Administrador, usuario, cambios realizados (rol, password, fecha) |
| Eliminacion de usuario | Administrador, usuario eliminado, rol que tenia |
| Desbloqueo de cuenta | Administrador, usuario desbloqueado |
| Forzar cambio password | Administrador, usuario afectado |

### 7.2 Registro de Accesos

| Tipo de Evento | Informacion Registrada |
|----------------|------------------------|
| Login exitoso | IP, usuario, roles asignados |
| Login fallido | IP, usuario, intentos restantes |
| Cuenta bloqueada | IP, usuario, minutos de bloqueo |
| Usuario expirado | Usuario, fecha de expiracion |
| Auto-desbloqueo | Usuario, razon |
| Cambio de contrasena | Usuario |
| Configuracion modificada | Clave, valor anterior, valor nuevo, usuario |

### 7.3 Registro de Acciones del AUDITOR

Todas las consultas realizadas por usuarios con rol AUDITOR se registran automaticamente:

| Campo Registrado | Descripcion |
|------------------|-------------|
| Usuario | ID y nombre de usuario |
| Rol | Rol al momento de la accion |
| Accion | Descripcion de la consulta |
| URL | Ruta accedida |
| Metodo HTTP | GET, POST, etc. |
| IP | Direccion IP del cliente |
| User Agent | Navegador/cliente utilizado |
| Fecha/Hora | Timestamp de la accion |

### 7.4 Retencion de Registros

| Tipo de Registro | Retencion Minima |
|------------------|------------------|
| Logs de aplicacion | 90 dias |
| Logs de auditoria | 365 dias (1 ano) |
| Auditoria de accesos (BD) | 5 anos |
| Movimientos (BD) | Permanente |

---

## 8. CONFIGURACION DE SEGURIDAD

### 8.1 Parametros de Login

| Parametro | Default | Min | Max | Descripcion |
|-----------|---------|-----|-----|-------------|
| Intentos maximos de login | 5 | 1 | 10 | Intentos antes de bloqueo |
| Duracion del bloqueo (min) | 15 | 1 | 60 | Minutos de bloqueo |
| Timeout de sesion (min) | 25 | 5 | 120 | Inactividad antes de logout |

### 8.2 Parametros de Contrasena

| Parametro | Default | Min | Max | Descripcion |
|-----------|---------|-----|-----|-------------|
| Longitud minima | 8 | 6 | 20 | Caracteres minimos |
| Dias de expiracion | 180 | 0 | 365 | 0 = nunca expira |
| Requiere mayuscula | Si | - | - | Al menos una A-Z |
| Requiere minuscula | Si | - | - | Al menos una a-z |
| Requiere numero | Si | - | - | Al menos un 0-9 |
| Requiere especial | Si | - | - | Al menos un simbolo |

### 8.3 Acceso a Configuracion

| Aspecto | Valor |
|---------|-------|
| URL | /admin/configuracion |
| Acceso | Solo rol ADMIN |
| Validacion | Rangos min/max por parametro |
| Auditoria | Cada cambio se registra |

---

## 9. PLAN DE PRUEBAS Y VALIDACION

### 9.1 Objetivo

Validar que los controles de acceso, autenticacion, contrasenas, bloqueos, auditoria y roles funcionen segun el diseno y normativa ANMAT.

### 9.2 Alcance de Pruebas

- Usuarios y roles
- Autenticacion
- Contrasenas
- Bloqueo de cuentas
- Auditoria
- Segregacion de funciones
- Accesos directos y restricciones

### 9.3 Casos de Prueba

#### TC-01: Crear usuario (segregacion de roles)

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar que solo ADMIN puede crear usuarios |
| **Precondicion** | Usuarios ADMIN y OPERADOR existen |
| **Procedimiento** | 1. Iniciar sesion como ADMIN e intentar crear usuario<br>2. Repetir como OPERADOR |
| **Resultado Esperado** | ADMIN puede crear; OPERADOR recibe acceso denegado |
| **Criterio de Aceptacion** | PASA si se cumple el resultado esperado |

#### TC-02: Login con credenciales validas

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar autenticacion exitosa |
| **Precondicion** | Usuario valido existe |
| **Procedimiento** | Ingresar usuario y contrasena correctos |
| **Resultado Esperado** | Acceso permitido y evento registrado en log |
| **Criterio de Aceptacion** | PASA si accede y se genera log LOGIN_SUCCESS |

#### TC-03: Login fallido

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar registro de intentos fallidos |
| **Precondicion** | Usuario valido existe |
| **Procedimiento** | Ingresar contrasena incorrecta varias veces |
| **Resultado Esperado** | Intento rechazado + incremento en contador |
| **Criterio de Aceptacion** | PASA si muestra intentos restantes y se registra |

#### TC-04: Contrasena invalida rechazada

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar politica de contrasenas |
| **Precondicion** | Usuario puede cambiar contrasena |
| **Procedimiento** | Intentar establecer contrasena simple (ej. "1234") |
| **Resultado Esperado** | Sistema rechaza segun politica |
| **Criterio de Aceptacion** | PASA si muestra error de validacion |

#### TC-05: Contrasena valida aceptada

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar aceptacion de contrasena conforme |
| **Precondicion** | Usuario puede cambiar contrasena |
| **Procedimiento** | Cambiar a contrasena que cumple requisitos |
| **Resultado Esperado** | Cambio exitoso + registro de auditoria |
| **Criterio de Aceptacion** | PASA si cambia y se registra evento |

#### TC-06: Contrasena expirada

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar manejo de expiracion |
| **Precondicion** | Usuario con contrasena expirada |
| **Procedimiento** | Ingresar con usuario cuya contrasena expiro |
| **Resultado Esperado** | Redireccion a cambio obligatorio |
| **Criterio de Aceptacion** | PASA si redirige a /users/change-password |

#### TC-07: Bloqueo por intentos fallidos

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar bloqueo automatico |
| **Precondicion** | Usuario con cuenta normal |
| **Procedimiento** | Realizar 5+ intentos de login fallidos |
| **Resultado Esperado** | Cuenta bloqueada + registro en auditoria |
| **Criterio de Aceptacion** | PASA si muestra mensaje de bloqueo |

#### TC-08: Desbloqueo de cuenta (ADMIN)

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar desbloqueo manual |
| **Precondicion** | Cuenta bloqueada existe |
| **Procedimiento** | ADMIN desbloquea cuenta desde panel de gestion |
| **Resultado Esperado** | Accion permitida y registrada |
| **Criterio de Aceptacion** | PASA si desbloquea y se registra USER_UNLOCKED |

#### TC-09: Accesos permitidos por rol

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar permisos positivos |
| **Precondicion** | Usuario ANALISTA_PLANTA existe |
| **Procedimiento** | Probar crear movimiento de ingreso compra |
| **Resultado Esperado** | Operacion habilitada |
| **Criterio de Aceptacion** | PASA si puede ejecutar la operacion |

#### TC-10: Accesos prohibidos por rol

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar restricciones de rol |
| **Precondicion** | Usuario ANALISTA_PLANTA existe |
| **Procedimiento** | Intentar confirmar liberacion de lote |
| **Resultado Esperado** | Acceso denegado |
| **Criterio de Aceptacion** | PASA si recibe error 403 Forbidden |

#### TC-11: Rol AUDITOR solo lectura

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar restriccion de solo lectura |
| **Precondicion** | Usuario AUDITOR existe |
| **Procedimiento** | Intentar modificar cualquier dato |
| **Resultado Esperado** | Solo lectura permitida |
| **Criterio de Aceptacion** | PASA si todas las modificaciones son denegadas |

#### TC-12: ADMIN no realiza tareas operativas

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar segregacion de funciones |
| **Precondicion** | Usuario ADMIN existe |
| **Procedimiento** | Intentar crear movimiento operativo |
| **Resultado Esperado** | Operacion permitida (ADMIN tiene acceso total) |
| **Criterio de Aceptacion** | PASA si ADMIN puede o no segun matriz |

#### TC-13: Segregacion - Operador crea, Supervisor aprueba

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar flujo de aprobacion |
| **Precondicion** | Usuarios OPERADOR y SUPERVISOR existen |
| **Procedimiento** | OPERADOR crea movimiento; SUPERVISOR lo aprueba |
| **Resultado Esperado** | Flujo valido completado |
| **Criterio de Aceptacion** | PASA si flujo se completa correctamente |

#### TC-14: Operador no puede aprobar propio movimiento

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar auto-aprobacion bloqueada |
| **Precondicion** | Movimiento creado por OPERADOR |
| **Procedimiento** | OPERADOR intenta aprobar su movimiento |
| **Resultado Esperado** | Rechazo de la operacion |
| **Criterio de Aceptacion** | PASA si recibe error de autorizacion |

#### TC-15: Editar movimiento cerrado

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar inmutabilidad |
| **Precondicion** | Movimiento cerrado/completado existe |
| **Procedimiento** | Intentar editar el movimiento |
| **Resultado Esperado** | Operacion denegada |
| **Criterio de Aceptacion** | PASA si no permite edicion |

#### TC-16: Cambio de dictamen por rol no autorizado

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar permisos de dictamen |
| **Precondicion** | Usuario sin permiso de dictamen |
| **Procedimiento** | Intentar cambiar dictamen de lote |
| **Resultado Esperado** | Rechazo de la operacion |
| **Criterio de Aceptacion** | PASA si recibe error 403 |

#### TC-17: Acceso directo por URL no permitido

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar proteccion de URLs |
| **Precondicion** | Usuario OPERADOR autenticado |
| **Procedimiento** | Intentar acceder a /admin/usuarios directamente |
| **Resultado Esperado** | Acceso bloqueado |
| **Criterio de Aceptacion** | PASA si redirige a error o login |

#### TC-18: Auditoria de login/logout

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar registro de sesiones |
| **Precondicion** | Usuario valido existe |
| **Procedimiento** | Iniciar sesion y cerrar sesion |
| **Resultado Esperado** | Ambos eventos registrados en logs |
| **Criterio de Aceptacion** | PASA si existen logs LOGIN_SUCCESS y logout |

#### TC-19: Auditoria de acciones criticas

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar registro de operaciones sensibles |
| **Precondicion** | Usuario con permisos adecuados |
| **Procedimiento** | Realizar cambio de dictamen o desbloqueo |
| **Resultado Esperado** | Registro completo en auditoria |
| **Criterio de Aceptacion** | PASA si se registra accion con detalles |

#### TC-20: Control de acceso a auditoria

| Aspecto | Descripcion |
|---------|-------------|
| **Objetivo** | Verificar acceso a logs de auditoria |
| **Precondicion** | Usuarios SUPERVISOR y OPERADOR existen |
| **Procedimiento** | Ambos intentan acceder a exportacion de auditoria |
| **Resultado Esperado** | Solo usuarios autorizados pueden acceder |
| **Criterio de Aceptacion** | PASA segun matriz de permisos |

### 9.4 Matriz de Resultados de Pruebas

| TC | Descripcion | Resultado | Fecha | Ejecutor |
|----|-------------|-----------|-------|----------|
| TC-01 | Crear usuario (segregacion) | PENDIENTE | | |
| TC-02 | Login valido | PENDIENTE | | |
| TC-03 | Login fallido | PENDIENTE | | |
| TC-04 | Contrasena invalida | PENDIENTE | | |
| TC-05 | Contrasena valida | PENDIENTE | | |
| TC-06 | Contrasena expirada | PENDIENTE | | |
| TC-07 | Bloqueo por intentos | PENDIENTE | | |
| TC-08 | Desbloqueo ADMIN | PENDIENTE | | |
| TC-09 | Accesos permitidos | PENDIENTE | | |
| TC-10 | Accesos prohibidos | PENDIENTE | | |
| TC-11 | AUDITOR solo lectura | PENDIENTE | | |
| TC-12 | ADMIN tareas operativas | PENDIENTE | | |
| TC-13 | SoD crear/aprobar | PENDIENTE | | |
| TC-14 | SoD auto-aprobacion | PENDIENTE | | |
| TC-15 | Movimiento cerrado | PENDIENTE | | |
| TC-16 | Dictamen no autorizado | PENDIENTE | | |
| TC-17 | URL directo | PENDIENTE | | |
| TC-18 | Auditoria login/logout | PENDIENTE | | |
| TC-19 | Auditoria acciones | PENDIENTE | | |
| TC-20 | Acceso auditoria | PENDIENTE | | |

---

## 10. PROCEDIMIENTOS OPERATIVOS

### 10.1 Creacion de Usuario

| Paso | Accion |
|------|--------|
| 1 | Acceder al panel de usuarios (solo ADMIN) |
| 2 | Completar: Username (unico), Password (cumpliendo politica), Rol, Fecha expiracion (opcional) |
| 3 | Guardar usuario |
| 4 | Comunicar credenciales de forma segura |
| 5 | Usuario debera cambiar password en primer login |

**Registro:** USER_CREATED con admin, usuario, rol, fecha

### 10.2 Desbloqueo de Cuenta

| Paso | Accion |
|------|--------|
| 1 | Identificar usuario bloqueado en lista |
| 2 | Click en boton de desbloqueo |
| 3 | Confirmar accion |
| 4 | Usuario puede intentar login |

**Registro:** USER_UNLOCKED con admin, usuario

### 10.3 Forzar Cambio de Password

| Paso | Accion |
|------|--------|
| 1 | Identificar usuario en lista |
| 2 | Click en boton de forzar cambio |
| 3 | Confirmar accion |
| 4 | En proximo login, usuario sera redirigido a cambio |

**Registro:** USER_FORCE_PASSWORD_CHANGE con admin, usuario

### 10.4 Creacion de Auditor Temporal

| Paso | Accion |
|------|--------|
| 1 | Crear usuario con rol AUDITOR |
| 2 | Establecer fecha de expiracion (fin de auditoria) |
| 3 | Comunicar credenciales al auditor |
| 4 | Post-auditoria: cuenta expira automaticamente |

---

## 11. CONTINGENCIAS

### 11.1 ADMIN Olvida Contrasena

| Opcion | Procedimiento | Tiempo |
|--------|---------------|--------|
| Intervencion BD | Actualizar hash de password en BD + forzar cambio | 5-15 min |
| Segundo ADMIN | Usar cuenta ADMIN de respaldo | 2 min |

### 11.2 ADMIN Bloqueado

| Opcion | Procedimiento | Tiempo |
|--------|---------------|--------|
| Esperar auto-desbloqueo | Transcurre tiempo de bloqueo | 15 min |
| Intervencion BD | Resetear campos de bloqueo en BD | 5 min |

### 11.3 Contrasena ADMIN Expirada

| Procedimiento | Tiempo |
|---------------|--------|
| Cambiar contrasena en pantalla de cambio obligatorio | 2 min |

### 11.4 Matriz de Contingencias

| Escenario | Severidad | Tiempo Recuperacion |
|-----------|-----------|---------------------|
| Admin olvido password | Media | 5-15 min |
| Admin bloqueado | Baja | 15 min (auto) |
| Password admin expirado | Baja | 2 min |
| Unico admin eliminado | Alta | 15-30 min (restore BD) |
| Compromiso credenciales | Critica | 30-60 min |

### 11.5 Recomendaciones Preventivas

1. Crear segundo usuario ADMIN como respaldo
2. Documentar credenciales de emergencia en lugar seguro fisico
3. Mantener acceso a base de datos documentado
4. No establecer fecha de expiracion para usuarios ADMIN permanentes
5. Monitorear logs de intentos de login bloqueados

---

## 12. CRITERIOS DE ACEPTACION

### 12.1 Criterios Funcionales

| # | Criterio | Verificacion |
|---|----------|--------------|
| 1 | Login con credenciales validas permite acceso | TC-02 |
| 2 | Login con credenciales invalidas es rechazado | TC-03 |
| 3 | Cuenta se bloquea despues de N intentos fallidos | TC-07 |
| 4 | Cuenta se desbloquea automaticamente despues de M minutos | TC-07 |
| 5 | ADMIN puede desbloquear cuentas manualmente | TC-08 |
| 6 | Politica de contrasenas se aplica correctamente | TC-04, TC-05 |
| 7 | Contrasenas expiradas fuerzan cambio | TC-06 |
| 8 | Roles restringen acceso segun matriz | TC-09, TC-10, TC-11 |
| 9 | Todas las acciones de ABM se registran | TC-19 |
| 10 | URLs protegidas no son accesibles directamente | TC-17 |

### 12.2 Criterios de Auditoria (ANMAT)

| # | Requisito ANMAT | Criterio de Aceptacion |
|---|-----------------|------------------------|
| 1 | Req. 2 - Personal | 8 roles definidos con permisos especificos |
| 2 | Req. 9 - Audit Trail | Todas las operaciones registradas con usuario y timestamp |
| 3 | Req. 12.1 - Acceso | Solo usuarios autorizados acceden al sistema |
| 4 | Req. 12.3 - ABM | Creacion, cambio y cancelacion de accesos se registra |
| 5 | Req. 12.4 - Identidad | Cada registro incluye identidad del operador |

### 12.3 Criterios de Seguridad

| # | Criterio | Metodo de Verificacion |
|---|----------|------------------------|
| 1 | Contrasenas almacenadas con BCrypt | Inspeccion de BD |
| 2 | Proteccion CSRF activa | Intento de POST sin token |
| 3 | Sesiones con timeout | Inactividad > 25 min |
| 4 | Rate limiting funcional | 5+ intentos fallidos |
| 5 | Logs con retencion adecuada | Verificacion de configuracion |

---

## 13. FIRMAS Y APROBACIONES

### 13.1 Elaboracion

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| Autor | | | |
| Revisor Tecnico | | | |

### 13.2 Aprobacion

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| QA | | | |
| IT | | | |
| Director Tecnico | | | |

### 13.3 Control de Versiones

| Version | Fecha | Autor | Cambios                                                    |
|---------|-------|-------|------------------------------------------------------------|
| 1.0 | 2025-11-30 | Sistema | Version inicial - Documento de Seguridad y Plan de Pruebas |
| 2.1 | 2025-12-06 | Sistema | Correccion timeout sesion (25 min), validacion contra codigo |

---

**FIN DEL DOCUMENTO**
