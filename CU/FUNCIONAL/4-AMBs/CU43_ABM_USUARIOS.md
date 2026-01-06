# CU43 - ABM USUARIOS: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** ABM (Alta/Baja/Modificacion)
**Categoria:** Administracion de Usuarios

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Usuarios Autorizados](#2-usuarios-autorizados)
3. [Operaciones Disponibles](#3-operaciones-disponibles)
4. [Roles del Sistema](#4-roles-del-sistema)
5. [Validaciones](#5-validaciones)
6. [Campos del Formulario](#6-campos-del-formulario)
7. [Flujos del Proceso](#7-flujos-del-proceso)
8. [Politica de Contrasenas](#8-politica-de-contrasenas)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite gestionar los **usuarios del sistema**. Incluye alta, baja, modificacion, desbloqueo y forzado de cambio de contrasena.

### 1.2 Importancia en el Sistema

La gestion de usuarios es **critica** porque:
- Controla el acceso al sistema
- Define que operaciones puede realizar cada persona
- Cumple requisitos ANMAT de identificacion de usuarios (Disp. 4159 Anexo 6)
- Mantiene trazabilidad de quien realiza cada operacion

### 1.3 Requisitos ANMAT

| Requisito | Descripcion | Implementacion |
|-----------|-------------|----------------|
| Req. 12.3 | Registro de ABM de usuarios | Log de USER_CREATED, USER_MODIFIED, USER_DELETED |
| Req. 2 | Identificacion unica | Username unico por usuario |
| Req. 9 | Control de acceso | Roles con permisos diferenciados |

---

## 2. Usuarios Autorizados

### 2.1 Roles con Acceso al ABM

| Rol | Nivel | Alta | Modificacion | Baja | Desbloquear | Forzar Cambio Password |
|-----|-------|------|--------------|------|-------------|------------------------|
| ADMIN | 6 | SI | SI | SI | SI | SI |

### 2.2 Cambio de Password Propio

| Operacion | Usuarios |
|-----------|----------|
| Cambiar password propio | Todos los usuarios autenticados |

**URL:** `/users/change-password`

---

## 3. Operaciones Disponibles

### 3.1 Alta de Usuario

| Aspecto | Detalle |
|---------|---------|
| URL | `/users/add-user` |
| Metodo HTTP | GET (formulario), POST (guardar) |
| Resultado | Usuario con mustChangePassword=true |

### 3.2 Modificacion de Usuario

| Aspecto | Detalle |
|---------|---------|
| URL | `/users/edit-user/{id}` |
| Metodo HTTP | GET (formulario), POST (guardar) |
| Incluye | Cambio de rol, fecha expiracion, reset password |

### 3.3 Baja de Usuario (Eliminacion Fisica)

| Aspecto | Detalle |
|---------|---------|
| URL | `/users/delete-user` |
| Metodo HTTP | POST |
| Efecto | Eliminacion FISICA (no soft delete) |
| Auditoria | Registro permanente en log |

### 3.4 Desbloquear Usuario

| Aspecto | Detalle |
|---------|---------|
| URL | `/users/unlock-user` |
| Metodo HTTP | POST |
| Efecto | accountLocked=false, failedLoginAttempts=0 |

### 3.5 Forzar Cambio de Contrasena

| Aspecto | Detalle |
|---------|---------|
| URL | `/users/force-password-change` |
| Metodo HTTP | POST |
| Efecto | mustChangePassword=true |

### 3.6 Cambio de Password Propio

| Aspecto | Detalle |
|---------|---------|
| URL | `/users/change-password` |
| Metodo HTTP | GET (formulario), POST (cambiar) |
| Usuarios | Todos los autenticados |

---

## 4. Roles del Sistema

### 4.1 Jerarquia de Roles

| Rol | Nivel | Descripcion | Permisos Principales |
|-----|-------|-------------|----------------------|
| ADMIN | 6 | Administrador del sistema | Todos |
| DT | 5 | Director Tecnico | Liberacion, Trazado |
| GERENTE_GARANTIA_CALIDAD | 4 | Gerente Garantia Calidad | Liberacion, Devoluciones, Recalls |
| GERENTE_CONTROL_CALIDAD | 3 | Gerente Control Calidad | Resultados analisis, Reanalisis |
| SUPERVISOR_PLANTA | 3 | Supervisor de Planta | Produccion, Ajustes |
| ANALISTA_CONTROL_CALIDAD | 2 | Analista Control Calidad | Cuarentena, Muestreo |
| ANALISTA_PLANTA | 2 | Analista de Planta | Compras, Ventas, Proveedores |
| AUDITOR | 1 | Auditor externo | Solo lectura, sin modificaciones |

### 4.2 Propiedades de Roles

| Rol | canView | canModify | isReadOnly |
|-----|---------|-----------|------------|
| ADMIN | true | true | false |
| DT | true | true | false |
| GERENTE_GARANTIA_CALIDAD | true | true | false |
| GERENTE_CONTROL_CALIDAD | true | true | false |
| SUPERVISOR_PLANTA | true | true | false |
| ANALISTA_CONTROL_CALIDAD | true | true | false |
| ANALISTA_PLANTA | true | true | false |
| AUDITOR | true | false | true |

### 4.3 Restricciones del Rol AUDITOR

| Restriccion | Efecto |
|-------------|--------|
| Solo lectura | No puede ejecutar ningun CU que modifique datos |
| Accesos logueados | Todos sus accesos se registran en BD y archivo |
| Sin reverso | No puede ejecutar CU31 (Reverso) |
| Reportes | Si puede acceder a reportes |

---

## 5. Validaciones

### 5.1 Validaciones de Campo

| Campo | Regla | Mensaje de Error |
|-------|-------|------------------|
| Username | Obligatorio, 3-50 chars | "El nombre de usuario es obligatorio" |
| Username | Unico | "Ya existe un usuario con ese nombre" |
| Password | Obligatorio (alta) | "La contrasena es obligatoria" |
| Password | Cumplir politica | Ver seccion 8 |
| Rol | Obligatorio | "El rol es obligatorio" |
| Fecha Expiracion | Opcional, formato valido | "Formato de fecha invalido" |

### 5.2 Politica de Contrasenas

Las contrasenas deben cumplir (configurable via CU42):

| Requisito | Default | Configurable |
|-----------|---------|--------------|
| Longitud minima | 8 caracteres | SI |
| Mayusculas | Requerida | SI |
| Minusculas | Requerida | SI |
| Numeros | Requerido | SI |
| Caracteres especiales | Requerido | SI |

---

## 6. Campos del Formulario

### 6.1 Alta de Usuario

| Campo | Tipo | Obligatorio | Descripcion |
|-------|------|-------------|-------------|
| Username | Texto | SI | Nombre de usuario unico |
| Password | Password | SI | Contrasena inicial |
| Confirmar Password | Password | SI | Debe coincidir |
| Rol | Selector | SI | Rol del usuario |
| Fecha Expiracion | Fecha | NO | Fecha de vencimiento de cuenta |

### 6.2 Modificacion de Usuario

| Campo | Tipo | Obligatorio | Descripcion |
|-------|------|-------------|-------------|
| Username | Texto | SI | No editable si tiene movimientos |
| Nuevo Password | Password | NO | Dejar vacio para no cambiar |
| Confirmar Password | Password | Condicional | Si se ingresa nuevo password |
| Rol | Selector | SI | Rol del usuario |
| Fecha Expiracion | Fecha | NO | Fecha de vencimiento de cuenta |

### 6.3 Cambio de Password Propio

| Campo | Tipo | Obligatorio | Descripcion |
|-------|------|-------------|-------------|
| Password Actual | Password | SI | Verificacion de identidad |
| Nuevo Password | Password | SI | Nueva contrasena |
| Confirmar Password | Password | SI | Debe coincidir |

### 6.4 Campos Automaticos

| Campo | Valor | Momento |
|-------|-------|---------|
| mustChangePassword | true | Alta |
| passwordChangedAt | null | Alta |
| failedLoginAttempts | 0 | Alta |
| accountLocked | false | Alta |
| lockTime | null | Alta |

---

## 7. Flujos del Proceso

### 7.1 Flujo de Alta de Usuario

```
[ADMIN accede a /users/add-user]
       |
       v
[Sistema muestra formulario vacio]
       |
       v
[ADMIN completa campos]
       |
       v
[ADMIN hace clic en Guardar]
       |
       v
[Valida username unico]
       |
       +---> (Duplicado) ---> [Error: "Ya existe usuario"]
       |
       v
[Valida password contra politica]
       |
       +---> (No cumple) ---> [Muestra requisitos faltantes]
       |
       v
[Valida rol existe]
       |
       v
[Hashea password con BCrypt]
       |
       v
[Guarda usuario con mustChangePassword=true]
       |
       v
[Log: USER_CREATED | admin={admin} | new_user={user} | role={rol}]
       |
       v
[Redirige a listado con mensaje exito]
```

### 7.2 Flujo de Primer Login

```
[Usuario ingresa credenciales]
       |
       v
[Sistema valida credenciales]
       |
       v
[Verifica mustChangePassword=true]
       |
       v
[Redirige a /users/change-password?reason=required]
       |
       v
[Usuario ingresa nueva contrasena]
       |
       v
[Valida contra politica]
       |
       v
[Actualiza password, mustChangePassword=false, passwordChangedAt=ahora]
       |
       v
[Redirige a pagina principal]
```

### 7.3 Flujo de Bloqueo por Intentos Fallidos

```
[Usuario ingresa credenciales incorrectas]
       |
       v
[Incrementa failedLoginAttempts]
       |
       v
[Verifica >= max-attempts (configurable)]
       |
       +---> (No) ---> [Muestra intentos restantes]
       |
       v
[Marca accountLocked=true, lockTime=ahora]
       |
       v
[Usuario no puede ingresar]
       |
       +---> (Espera block-duration-minutes) ---> [Desbloqueo automatico]
       |
       +---> (ADMIN desbloquea) ---> [accountLocked=false]
```

### 7.4 Flujo de Desbloqueo Manual

```
[ADMIN accede a /users/list-users]
       |
       v
[Ve usuario bloqueado (indicador visual)]
       |
       v
[Hace clic en Desbloquear]
       |
       v
[Sistema marca accountLocked=false, failedLoginAttempts=0]
       |
       v
[Log: USER_UNLOCKED | admin={admin} | user={user}]
       |
       v
[Usuario puede intentar login nuevamente]
```

---

## 8. Politica de Contrasenas

### 8.1 Requisitos Default

| Requisito | Valor Default | Clave de Configuracion |
|-----------|---------------|------------------------|
| Longitud minima | 8 | security.password.min-length |
| Mayusculas | Requerida | security.password.require-uppercase |
| Minusculas | Requerida | security.password.require-lowercase |
| Numeros | Requerido | security.password.require-digit |
| Especiales | Requerido | security.password.require-special-char |
| Caracteres especiales | !@#$%^&*()... | security.password.special-characters |

### 8.2 Expiracion de Password

| Aspecto | Valor Default | Clave |
|---------|---------------|-------|
| Dias hasta expiracion | 180 | security.password.expiration-days |
| Historial (no repetir) | 5 | security.password.history-count |

### 8.3 Mensajes de Validacion

| Requisito Faltante | Mensaje |
|--------------------|---------|
| Longitud | "La contrasena debe tener al menos {n} caracteres" |
| Mayuscula | "La contrasena debe contener al menos una mayuscula" |
| Minuscula | "La contrasena debe contener al menos una minuscula" |
| Numero | "La contrasena debe contener al menos un numero" |
| Especial | "La contrasena debe contener al menos un caracter especial" |

---

## 9. Casos de Uso Ejemplos

### 9.1 Alta de Analista de Planta

**Situacion:** Se incorpora un nuevo empleado al area de planta.

**Accion:**
1. ADMIN accede a `/users/add-user`
2. Ingresa:
   - Username: `eraciti`
   - Password: `Temp@1234`
   - Rol: ANALISTA_PLANTA
3. Guarda

**Resultado:**
- Usuario creado con mustChangePassword=true
- Al primer login, debera cambiar contrasena
- Log: `USER_CREATED | admin=admin | new_user=eraciti | role=ANALISTA_PLANTA`

### 9.2 Desbloqueo de Usuario

**Situacion:** Un usuario intento demasiadas veces con contrasena incorrecta.

**Accion:**
1. ADMIN ve en listado que `jmartinez` esta bloqueado
2. Hace clic en "Desbloquear"

**Resultado:**
- Usuario puede volver a intentar login
- Log: `USER_UNLOCKED | admin=admin | user=jmartinez`

### 9.3 Forzar Cambio de Password

**Situacion:** Se sospecha que la contrasena de un usuario fue comprometida.

**Accion:**
1. ADMIN accede a `/users/list-users`
2. Selecciona usuario y hace clic en "Forzar cambio de password"

**Resultado:**
- Usuario debera cambiar contrasena en proximo login
- Log: `USER_FORCE_PASSWORD_CHANGE | admin=admin | user=npao`

### 9.4 Cambio de Rol

**Situacion:** Un analista es promovido a supervisor.

**Accion:**
1. ADMIN accede a `/users/edit-user/{id}`
2. Cambia rol de ANALISTA_PLANTA a SUPERVISOR_PLANTA
3. Guarda

**Resultado:**
- Usuario tiene nuevos permisos al siguiente login
- Log: `USER_MODIFIED | admin=admin | user=msilva | changes=role:ANALISTA_PLANTA->SUPERVISOR_PLANTA`

### 9.5 Baja de Usuario

**Situacion:** Un empleado deja la empresa.

**Accion:**
1. ADMIN accede a `/users/list-users`
2. Hace clic en "Eliminar" para el usuario
3. Confirma eliminacion

**Resultado:**
- Usuario eliminado fisicamente de BD
- Log permanente: `USER_DELETED | admin=admin | deleted_user=jperez | role=ANALISTA_PLANTA`
- Movimientos del usuario mantienen referencia historica

---

## 10. Preguntas Frecuentes

### 10.1 Sobre Usuarios

**P: ¿Puedo tener dos usuarios con el mismo username?**
R: No. El username es unico en el sistema.

**P: ¿Que pasa con los movimientos si elimino un usuario?**
R: Los movimientos mantienen referencia historica al usuario. Por eso la eliminacion se registra en log.

**P: ¿Puedo reactivar un usuario eliminado?**
R: No. La eliminacion es fisica. Debe crearse como nuevo usuario.

### 10.2 Sobre Contrasenas

**P: ¿Cada cuanto expira la contrasena?**
R: Por defecto cada 180 dias. Configurable en CU42.

**P: ¿Puedo reutilizar contrasenas anteriores?**
R: No, el sistema guarda historial (default: 5 ultimas).

**P: ¿Que hago si olvide mi contrasena?**
R: ADMIN debe resetear la contrasena desde edicion de usuario.

### 10.3 Sobre Bloqueo

**P: ¿Cuantos intentos tengo antes de ser bloqueado?**
R: Por defecto 5. Configurable en CU42.

**P: ¿Cuanto dura el bloqueo?**
R: Por defecto 15 minutos. Configurable en CU42.

**P: ¿Se desbloquea automaticamente?**
R: Si, despues de block-duration-minutes. Tambien ADMIN puede desbloquear manualmente.

### 10.4 Sobre Roles

**P: ¿Un usuario puede tener multiples roles?**
R: No. Cada usuario tiene exactamente un rol.

**P: ¿Que diferencia hay entre DT y GERENTE_GARANTIA_CALIDAD?**
R: Ambos pueden liberar lotes. DT tiene nivel superior en jerarquia de reversos.

---

## Resumen de Puntos Clave

1. **Tipo:** ABM de usuarios del sistema
2. **Usuario unico:** ADMIN (para ABM), todos (para cambiar password propio)
3. **Roles:** 8 roles con jerarquia de niveles 1-6
4. **Eliminacion:** Fisica (no soft delete), con log permanente
5. **Bloqueo:** Automatico por intentos fallidos, desbloqueo manual o automatico
6. **Password:** Politica configurable via CU42
7. **Primer login:** Obliga cambio de contrasena
8. **Auditoria ANMAT:** Logs de USER_CREATED, USER_MODIFIED, USER_DELETED, USER_UNLOCKED

---

## Relacion con Otros CUs

### CUs que Dependen de Usuarios

| CU | Uso del Usuario |
|----|-----------------|
| Todos | Cada movimiento registra el usuario que lo ejecuto |
| CU31 | Verifica jerarquia para autorizar reverso |
| CU9/CU10 | Usa usuario especial "system_auto" |

### Documentacion Relacionada

| Documento | Contenido |
|-----------|-----------|
| CONITRACK-SEGURIDAD-VALIDACION-FORMAL.md | Validacion formal de seguridad |
| CU42_ABM_CONFIGURACION.md | Configuracion de politicas de password |

---

**Fin del Documento - CU43_ABM_USUARIOS v1.0 - Especificacion Funcional**
