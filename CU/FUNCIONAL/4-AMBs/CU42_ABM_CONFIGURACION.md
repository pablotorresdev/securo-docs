# CU42 - CONFIGURACION DEL SISTEMA: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** Administracion
**Categoria:** Configuracion del Sistema

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Usuarios Autorizados](#2-usuarios-autorizados)
3. [Operaciones Disponibles](#3-operaciones-disponibles)
4. [Categorias de Configuracion](#4-categorias-de-configuracion)
5. [Validaciones](#5-validaciones)
6. [Flujos del Proceso](#6-flujos-del-proceso)
7. [Casos de Uso Ejemplos](#7-casos-de-uso-ejemplos)
8. [Preguntas Frecuentes](#8-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite gestionar los **parametros de configuracion** del sistema. Los parametros controlan el comportamiento de seguridad, lotes, auditoria, UI y otras funcionalidades.

### 1.2 Importancia en el Sistema

La configuracion centralizada permite:
- Ajustar politicas de seguridad sin codigo
- Definir alertas y umbrales operativos
- Cumplir requisitos ANMAT de configurabilidad
- Mantener trazabilidad de cambios de configuracion

### 1.3 Arquitectura de Configuracion

| Componente | Funcion |
|------------|---------|
| Entidad Configuracion | Almacena clave-valor en BD |
| ConfiguracionService | Cache en memoria + validacion tipada |
| ConfiguracionController | UI para edicion |
| ConfiguracionDataInitializer | Inicializa valores default |

---

## 2. Usuarios Autorizados

### 2.1 Roles con Acceso

| Rol | Nivel | Consulta | Modificacion | Reset |
|-----|-------|----------|--------------|-------|
| ADMIN | 6 | SI | SI | SI |

### 2.2 Roles Sin Acceso

| Rol | Motivo |
|-----|--------|
| Todos los demas | Solo ADMIN puede modificar configuracion del sistema |

---

## 3. Operaciones Disponibles

### 3.1 Listado de Configuracion

| Aspecto | Detalle |
|---------|---------|
| URL | `/admin/configuracion` |
| Metodo HTTP | GET |
| Vista | Configuraciones agrupadas por categoria |

### 3.2 Modificacion de Parametro

| Aspecto | Detalle |
|---------|---------|
| URL | `/admin/edit-configuracion` |
| Metodo HTTP | POST |
| Parametros | clave, nuevoValor |
| Restriccion | Solo parametros con editable=true |

### 3.3 Reset a Valor Default

| Aspecto | Detalle |
|---------|---------|
| URL | `/admin/reset-configuracion` |
| Metodo HTTP | POST |
| Parametros | clave |
| Efecto | Restaura valorDefault |

### 3.4 Reset de Todos los Parametros

| Aspecto | Detalle |
|---------|---------|
| URL | `/admin/reset-all-configuracion` |
| Metodo HTTP | POST |
| Efecto | Restaura todos los valorDefault |

### 3.5 Refrescar Cache

| Aspecto | Detalle |
|---------|---------|
| URL | `/admin/refresh-cache` |
| Metodo HTTP | POST |
| Efecto | Recarga cache en memoria desde BD |

---

## 4. Categorias de Configuracion

### 4.1 Seguridad - Login

| Clave | Tipo | Default | Descripcion |
|-------|------|---------|-------------|
| `security.login.max-attempts` | INTEGER | 5 | Intentos antes de bloqueo |
| `security.login.block-duration-minutes` | INTEGER | 15 | Minutos de bloqueo |
| `security.session.timeout-minutes` | INTEGER | 25 | Timeout de sesion |

**Integracion:** LoginAttemptService usa estos valores en tiempo real.

### 4.2 Seguridad - Password

| Clave | Tipo | Default | Descripcion |
|-------|------|---------|-------------|
| `security.password.min-length` | INTEGER | 8 | Longitud minima |
| `security.password.require-uppercase` | BOOLEAN | true | Requiere mayuscula |
| `security.password.require-lowercase` | BOOLEAN | true | Requiere minuscula |
| `security.password.require-digit` | BOOLEAN | true | Requiere numero |
| `security.password.require-special-char` | BOOLEAN | true | Requiere caracter especial |
| `security.password.special-characters` | STRING | !@#$%... | Caracteres permitidos |
| `security.password.expiration-days` | INTEGER | 180 | Dias hasta expiracion |
| `security.password.history-count` | INTEGER | 5 | Historial de passwords |

**Integracion:** PasswordValidator usa estos valores para validar contrasenas.

### 4.3 Lotes

| Clave | Tipo | Default | Descripcion |
|-------|------|---------|-------------|
| `lotes.alert-days-before-expiry` | INTEGER | 60 | Dias alerta vencimiento |
| `lotes.alert-days-before-reanalysis` | INTEGER | 30 | Dias alerta reanalisis |
| `lotes.max-genealogy-depth` | INTEGER | 100 | Profundidad maxima genealogia |
| `lotes.quantity-decimal-precision` | INTEGER | 4 | Decimales en cantidades |

### 4.4 Auditoria

| Clave | Tipo | Default | Descripcion |
|-------|------|---------|-------------|
| `audit.retention-years` | INTEGER | 7 | Anos retencion datos |
| `audit.log-retention-days` | INTEGER | 90 | Dias retencion logs |
| `audit.log-level` | ENUM | WARN | Nivel de log |
| `audit.log-sql-queries` | BOOLEAN | false | Loguear consultas SQL |
| `audit.log-read-operations` | BOOLEAN | false | Auditar operaciones lectura |

### 4.5 Backups

| Clave | Tipo | Default | Descripcion |
|-------|------|---------|-------------|
| `backup.frequency-hours` | INTEGER | 1 | Horas entre backups |
| `backup.retention-days` | INTEGER | 30 | Dias retencion |
| `backup.path` | PATH | /backups | Ruta de backups |
| `backup.before-critical-operations` | BOOLEAN | true | Backup pre-operacion |
| `backup.compression-enabled` | BOOLEAN | true | Comprimir backups |

### 4.6 UI

| Clave | Tipo | Default | Descripcion |
|-------|------|---------|-------------|
| `ui.records-per-page` | INTEGER | 25 | Registros por pagina |
| `ui.date-format` | STRING | dd/MM/yyyy | Formato de fecha |
| `ui.timezone` | STRING | America/Argentina/Buenos_Aires | Zona horaria |

### 4.7 Notificaciones (Fase Futura)

| Clave | Tipo | Default | Estado |
|-------|------|---------|--------|
| `notifications.enabled` | BOOLEAN | false | NO IMPLEMENTADO |
| `notifications.email` | EMAIL | - | NO IMPLEMENTADO |

---

## 5. Validaciones

### 5.1 Validaciones por Tipo

| Tipo | Validacion | Ejemplo |
|------|------------|---------|
| INTEGER | Rango min/max si definido | max-attempts: 1-10 |
| LONG | Rango min/max si definido | - |
| BOOLEAN | Solo "true" o "false" | require-uppercase: true |
| ENUM | Valor en lista de opciones | log-level: DEBUG,INFO,WARN,ERROR |
| EMAIL | Formato email valido | user@domain.com |
| STRING | Sin validacion especifica | - |
| PATH | Sin validacion especifica | /backups |
| URL | Sin validacion especifica | - |
| PASSWORD | Sin validacion especifica | - |

### 5.2 Restricciones

| Restriccion | Efecto |
|-------------|--------|
| editable=false | No permite modificacion |
| requiereReinicio=true | Muestra advertencia de reinicio |
| activo=false | No se muestra en UI |

### 5.3 Mensajes de Error

| Situacion | Mensaje |
|-----------|---------|
| Valor fuera de rango | "El valor debe estar entre {min} y {max}" |
| Boolean invalido | "El valor debe ser 'true' o 'false'" |
| Enum invalido | "El valor debe ser uno de: {opciones}" |
| Email invalido | "El formato de email no es valido" |
| No editable | "Esta configuracion no es editable" |

---

## 6. Flujos del Proceso

### 6.1 Flujo de Modificacion

```
[Usuario accede a /admin/configuracion]
       |
       v
[Sistema muestra configuraciones por categoria]
       |
       v
[Usuario modifica valor de un parametro]
       |
       v
[Usuario hace clic en Guardar]
       |
       v
[Sistema verifica editable=true]
       |
       +---> (No editable) ---> [Error: "Esta configuracion no es editable"]
       |
       v
[Sistema valida valor segun tipo]
       |
       +---> (Invalido) ---> [Muestra error especifico]
       |
       v
[Guarda nuevo valor en BD]
       |
       v
[Actualiza cache en memoria]
       |
       v
[Registra log: CONFIG_UPDATED]
       |
       v
[Muestra mensaje de exito]
       |
       +---> (requiereReinicio) ---> [Muestra: "Requiere reinicio para aplicar"]
```

### 6.2 Flujo de Reset Individual

```
[Usuario hace clic en Reset para un parametro]
       |
       v
[Sistema verifica editable=true]
       |
       v
[Restaura valorDefault]
       |
       v
[Actualiza cache]
       |
       v
[Registra log: CONFIG_RESET]
```

### 6.3 Flujo de Reset Total

```
[Usuario hace clic en "Restaurar todos los defaults"]
       |
       v
[Sistema confirma accion]
       |
       v
[Para cada configuracion editable:]
  - Restaura valorDefault
       |
       v
[Actualiza cache completo]
       |
       v
[Registra log: CONFIG_RESET_ALL]
```

---

## 7. Casos de Uso Ejemplos

### 7.1 Aumentar Intentos de Login

**Situacion:** Se quiere permitir mas intentos antes de bloquear.

**Accion:**
1. Acceder a `/admin/configuracion`
2. Buscar `security.login.max-attempts`
3. Cambiar de 5 a 10
4. Guardar

**Resultado:** LoginAttemptService ahora permite 10 intentos.

### 7.2 Cambiar Politica de Contrasenas

**Situacion:** Se requiere contrasenas mas largas por politica de seguridad.

**Accion:**
1. Acceder a `/admin/configuracion`
2. Buscar `security.password.min-length`
3. Cambiar de 8 a 12
4. Guardar

**Resultado:** Nuevas contrasenas deben tener minimo 12 caracteres.

### 7.3 Ajustar Alerta de Vencimiento

**Situacion:** Se quiere mas anticipacion para alertas de vencimiento.

**Accion:**
1. Acceder a `/admin/configuracion`
2. Buscar `lotes.alert-days-before-expiry`
3. Cambiar de 60 a 90
4. Guardar

**Resultado:** Alertas de vencimiento se mostraran 90 dias antes.

### 7.4 Restaurar Defaults de Seguridad

**Situacion:** Se modificaron politicas y se quiere volver a valores originales.

**Accion:**
1. Acceder a `/admin/configuracion`
2. Clic en "Restaurar todos los defaults"
3. Confirmar

**Resultado:** Todas las configuraciones vuelven a sus valores originales.

---

## 8. Preguntas Frecuentes

### 8.1 Sobre la Configuracion

**P: ¿Los cambios aplican inmediatamente?**
R: Si, el cache se actualiza al guardar. Algunas configuraciones con `requiereReinicio=true` requieren reiniciar la aplicacion.

**P: ¿Puedo agregar nuevas configuraciones desde la UI?**
R: No. Las configuraciones se definen en `ConfiguracionDataInitializer`. Agregar nuevas requiere modificar codigo.

**P: ¿Donde se almacenan las configuraciones?**
R: En la tabla `configuracion` de la base de datos.

### 8.2 Sobre el Cache

**P: ¿Que es el cache de configuracion?**
R: Un ConcurrentHashMap en memoria que evita consultas repetidas a la BD.

**P: ¿Cuando debo refrescar el cache?**
R: Normalmente no es necesario. Se actualiza automaticamente al guardar. Use "Refrescar cache" si sospecha inconsistencias.

### 8.3 Sobre Auditoria

**P: ¿Se registran los cambios de configuracion?**
R: Si. Cada cambio genera un log con: clave, valor anterior, valor nuevo, usuario, timestamp.

**P: ¿Donde veo el historial de cambios?**
R: En los logs de aplicacion (conitrack-app.log) y logs de auditoria.

### 8.4 Sobre Seguridad

**P: ¿Que pasa si configuro mal los intentos de login?**
R: Use "Restaurar todos los defaults" para volver a valores seguros.

**P: ¿Puedo desactivar el bloqueo de cuentas?**
R: Tecnicamente si (poniendo max-attempts muy alto), pero no se recomienda por seguridad.

---

## Resumen de Puntos Clave

1. **Tipo:** Administracion del sistema
2. **Usuario unico:** ADMIN
3. **Categorias:** 7 (security, lotes, audit, backup, ui, notifications, api)
4. **Parametros:** 28 configuraciones
5. **Validacion:** Tipada segun tipo de dato
6. **Cache:** En memoria con actualizacion automatica
7. **Auditoria:** Logs de todos los cambios
8. **Integracion:** LoginAttemptService, PasswordValidator

---

## Relacion con Otros CUs

### Servicios que Usan Configuracion

| Servicio | Configuraciones Usadas |
|----------|------------------------|
| LoginAttemptService | security.login.* |
| PasswordValidator | security.password.* |
| FechaValidatorService | lotes.alert-days-* |
| AuditorAccessLogger | audit.* |

### Documentacion Relacionada

| Documento | Contenido |
|-----------|-----------|
| CONFIGURACION-SISTEMA-ESTADO.md | Estado de implementacion detallado |
| CONITRACK-SEGURIDAD-VALIDACION-FORMAL.md | Validacion formal de seguridad |

---

**Fin del Documento - CU42_ABM_CONFIGURACION v1.0 - Especificacion Funcional**
