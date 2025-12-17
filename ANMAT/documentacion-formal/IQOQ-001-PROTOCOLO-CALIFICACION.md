# IQ/OQ-001: Protocolo de Calificacion
## Sistema Conitrack - Gestion de Stock Farmaceutico

---

**Codigo documento:** IQOQ-001
**Version:** 1.0
**Fecha:** 2025-12-16
**Estado:** Aprobado

---

## Control de Versiones

| Version | Fecha | Autor | Descripcion |
|---------|-------|-------|-------------|
| 1.0 | 2025-12-16 | Equipo QA | Version inicial aprobada |

---

## Firmas de Aprobacion

### Aprobacion del Protocolo (Pre-Ejecucion)

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| Elaborado por | _________________ | _________________ | ____/____/____ |
| Revisado por (QA) | _________________ | _________________ | ____/____/____ |
| Aprobado por (System Owner) | _________________ | _________________ | ____/____/____ |

### Aprobacion del Reporte (Post-Ejecucion)

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| Ejecutado por | _________________ | _________________ | ____/____/____ |
| Verificado por (QA) | _________________ | _________________ | ____/____/____ |
| Aprobado por (System Owner) | _________________ | _________________ | ____/____/____ |

---

## Indice de Contenidos

1. [Objetivo](#1-objetivo)
2. [Alcance](#2-alcance)
3. [Responsabilidades](#3-responsabilidades)
4. [Documentos de Referencia](#4-documentos-de-referencia)
5. [Prerrequisitos](#5-prerrequisitos)
6. [Calificacion de Instalacion (IQ)](#6-calificacion-de-instalacion-iq)
7. [Calificacion Operacional (OQ)](#7-calificacion-operacional-oq)
8. [Registro de Desviaciones](#8-registro-de-desviaciones)
9. [Resumen de Resultados](#9-resumen-de-resultados)
10. [Conclusion](#10-conclusion)
11. [Anexos](#11-anexos)

---

## 1. Objetivo

Este protocolo tiene como objetivo demostrar que el sistema Conitrack:

1. **IQ (Installation Qualification):** Esta correctamente instalado con los componentes y versiones especificadas
2. **OQ (Operational Qualification):** Opera de acuerdo a las especificaciones funcionales documentadas en ERU-001

**Referencia regulatoria:**
> "Debe demostrarse con evidencias que los metodos y los escenarios de test son adecuados." - ANMAT Anexo 6, Requisito 4.7

---

## 2. Alcance

**Sistema:** Conitrack v1.0
**Ambiente:** _________________________ (Produccion / Staging)
**URL:** _________________________
**Fecha de ejecucion:** ____/____/____

**Incluye:**
- Verificacion de instalacion de componentes (IQ)
- Pruebas de funcionalidades criticas (OQ)
- Pruebas de seguridad y control de acceso
- Pruebas de audit trail
- Pruebas de backup y recuperacion

**Excluye:**
- Pruebas de carga/estres (no requerido para sistema GAMP 5 baja criticidad)
- Pruebas de penetracion (fuera de alcance)
- Calificacion de infraestructura fisica (servidor, red)

---

## 3. Responsabilidades

| Rol | Responsabilidad |
|-----|-----------------|
| **Ejecutor** | Ejecutar tests segun procedimiento, registrar resultados, capturar evidencia |
| **QA** | Verificar ejecucion correcta, revisar evidencia, aprobar resultados |
| **System Owner** | Aprobar protocolo pre-ejecucion y reporte post-ejecucion |
| **IT** | Proveer acceso al sistema, soporte tecnico durante ejecucion |

---

## 4. Documentos de Referencia

| Documento | Codigo | Version |
|-----------|--------|---------|
| Especificaciones de Requerimientos de Usuario | ERU-001 | 1.0 |
| ANMAT Disposicion 4159/2023 | Anexo 6 | - |
| SOP de Backup y Recuperacion | SOP-001 | 1.0 |
| Manual de Usuario | MAN-001 | 1.0 |

---

## 5. Prerrequisitos

Antes de iniciar la ejecucion, verificar y marcar:

- [ ] Sistema desplegado en ambiente de calificacion
- [ ] ERU-001 aprobado y disponible
- [ ] Protocolo aprobado (firmas en Seccion de Aprobacion Pre-Ejecucion)
- [ ] Acceso disponible con credenciales de prueba para todos los roles
- [ ] Herramienta de captura de pantalla disponible
- [ ] Reloj del sistema sincronizado (verificar fecha/hora)
- [ ] Base de datos con datos de prueba cargados
- [ ] Backup reciente disponible para test de restore

**Credenciales de prueba disponibles:**

| Rol | Usuario | Password | Verificado |
|-----|---------|----------|------------|
| ADMIN | __________ | __________ | [ ] |
| DT | __________ | __________ | [ ] |
| GERENTE_CONTROL_CALIDAD | __________ | __________ | [ ] |
| ANALISTA_PLANTA | __________ | __________ | [ ] |
| AUDITOR | __________ | __________ | [ ] |

---

## 6. Calificacion de Instalacion (IQ)

### IQ-001: Verificacion de Version de Software

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que las versiones de software instaladas coinciden con las especificadas |
| **Referencia** | ERU-001 Seccion 2.1 |

**Procedimiento:**

1. Acceder al servidor/contenedor de aplicacion
2. Ejecutar comandos para verificar versiones

**Comandos a ejecutar:**
```bash
# Version de Java
java -version

# Version de PostgreSQL (en contenedor)
docker exec postgres-db psql -V

# Version de Spring Boot (en logs de inicio)
docker logs spring-app 2>&1 | grep "Spring Boot"
```

**Resultados:**

| Componente | Version Esperada | Version Encontrada | Resultado |
|------------|------------------|-------------------|-----------|
| Java JDK | 17.x | _________________ | [ ] PASS [ ] FAIL |
| Spring Boot | 3.4.1 | _________________ | [ ] PASS [ ] FAIL |
| PostgreSQL | 17.x | _________________ | [ ] PASS [ ] FAIL |

**Evidencia:** Adjuntar screenshot de comandos ejecutados (Anexo 11.1, Evidencia IQ-001)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

### IQ-002: Verificacion de Base de Datos

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que la base de datos tiene las tablas requeridas |
| **Referencia** | ERU-001 Seccion 5.3 |

**Procedimiento:**

1. Conectar a la base de datos
2. Listar tablas existentes
3. Verificar tablas criticas

**Comando:**
```bash
docker exec postgres-db psql -U postgres -d conitrack -c "\dt"
```

**Resultados:**

| Tabla | Existe (Si/No) | Resultado |
|-------|----------------|-----------|
| lote | ______ | [ ] PASS [ ] FAIL |
| movimiento | ______ | [ ] PASS [ ] FAIL |
| analisis | ______ | [ ] PASS [ ] FAIL |
| bulto | ______ | [ ] PASS [ ] FAIL |
| traza | ______ | [ ] PASS [ ] FAIL |
| users | ______ | [ ] PASS [ ] FAIL |
| roles | ______ | [ ] PASS [ ] FAIL |
| producto | ______ | [ ] PASS [ ] FAIL |
| proveedor | ______ | [ ] PASS [ ] FAIL |
| auditoria_cambios | ______ | [ ] PASS [ ] FAIL |
| auditoria_acceso | ______ | [ ] PASS [ ] FAIL |
| configuracion | ______ | [ ] PASS [ ] FAIL |

**Evidencia:** Adjuntar screenshot de listado de tablas (Anexo 11.1, Evidencia IQ-002)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

### IQ-003: Verificacion de Configuracion de Produccion

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que la configuracion de produccion es correcta |
| **Referencia** | ERU-001 Seccion 4.1 |

**Procedimiento:**

1. Revisar perfil activo de Spring
2. Verificar configuraciones de seguridad

**Resultados:**

| Configuracion | Valor Esperado | Valor Encontrado | Resultado |
|---------------|----------------|------------------|-----------|
| Perfil activo | PROD | _________________ | [ ] PASS [ ] FAIL |
| spring.jpa.show-sql | false | _________________ | [ ] PASS [ ] FAIL |
| Hibernate ddl-auto | validate | _________________ | [ ] PASS [ ] FAIL |

**Evidencia:** Adjuntar screenshot de configuracion (Anexo 11.1, Evidencia IQ-003)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

### IQ-004: Verificacion de Conectividad

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que el sistema responde a peticiones HTTP |
| **Referencia** | ERU-001 Seccion 5.4 |

**Procedimiento:**

1. Acceder a URL del sistema desde navegador
2. Verificar que pagina de login carga correctamente
3. Verificar HTTPS (en produccion)

**Resultados:**

| Verificacion | Esperado | Observado | Resultado |
|--------------|----------|-----------|-----------|
| URL accesible | Pagina login visible | _________________ | [ ] PASS [ ] FAIL |
| HTTPS activo | Candado verde/certificado valido | _________________ | [ ] PASS [ ] FAIL |
| Tiempo de carga | < 3 segundos | _______ segundos | [ ] PASS [ ] FAIL |

**Evidencia:** Adjuntar screenshot de pagina de login (Anexo 11.1, Evidencia IQ-004)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

### IQ-005: Verificacion de Usuarios y Roles

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que existen usuarios con los roles definidos |
| **Referencia** | ERU-001 Seccion 2.3 |

**Procedimiento:**

1. Consultar tabla de usuarios en base de datos o acceder como ADMIN
2. Verificar existencia de usuarios por rol

**Resultados:**

| Rol | Usuario Existe | Username | Resultado |
|-----|----------------|----------|-----------|
| ADMIN | ______ | _________________ | [ ] PASS [ ] FAIL |
| DT | ______ | _________________ | [ ] PASS [ ] FAIL |
| GERENTE_GARANTIA_CALIDAD | ______ | _________________ | [ ] PASS [ ] FAIL |
| GERENTE_CONTROL_CALIDAD | ______ | _________________ | [ ] PASS [ ] FAIL |
| ANALISTA_CONTROL_CALIDAD | ______ | _________________ | [ ] PASS [ ] FAIL |
| SUPERVISOR_PLANTA | ______ | _________________ | [ ] PASS [ ] FAIL |
| ANALISTA_PLANTA | ______ | _________________ | [ ] PASS [ ] FAIL |
| AUDITOR | ______ | _________________ | [ ] PASS [ ] FAIL |

**Evidencia:** Adjuntar screenshot de listado de usuarios (Anexo 11.1, Evidencia IQ-005)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

## 7. Calificacion Operacional (OQ)

### 7.1 Control de Acceso

#### TC-001: Login Exitoso

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que usuario puede autenticarse con credenciales validas |
| **Referencia** | RF-041 |
| **Prerrequisitos** | Usuario de prueba creado y activo |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Acceder a pagina de login | Se muestra formulario con campos usuario y contrasena | | [ ] |
| 2 | Ingresar usuario valido | Campo acepta entrada | | [ ] |
| 3 | Ingresar contrasena correcta | Campo acepta entrada (caracteres ocultos) | | [ ] |
| 4 | Click en "Iniciar Sesion" | Redirige a pagina principal | | [ ] |
| 5 | Verificar nombre en header | Muestra nombre del usuario logueado | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots de pasos 1, 4, 5 (Anexo 11.1, Evidencia TC-001)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-002: Login Fallido - Credenciales Invalidas

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que sistema rechaza credenciales invalidas |
| **Referencia** | RF-041 |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Acceder a pagina de login | Se muestra formulario | | [ ] |
| 2 | Ingresar usuario valido | Campo acepta entrada | | [ ] |
| 3 | Ingresar contrasena INCORRECTA | Campo acepta entrada | | [ ] |
| 4 | Click en "Iniciar Sesion" | Muestra mensaje de error generico | | [ ] |
| 5 | Verificar que permanece en login | No hay acceso al sistema | | [ ] |

**Verificar:** El mensaje de error NO revela si el usuario o la contrasena es incorrecta (mensaje generico)

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshot de mensaje de error (Anexo 11.1, Evidencia TC-002)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-003: Bloqueo por Intentos Fallidos

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar bloqueo de cuenta tras 5 intentos fallidos |
| **Referencia** | RF-043, RNF-002 |
| **Prerrequisitos** | Usuario de prueba sin intentos fallidos previos |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Intento 1: Login con contrasena incorrecta | Error de credenciales | | [ ] |
| 2 | Intento 2: Login con contrasena incorrecta | Error de credenciales | | [ ] |
| 3 | Intento 3: Login con contrasena incorrecta | Error de credenciales | | [ ] |
| 4 | Intento 4: Login con contrasena incorrecta | Error de credenciales | | [ ] |
| 5 | Intento 5: Login con contrasena incorrecta | Error de credenciales | | [ ] |
| 6 | Intento 6: Login con contrasena CORRECTA | Cuenta bloqueada, acceso denegado | | [ ] |
| 7 | Esperar 15 minutos | - | | [ ] |
| 8 | Intento 7: Login con contrasena correcta | Acceso exitoso (cuenta desbloqueada) | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots de pasos 5, 6, 8 (Anexo 11.1, Evidencia TC-003)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-004: Control de Acceso por Rol - AUDITOR Solo Lectura

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que rol AUDITOR no puede modificar datos |
| **Referencia** | RF-042, RF-054 |
| **Prerrequisitos** | Usuario con rol AUDITOR |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Login como AUDITOR | Acceso exitoso | | [ ] |
| 2 | Navegar a listado de lotes | Ve lista de lotes (solo lectura) | | [ ] |
| 3 | Buscar opcion "Nuevo Lote" o "Alta" | Opcion NO visible o acceso denegado | | [ ] |
| 4 | Intentar acceder via URL directa a /compras/alta | Error 403 Acceso Denegado | | [ ] |
| 5 | Verificar menu | Solo opciones de consulta visibles | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots de pasos 2, 4, 5 (Anexo 11.1, Evidencia TC-004)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-005: Gestion de Usuarios - Crear Usuario

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que ADMIN puede crear nuevos usuarios |
| **Referencia** | RF-071 |
| **Prerrequisitos** | Login como ADMIN |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Login como ADMIN | Acceso exitoso | | [ ] |
| 2 | Navegar a Admin > Usuarios | Lista de usuarios visible | | [ ] |
| 3 | Click en "Nuevo Usuario" | Formulario de creacion | | [ ] |
| 4 | Completar: username, nombre, email, rol | Campos aceptan datos | | [ ] |
| 5 | Guardar | Usuario creado, mensaje exito | | [ ] |
| 6 | Buscar usuario en listado | Usuario aparece en lista | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots de pasos 3, 5, 6 (Anexo 11.1, Evidencia TC-005)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

### 7.2 Gestion de Lotes

#### TC-011: Alta de Lote por Compra

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar creacion de lote por ingreso de compra |
| **Referencia** | RF-001 |
| **Prerrequisitos** | Login como ANALISTA_PLANTA, producto y proveedor existentes |

**Datos de prueba:**
- Producto: _________________ (seleccionar uno existente)
- Numero de lote: TEST-IQOQ-001
- Cantidad: 100
- Unidad: KILOGRAMO
- Proveedor: _________________ (seleccionar uno existente)
- Fecha vencimiento: (fecha futura)
- Motivo: "Ingreso de mercaderia por orden de compra OC-2025-001 para reposicion de stock"

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Navegar a Compras > Alta Ingreso | Se muestra formulario | | [ ] |
| 2 | Seleccionar producto | Campo poblado | | [ ] |
| 3 | Ingresar numero de lote: TEST-IQOQ-001 | Campo acepta entrada | | [ ] |
| 4 | Ingresar cantidad: 100 | Campo acepta numero | | [ ] |
| 5 | Seleccionar unidad: KILOGRAMO | Campo poblado | | [ ] |
| 6 | Seleccionar proveedor | Campo poblado | | [ ] |
| 7 | Ingresar fecha de vencimiento | Campo acepta fecha | | [ ] |
| 8 | Ingresar motivo (>20 chars) | Campo acepta texto | | [ ] |
| 9 | Click "Guardar" | Mensaje de exito | | [ ] |
| 10 | Buscar lote en listado | Lote visible con estado NUEVO y dictamen RECIBIDO | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots de formulario completo y listado (Anexo 11.1, Evidencia TC-011)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-012: Alta de Lote por Produccion

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar creacion de lote producido internamente |
| **Referencia** | RF-002 |
| **Prerrequisitos** | Login como SUPERVISOR_PLANTA |

**Datos de prueba:**
- Producto: _________________ (producto terminado)
- Numero de lote: PROD-IQOQ-001
- Cantidad: 50
- Motivo: "Produccion del lote segun orden de produccion OP-2025-001"

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Navegar a Produccion > Alta Lote | Se muestra formulario | | [ ] |
| 2 | Completar datos del lote | Campos aceptan datos | | [ ] |
| 3 | Ingresar motivo (>20 chars) | Campo acepta texto | | [ ] |
| 4 | Click "Guardar" | Mensaje de exito | | [ ] |
| 5 | Verificar lote creado | Estado NUEVO, dictamen RECIBIDO | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots (Anexo 11.1, Evidencia TC-012)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-013: Consulta de Lotes

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar funcionalidad de consulta y filtrado de lotes |
| **Referencia** | RF-003 |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Navegar a Lotes > Listado | Lista de lotes visible | | [ ] |
| 2 | Verificar columnas | Codigo, Producto, Nro Lote, Cantidad, Estado, Dictamen, Vencimiento | | [ ] |
| 3 | Filtrar por producto | Solo lotes del producto seleccionado | | [ ] |
| 4 | Filtrar por estado | Solo lotes con estado filtrado | | [ ] |
| 5 | Click en un lote | Navega a detalle del lote | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots (Anexo 11.1, Evidencia TC-013)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

### 7.3 Gestion de Movimientos

#### TC-021: Egreso por Venta

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar registro de egreso de stock por venta |
| **Referencia** | RF-011 |
| **Prerrequisitos** | Lote con dictamen LIBERADO y cantidad disponible |

**Datos de prueba:**
- Lote: _________________ (lote aprobado)
- Cantidad a egresar: 10
- Motivo: "Venta a cliente XYZ segun pedido PED-2025-001"

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Navegar a Ventas > Egreso | Se muestra formulario | | [ ] |
| 2 | Seleccionar lote con dictamen LIBERADO | Lote seleccionado, cantidad disponible visible | | [ ] |
| 3 | Ingresar cantidad: 10 | Campo acepta cantidad <= disponible | | [ ] |
| 4 | Ingresar motivo (>20 chars) | Campo acepta texto | | [ ] |
| 5 | Click "Guardar" | Mensaje de exito | | [ ] |
| 6 | Verificar lote | Cantidad reducida en 10 unidades | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots (Anexo 11.1, Evidencia TC-021)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-022: Reverso de Movimiento

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar funcionalidad de reverso de movimiento |
| **Referencia** | RF-016 |
| **Prerrequisitos** | Movimiento previo registrado por el mismo usuario |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Navegar a Contingencias > Reverso | Lista de movimientos reversibles | | [ ] |
| 2 | Seleccionar movimiento a revertir | Movimiento seleccionado | | [ ] |
| 3 | Ingresar motivo (>20 chars) | Campo acepta texto | | [ ] |
| 4 | Click "Revertir" | Mensaje de exito | | [ ] |
| 5 | Verificar lote | Cantidad restaurada | | [ ] |
| 6 | Verificar movimientos | Nuevo movimiento de reverso creado | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots (Anexo 11.1, Evidencia TC-022)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-023: Resultado de Analisis

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar registro de resultado de analisis |
| **Referencia** | RF-021 |
| **Prerrequisitos** | Lote con dictamen CUARENTENA, login como rol de calidad |

**Datos de prueba:**
- Lote: TEST-IQOQ-001 (creado en TC-011)
- Numero analisis: AN-2025-001
- Resultado: APROBADO
- Motivo: "Analisis fisicoquimico completo segun protocolo AN-001, todos los parametros dentro de especificacion"

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Navegar a Calidad > Resultado Analisis | Formulario visible | | [ ] |
| 2 | Seleccionar lote con dictamen CUARENTENA | Lote seleccionado | | [ ] |
| 3 | Ingresar numero analisis | Campo acepta texto | | [ ] |
| 4 | Seleccionar resultado: APROBADO | Campo poblado | | [ ] |
| 5 | Ingresar fecha analisis | Campo acepta fecha | | [ ] |
| 6 | Ingresar motivo (>20 chars) | Campo acepta texto | | [ ] |
| 7 | Click "Guardar" | Mensaje de exito | | [ ] |
| 8 | Verificar lote | Dictamen cambia a APROBADO | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots (Anexo 11.1, Evidencia TC-023)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-024: Registro de Traza

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar registro de trazabilidad entre lotes |
| **Referencia** | RF-031 |
| **Prerrequisitos** | Lote de materia prima y lote producido |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Navegar a Produccion > Trazado | Formulario visible | | [ ] |
| 2 | Seleccionar lote producido (destino) | Lote seleccionado | | [ ] |
| 3 | Seleccionar lote de MP (origen) | Lote agregado a lista | | [ ] |
| 4 | Ingresar cantidad utilizada | Campo acepta cantidad | | [ ] |
| 5 | Ingresar motivo | Campo acepta texto | | [ ] |
| 6 | Click "Guardar" | Mensaje de exito | | [ ] |
| 7 | Verificar detalle de lote destino | Muestra traza con lote origen | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots (Anexo 11.1, Evidencia TC-024)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-025: Retiro de Mercado (Recall)

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar funcionalidad de recall |
| **Referencia** | RF-061 |
| **Prerrequisitos** | Login como ADMIN o DT, lote activo |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Navegar a Contingencias > Recall | Formulario visible | | [ ] |
| 2 | Seleccionar lote(s) a retirar | Lote(s) seleccionado(s) | | [ ] |
| 3 | Ingresar motivo detallado | Campo acepta texto | | [ ] |
| 4 | Click "Aplicar Recall" | Mensaje de exito | | [ ] |
| 5 | Verificar lote | Dictamen cambia a RETIRO_MERCADO | | [ ] |
| 6 | Intentar venta del lote | Operacion bloqueada | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots (Anexo 11.1, Evidencia TC-025)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

### 7.4 Audit Trail

#### TC-031: Registro de Cambio en Audit Trail

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que cambios se registran en audit trail con motivo |
| **Referencia** | RF-051, RNF-026 |
| **Prerrequisitos** | Lote existente para modificar |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Seleccionar lote para modificar | Lote seleccionado | | [ ] |
| 2 | Realizar un cambio (ej: resultado analisis) | Formulario de cambio | | [ ] |
| 3 | Ingresar motivo (>20 chars) | Campo acepta texto | | [ ] |
| 4 | Guardar cambios | Mensaje de exito | | [ ] |
| 5 | Acceder a historial del lote | Lista de cambios visible | | [ ] |
| 6 | Verificar registro | Muestra: usuario, fecha, campo, antes, despues, motivo | | [ ] |

**Verificar que el registro contiene:**
- [ ] Usuario que realizo el cambio
- [ ] Fecha y hora del cambio
- [ ] Campo modificado
- [ ] Valor anterior
- [ ] Valor nuevo
- [ ] Motivo del cambio

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshot de historial de cambios (Anexo 11.1, Evidencia TC-031)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-032: Motivo de Cambio Obligatorio

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que sistema no permite cambios sin motivo valido |
| **Referencia** | RF-053 |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Iniciar operacion de modificacion | Formulario visible | | [ ] |
| 2 | Completar datos | Datos ingresados | | [ ] |
| 3 | Dejar campo motivo VACIO | Campo vacio | | [ ] |
| 4 | Intentar guardar | Error: motivo obligatorio | | [ ] |
| 5 | Ingresar motivo corto (5 chars) | "Ajust" | | [ ] |
| 6 | Intentar guardar | Error: motivo muy corto (min 20) | | [ ] |
| 7 | Ingresar motivo valido (>20 chars) | Motivo completo | | [ ] |
| 8 | Guardar | Operacion exitosa | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots de mensajes de error (Anexo 11.1, Evidencia TC-032)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-033: Audit Trail No Modificable

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que registros de audit trail no pueden modificarse ni eliminarse |
| **Referencia** | RF-051, RNF-028 |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Acceder a historial de cambios de un lote | Lista de cambios visible | | [ ] |
| 2 | Buscar opciones de editar | NO hay botones de edicion | | [ ] |
| 3 | Buscar opciones de eliminar | NO hay botones de eliminacion | | [ ] |
| 4 | Verificar interfaz | Solo lectura, sin acciones de modificacion | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshot mostrando ausencia de controles de edicion (Anexo 11.1, Evidencia TC-033)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

### 7.5 Seguridad

#### TC-041: Politica de Contrasenas - Complejidad

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que sistema exige contrasenas complejas |
| **Referencia** | RNF-001 |
| **Prerrequisitos** | Acceso a cambio de contrasena |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Acceder a cambio de contrasena | Formulario visible | | [ ] |
| 2 | Intentar: "abc" | Error: muy corta (min 8) | | [ ] |
| 3 | Intentar: "abcdefgh" | Error: falta mayuscula y numero | | [ ] |
| 4 | Intentar: "ABCDEFGH" | Error: falta minuscula y numero | | [ ] |
| 5 | Intentar: "Abcdefgh" | Error: falta numero | | [ ] |
| 6 | Intentar: "Abcdefg1" | Aceptada (cumple todos requisitos) | | [ ] |

**Requisitos verificados:**
- [ ] Minimo 8 caracteres
- [ ] Al menos 1 mayuscula
- [ ] Al menos 1 minuscula
- [ ] Al menos 1 numero

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshots de mensajes de validacion (Anexo 11.1, Evidencia TC-041)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-042: Timeout de Sesion

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que sesion expira por inactividad |
| **Referencia** | RF-047, RNF-003 |

**Nota:** En ambiente PROD, timeout es 30 minutos. En DEV es 5 horas. Verificar segun ambiente.

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Login al sistema | Sesion activa | | [ ] |
| 2 | Esperar tiempo de timeout | - | | [ ] |
| 3 | Intentar navegar | Redirige a login | | [ ] |
| 4 | Verificar mensaje | Indica sesion expirada | | [ ] |

**Resultado Final:** [ ] PASS [ ] FAIL [ ] N/A (si timeout muy largo para prueba)

**Evidencia:** Screenshot de redireccion a login (Anexo 11.1, Evidencia TC-042)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

### 7.6 Backup y Recuperacion

#### TC-051: Existencia de Backup Configurado

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que existe backup automatico configurado |
| **Referencia** | RNF-016, SOP-001 |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Verificar configuracion de backup | Cron job o schedule configurado | | [ ] |
| 2 | Listar archivos de backup | Al menos 1 backup existente | | [ ] |
| 3 | Verificar fecha de ultimo backup | Dentro de periodo esperado (< 7 dias) | | [ ] |
| 4 | Verificar tamano de backup | > 1 MB (indica datos reales) | | [ ] |

**Datos de backup encontrado:**
- Archivo: _________________________________
- Fecha: ____/____/____
- Tamano: _______ MB

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshot de configuracion y listado (Anexo 11.1, Evidencia TC-051)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

#### TC-052: Prueba de Restore

| Campo | Valor |
|-------|-------|
| **Objetivo** | Verificar que backup puede restaurarse correctamente |
| **Referencia** | RNF-017, SOP-001 |

**Procedimiento:**

| Paso | Accion | Resultado Esperado | Resultado Obtenido | OK |
|------|--------|-------------------|-------------------|-----|
| 1 | Seleccionar backup de prueba | Archivo identificado | | [ ] |
| 2 | Crear BD de prueba (conitrack_test) | BD creada | | [ ] |
| 3 | Ejecutar restore a BD de prueba | Restore sin errores | | [ ] |
| 4 | Contar registros en tabla lote | Conteo > 0 | | [ ] |
| 5 | Contar registros en tabla movimiento | Conteo > 0 | | [ ] |
| 6 | Contar registros en tabla users | Conteo > 0 | | [ ] |
| 7 | Eliminar BD de prueba | Limpieza completada | | [ ] |

**Conteos obtenidos:**
- Tabla lote: _______ registros
- Tabla movimiento: _______ registros
- Tabla users: _______ registros

**Tiempo de restore:** _______ minutos

**Resultado Final:** [ ] PASS [ ] FAIL

**Evidencia:** Screenshot de comandos y conteos (Anexo 11.1, Evidencia TC-052)

**Ejecutado por:** _________________ **Fecha:** ____/____/____ **Hora:** ____:____

---

## 8. Registro de Desviaciones

Registrar cualquier desviacion encontrada durante la ejecucion:

| ID | Test | Descripcion de Desviacion | Criticidad | Accion Correctiva | Estado |
|----|------|---------------------------|------------|-------------------|--------|
| DEV-001 | | | [ ] Critica [ ] Mayor [ ] Menor | | [ ] Abierta [ ] Cerrada |
| DEV-002 | | | [ ] Critica [ ] Mayor [ ] Menor | | [ ] Abierta [ ] Cerrada |
| DEV-003 | | | [ ] Critica [ ] Mayor [ ] Menor | | [ ] Abierta [ ] Cerrada |
| DEV-004 | | | [ ] Critica [ ] Mayor [ ] Menor | | [ ] Abierta [ ] Cerrada |
| DEV-005 | | | [ ] Critica [ ] Mayor [ ] Menor | | [ ] Abierta [ ] Cerrada |

**Criterios de criticidad:**
- **Critica:** Afecta integridad de datos, seguridad, o cumplimiento regulatorio. Bloquea aprobacion.
- **Mayor:** Afecta funcionalidad importante pero hay workaround. Requiere plan de remediacion.
- **Menor:** Afecta usabilidad o estetica. No bloquea aprobacion.

---

## 9. Resumen de Resultados

### 9.1 Calificacion de Instalacion (IQ)

| Test | Descripcion | Resultado |
|------|-------------|-----------|
| IQ-001 | Verificacion de versiones | [ ] PASS [ ] FAIL |
| IQ-002 | Verificacion de base de datos | [ ] PASS [ ] FAIL |
| IQ-003 | Verificacion de configuracion | [ ] PASS [ ] FAIL |
| IQ-004 | Verificacion de conectividad | [ ] PASS [ ] FAIL |
| IQ-005 | Verificacion de usuarios | [ ] PASS [ ] FAIL |

**Total IQ:** _____ / 5 PASS

### 9.2 Calificacion Operacional (OQ)

| Categoria | Tests | PASS | FAIL | N/A |
|-----------|-------|------|------|-----|
| Control de Acceso | TC-001 a TC-005 | | | |
| Gestion de Lotes | TC-011 a TC-013 | | | |
| Gestion de Movimientos | TC-021 a TC-025 | | | |
| Audit Trail | TC-031 a TC-033 | | | |
| Seguridad | TC-041 a TC-042 | | | |
| Backup y Recuperacion | TC-051 a TC-052 | | | |

**Total OQ:** _____ / 20 PASS

### 9.3 Resumen de Desviaciones

| Criticidad | Cantidad Total | Cerradas | Abiertas |
|------------|----------------|----------|----------|
| Criticas | | | |
| Mayores | | | |
| Menores | | | |

---

## 10. Conclusion

Basado en los resultados de la ejecucion del protocolo IQ/OQ:

### Resultado de IQ:
- [ ] La instalacion del sistema Conitrack v1.0 **CUMPLE** con las especificaciones
- [ ] La instalacion **NO CUMPLE** - Ver desviaciones

### Resultado de OQ:
- [ ] El sistema Conitrack v1.0 **OPERA** de acuerdo a ERU-001
- [ ] El sistema **NO OPERA** correctamente - Ver desviaciones

### Recomendacion Final:

- [ ] Sistema **APROBADO** para uso en produccion
- [ ] Sistema **APROBADO CON CONDICIONES** (ver desviaciones abiertas y plan de remediacion)
- [ ] Sistema **NO APROBADO** - Requiere correccion y re-ejecucion

### Comentarios:

_____________________________________________________________________________

_____________________________________________________________________________

_____________________________________________________________________________

---

## 11. Anexos

### 11.1 Evidencia de Ejecucion (Screenshots)

Adjuntar screenshots numerados correspondientes a cada test.

| Evidencia ID | Test | Descripcion | Pagina |
|--------------|------|-------------|--------|
| Evidencia IQ-001 | IQ-001 | Versiones de software | |
| Evidencia IQ-002 | IQ-002 | Listado de tablas | |
| Evidencia IQ-003 | IQ-003 | Configuracion | |
| Evidencia IQ-004 | IQ-004 | Pagina de login | |
| Evidencia IQ-005 | IQ-005 | Listado de usuarios | |
| Evidencia TC-001 | TC-001 | Login exitoso | |
| Evidencia TC-002 | TC-002 | Error de login | |
| Evidencia TC-003 | TC-003 | Bloqueo de cuenta | |
| Evidencia TC-004 | TC-004 | Acceso AUDITOR | |
| Evidencia TC-005 | TC-005 | Creacion usuario | |
| Evidencia TC-011 | TC-011 | Alta lote compra | |
| Evidencia TC-012 | TC-012 | Alta lote produccion | |
| Evidencia TC-013 | TC-013 | Consulta lotes | |
| Evidencia TC-021 | TC-021 | Egreso venta | |
| Evidencia TC-022 | TC-022 | Reverso movimiento | |
| Evidencia TC-023 | TC-023 | Resultado analisis | |
| Evidencia TC-024 | TC-024 | Registro traza | |
| Evidencia TC-025 | TC-025 | Recall | |
| Evidencia TC-031 | TC-031 | Audit trail | |
| Evidencia TC-032 | TC-032 | Motivo obligatorio | |
| Evidencia TC-033 | TC-033 | Audit no modificable | |
| Evidencia TC-041 | TC-041 | Password policy | |
| Evidencia TC-042 | TC-042 | Timeout sesion | |
| Evidencia TC-051 | TC-051 | Backup configurado | |
| Evidencia TC-052 | TC-052 | Prueba restore | |

### 11.2 Matriz de Trazabilidad Completa

| Requerimiento (ERU) | Test (IQOQ) | Resultado |
|---------------------|-------------|-----------|
| RF-001 | TC-011 | |
| RF-002 | TC-012 | |
| RF-003 | TC-013 | |
| RF-011 | TC-021 | |
| RF-016 | TC-022 | |
| RF-021 | TC-023 | |
| RF-031 | TC-024 | |
| RF-041 | TC-001, TC-002 | |
| RF-042 | TC-004 | |
| RF-043 | TC-003 | |
| RF-051 | TC-031 | |
| RF-053 | TC-032 | |
| RF-061 | TC-025 | |
| RF-071 | TC-005 | |
| RNF-001 | TC-041 | |
| RNF-002 | TC-003 | |
| RNF-003 | TC-042 | |
| RNF-016 | TC-051 | |
| RNF-017 | TC-052 | |
| RNF-026 | TC-031 | |
| RNF-028 | TC-033 | |

---

**Fin del documento**

---

**Aprobaciones finales:**

| | Firma | Fecha |
|---|-------|-------|
| **Ejecutado por:** | _________________ | ____/____/____ |
| **Verificado por (QA):** | _________________ | ____/____/____ |
| **Aprobado por (System Owner):** | _________________ | ____/____/____ |
