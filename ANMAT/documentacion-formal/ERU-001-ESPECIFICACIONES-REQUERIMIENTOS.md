# ERU-001: Especificaciones de Requerimientos de Usuario
## Sistema Conitrack - Gestion de Stock Farmaceutico

---

**Codigo documento:** ERU-001
**Version:** 1.0
**Fecha:** 2025-12-16
**Estado:** Aprobado

---

## Control de Versiones

| Version | Fecha | Autor | Descripcion |
|---------|-------|-------|-------------|
| 1.0 | 2025-12-16 | Equipo Desarrollo | Version inicial aprobada |

---

## Firmas de Aprobacion

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| Elaborado por | _________________ | _________________ | ____/____/____ |
| Revisado por (QA) | _________________ | _________________ | ____/____/____ |
| Aprobado por (System Owner) | _________________ | _________________ | ____/____/____ |

---

## Indice de Contenidos

1. [Introduccion](#1-introduccion)
2. [Descripcion General del Sistema](#2-descripcion-general-del-sistema)
3. [Requerimientos Funcionales](#3-requerimientos-funcionales)
4. [Requerimientos No Funcionales](#4-requerimientos-no-funcionales)
5. [Interfaces del Sistema](#5-interfaces-del-sistema)
6. [Matriz de Trazabilidad](#6-matriz-de-trazabilidad)
7. [Anexos](#7-anexos)

---

## 1. Introduccion

### 1.1 Proposito del documento

Este documento define las Especificaciones de Requerimientos de Usuario (ERU) para el sistema Conitrack, conforme a los lineamientos de ANMAT Disposicion 4159/2023 Anexo 6 e ISPE GAMP 5. Su proposito es:

- Documentar los requerimientos funcionales y no funcionales del sistema
- Servir como base para la validacion del sistema (IQ/OQ)
- Proporcionar trazabilidad entre requerimientos y tests de calificacion
- Cumplir con el requisito 4.4 de ANMAT Anexo 6: "Las especificaciones de requerimientos de usuario deben describir las funciones requeridas del sistema informatizado"

### 1.2 Alcance

**Nombre del sistema:** Conitrack
**Version:** 1.0
**Clasificacion GAMP:** Categoria 5 - Software Custom de Baja Criticidad

**El sistema INCLUYE:**
- Gestion de lotes de productos farmaceuticos (materias primas e insumos)
- Registro de movimientos de stock (ingresos, egresos, ajustes)
- Registro de resultados de analisis de calidad
- Trazabilidad de lotes en produccion
- Control de acceso basado en roles
- Audit trail de cambios con motivo obligatorio
- Gestion de bultos/paquetes individuales

**El sistema NO INCLUYE:**
- Liberacion formal de lotes (proceso externo al sistema)
- Ejecucion de analisis de laboratorio (solo registro de resultados)
- Facturacion y contabilidad
- Gestion de ordenes de produccion
- Firma electronica avanzada (no requerida para este nivel de criticidad)

### 1.3 Definiciones y acronimos

| Termino | Definicion |
|---------|------------|
| Lote | Conjunto de unidades de un producto fabricadas en un mismo ciclo de produccion |
| Bulto | Unidad de empaque individual que contiene producto (caja, bolsa, tambor) |
| Dictamen | Resultado de evaluacion de calidad: RECIBIDO, CUARENTENA, APROBADO, RECHAZADO, LIBERADO, RETIRO_MERCADO, etc. |
| Estado | Condicion del lote segun cantidad: NUEVO, DISPONIBLE, EN_USO, CONSUMIDO, VENDIDO, DEVUELTO, RECALL, DESCARTADO |
| Movimiento | Registro de transaccion que afecta cantidad de stock (ALTA, BAJA, MODIFICACION) |
| Traza | Registro de uso de un lote como materia prima en produccion de otro lote |
| CU | Caso de Uso |
| ERU | Especificacion de Requerimientos de Usuario |
| GxP | Good x Practice (Buenas Practicas: GMP, GDP, GLP) |
| ANMAT | Administracion Nacional de Medicamentos, Alimentos y Tecnologia Medica |
| Audit Trail | Registro cronologico de cambios que permite reconstruir el historial de datos |
| Soft Delete | Eliminacion logica (el registro se marca inactivo pero no se borra fisicamente) |

### 1.4 Referencias

| Documento | Version | Descripcion |
|-----------|---------|-------------|
| ANMAT Disposicion 4159/2023 | Anexo 6 | Requisitos para sistemas informatizados GxP |
| ISPE GAMP 5 | 2022 | Guia para validacion de sistemas informatizados |
| CLAUDE.md | - | Documentacion tecnica del proyecto |
| IQOQ-001 | 1.0 | Protocolo de Calificacion de Instalacion y Operacion |

---

## 2. Descripcion General del Sistema

### 2.1 Perspectiva del producto

Conitrack es un sistema web de gestion de stock farmaceutico diseñado para reemplazar el control manual mediante planillas Excel. Opera como sistema independiente de soporte, sin integracion con otros sistemas (ERP, LIMS, etc.).

**Arquitectura tecnica:**
| Componente | Tecnologia | Version |
|------------|------------|---------|
| Backend | Spring Boot | 3.4.1 |
| Base de datos | PostgreSQL | 17.x |
| Frontend | Thymeleaf (SSR) | 3.x |
| Contenedores | Docker | 24.x |
| Runtime | Java | 17+ |
| Migraciones BD | Flyway | 10.x |

**Diagrama de arquitectura simplificado:**
```
+------------------+     +------------------+     +------------------+
|    Navegador     | --> |   Spring Boot    | --> |   PostgreSQL     |
|   (Chrome/Edge)  |     |   (Thymeleaf)    |     |   (Docker)       |
+------------------+     +------------------+     +------------------+
```

### 2.2 Funciones principales

El sistema proporciona las siguientes funciones agrupadas por area:

| Area | Funciones |
|------|-----------|
| **Compras** | Alta ingreso compra, Devolucion a proveedor |
| **Produccion** | Alta lote produccion, Consumo produccion, Trazado de lotes |
| **Ventas** | Egreso venta, Devolucion cliente, Confirmacion liberacion |
| **Calidad** | Resultado analisis, Anulacion analisis, Reanalisis, Muestreo bultos |
| **Contingencias** | Retiro de mercado (Recall), Reverso de movimientos, Ajuste stock |
| **Consultas** | Listado lotes, Listado movimientos, Historial cambios |
| **Administracion** | Gestion usuarios, Gestion productos, Gestion proveedores, Configuracion |

### 2.3 Caracteristicas de los usuarios

El sistema define 8 roles organizados por areas funcionales:

| Rol | Area | Nivel | Descripcion | Permisos principales |
|-----|------|-------|-------------|---------------------|
| ADMIN | GLOBAL | 6 | Administrador del sistema | Acceso total, gestion usuarios |
| DT | GLOBAL | 5 | Director Tecnico | Aprobaciones finales, configuracion |
| GERENTE_GARANTIA_CALIDAD | CALIDAD | 4 | Gerente QA | Aprobaciones de calidad, reversos |
| GERENTE_CONTROL_CALIDAD | CALIDAD | 3 | Gerente QC | Gestion de analisis, muestreo |
| ANALISTA_CONTROL_CALIDAD | CALIDAD | 2 | Analista QC | Registro de analisis |
| SUPERVISOR_PLANTA | PLANTA | 3 | Supervisor produccion | Movimientos produccion, trazado |
| ANALISTA_PLANTA | PLANTA | 2 | Analista produccion | Movimientos basicos |
| AUDITOR | GLOBAL | 1 | Auditor externo | Solo lectura, accesos registrados |

**Jerarquia por areas:**
- Roles GLOBAL (ADMIN, DT, AUDITOR): Tienen autoridad sobre todas las areas
- Roles CALIDAD: Solo tienen autoridad dentro del area CALIDAD
- Roles PLANTA: Solo tienen autoridad dentro del area PLANTA
- CALIDAD y PLANTA no tienen autoridad cruzada entre si

### 2.4 Restricciones

| Restriccion | Especificacion |
|-------------|----------------|
| Navegadores soportados | Chrome, Firefox, Edge (ultimas 2 versiones) |
| Resolucion minima | 1280x720 pixeles |
| Conectividad | Conexion a internet requerida |
| Usuarios concurrentes | Hasta 20 usuarios simultaneos |
| Idioma interfaz | Español |

### 2.5 Supuestos y dependencias

| Supuesto/Dependencia | Descripcion |
|----------------------|-------------|
| Base de datos PostgreSQL | Disponible y accesible via red |
| Java Runtime 17+ | Instalado en servidor de aplicacion |
| Docker | Instalado para despliegue en contenedores |
| Usuarios capacitados | Personal con formacion basica en informatica |
| Backup externo | Procedimiento de backup configurado (ver SOP-001) |

---

## 3. Requerimientos Funcionales

### 3.1 Gestion de Lotes

#### RF-001: Alta de Lote por Compra

| Campo | Valor |
|-------|-------|
| **ID** | RF-001 |
| **Nombre** | Alta de Lote por Ingreso de Compra |
| **Descripcion** | El sistema debe permitir registrar el ingreso de lotes adquiridos a proveedores externos |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario ingresa: producto, nro lote, proveedor, cantidad, unidad, fecha vencimiento<br>2. Sistema genera codigo interno unico<br>3. Lote queda en estado NUEVO y dictamen RECIBIDO<br>4. Se registra movimiento tipo ALTA con motivo COMPRA<br>5. Cambio registrado en audit trail |
| **Caso de uso** | CU-AltaIngresoCompra |
| **Roles autorizados** | ADMIN, DT, SUPERVISOR_PLANTA, ANALISTA_PLANTA |

#### RF-002: Alta de Lote por Produccion

| Campo | Valor |
|-------|-------|
| **ID** | RF-002 |
| **Nombre** | Alta de Lote por Produccion Interna |
| **Descripcion** | El sistema debe permitir registrar lotes producidos internamente |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario ingresa: producto, nro lote, cantidad producida, fecha produccion<br>2. Opcionalmente vincula lotes de materia prima consumidos (trazas)<br>3. Sistema genera codigo interno unico<br>4. Lote queda en estado NUEVO y dictamen RECIBIDO<br>5. Se registra movimiento tipo ALTA con motivo PRODUCCION |
| **Caso de uso** | CU-AltaIngresoProduccion |
| **Roles autorizados** | ADMIN, DT, SUPERVISOR_PLANTA, ANALISTA_PLANTA |

#### RF-003: Consulta de Lotes

| Campo | Valor |
|-------|-------|
| **ID** | RF-003 |
| **Nombre** | Consulta y Listado de Lotes |
| **Descripcion** | El sistema debe permitir consultar lotes con filtros multiples |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Lista todos los lotes activos ordenados por fecha<br>2. Permite filtrar por: producto, nro lote, estado, dictamen, proveedor, fechas<br>3. Muestra: codigo, producto, nro lote, cantidad actual, estado, dictamen, vencimiento<br>4. Permite exportar listado<br>5. Paginacion de resultados |
| **Caso de uso** | Consulta general |
| **Roles autorizados** | Todos los roles |

#### RF-004: Detalle de Lote

| Campo | Valor |
|-------|-------|
| **ID** | RF-004 |
| **Nombre** | Visualizacion de Detalle de Lote |
| **Descripcion** | El sistema debe mostrar informacion completa de un lote seleccionado |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Muestra datos generales: producto, nro lote, cantidades, fechas, proveedor<br>2. Lista movimientos asociados al lote<br>3. Lista analisis realizados<br>4. Lista bultos del lote<br>5. Muestra historial de cambios (audit trail)<br>6. Muestra trazas (lotes derivados o lotes origen) |
| **Roles autorizados** | Todos los roles |

#### RF-005: Genealogia de Lotes

| Campo | Valor |
|-------|-------|
| **ID** | RF-005 |
| **Nombre** | Trazabilidad Genealogica de Lotes |
| **Descripcion** | El sistema debe permitir rastrear el origen de un lote hasta su raiz |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Cada lote puede tener un lote de origen (loteOrigen)<br>2. Sistema permite navegar la cadena de origen<br>3. Profundidad maxima de 5 niveles para evitar referencias circulares<br>4. Metodo getRootLote() retorna el lote raiz de la cadena |
| **Roles autorizados** | Todos los roles |

#### RF-006: Gestion de Bultos

| Campo | Valor |
|-------|-------|
| **ID** | RF-006 |
| **Nombre** | Gestion de Bultos por Lote |
| **Descripcion** | El sistema debe permitir registrar y gestionar bultos individuales dentro de un lote |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Un lote puede tener multiples bultos<br>2. Cada bulto tiene: numero, cantidad, unidad de medida<br>3. Suma de bultos <= cantidad total del lote<br>4. Muestreo puede afectar bultos individuales |
| **Caso de uso** | Gestion de bultos |
| **Roles autorizados** | ADMIN, DT, SUPERVISOR_PLANTA, ANALISTA_PLANTA |

### 3.2 Gestion de Movimientos

#### RF-011: Registro de Movimiento de Egreso (Venta)

| Campo | Valor |
|-------|-------|
| **ID** | RF-011 |
| **Nombre** | Egreso de Stock por Venta |
| **Descripcion** | El sistema debe permitir registrar salidas de stock por venta a clientes |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario selecciona lote y cantidad a egresar<br>2. Sistema valida que lote este APROBADO<br>3. Sistema valida cantidad disponible suficiente<br>4. Se descuenta cantidad del lote<br>5. Se registra movimiento tipo BAJA con motivo VENTA<br>6. Requiere motivo obligatorio (min 20 caracteres) |
| **Caso de uso** | CU-BajaVentaProducto |
| **Roles autorizados** | ADMIN, DT, SUPERVISOR_PLANTA, ANALISTA_PLANTA |

#### RF-012: Devolucion de Venta

| Campo | Valor |
|-------|-------|
| **ID** | RF-012 |
| **Nombre** | Ingreso por Devolucion de Cliente |
| **Descripcion** | El sistema debe permitir registrar devoluciones de producto de clientes |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Usuario selecciona movimiento de venta original<br>2. Ingresa cantidad devuelta y motivo<br>3. Se incrementa cantidad del lote<br>4. Dictamen del lote cambia a DEVOLUCION_CLIENTES para reinspeccion<br>5. Se registra movimiento tipo ALTA con motivo DEVOLUCION_VENTA |
| **Caso de uso** | CU-AltaDevolucionVenta |
| **Roles autorizados** | ADMIN, DT, SUPERVISOR_PLANTA |

#### RF-013: Devolucion de Compra

| Campo | Valor |
|-------|-------|
| **ID** | RF-013 |
| **Nombre** | Egreso por Devolucion a Proveedor |
| **Descripcion** | El sistema debe permitir registrar devoluciones de producto a proveedores |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Usuario selecciona lote y cantidad a devolver<br>2. Ingresa motivo de devolucion<br>3. Se descuenta cantidad del lote<br>4. Se registra movimiento tipo BAJA con motivo DEVOLUCION_COMPRA |
| **Caso de uso** | CU-BajaDevolucionCompra |
| **Roles autorizados** | ADMIN, DT, SUPERVISOR_PLANTA |

#### RF-014: Consumo de Produccion

| Campo | Valor |
|-------|-------|
| **ID** | RF-014 |
| **Nombre** | Egreso por Consumo en Produccion |
| **Descripcion** | El sistema debe permitir registrar consumo de materias primas en produccion |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario selecciona lote de materia prima y cantidad consumida<br>2. Opcionalmente vincula al lote producido (traza)<br>3. Sistema valida lote APROBADO y cantidad disponible<br>4. Se descuenta cantidad del lote<br>5. Se registra movimiento tipo BAJA con motivo CONSUMO_PRODUCCION |
| **Caso de uso** | CU-BajaConsumoProduccion |
| **Roles autorizados** | ADMIN, DT, SUPERVISOR_PLANTA, ANALISTA_PLANTA |

#### RF-015: Ajuste de Stock

| Campo | Valor |
|-------|-------|
| **ID** | RF-015 |
| **Nombre** | Ajuste de Cantidad de Stock |
| **Descripcion** | El sistema debe permitir ajustar cantidades por diferencias de inventario |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Usuario selecciona lote y nueva cantidad<br>2. Ingresa motivo detallado del ajuste (min 20 caracteres)<br>3. Sistema calcula diferencia (positiva o negativa)<br>4. Se registra movimiento tipo BAJA con motivo AJUSTE_STOCK<br>5. Cambio queda registrado en audit trail |
| **Caso de uso** | CU-BajaAjusteStock |
| **Roles autorizados** | ADMIN, DT, SUPERVISOR_PLANTA |

#### RF-016: Reverso de Movimiento

| Campo | Valor |
|-------|-------|
| **ID** | RF-016 |
| **Nombre** | Reverso de Movimiento Previo |
| **Descripcion** | El sistema debe permitir anular/revertir un movimiento registrado por error |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario selecciona movimiento a revertir<br>2. Sistema verifica autorizacion segun reglas de jerarquia<br>3. Ingresa motivo de reverso (min 20 caracteres)<br>4. Sistema genera movimiento inverso<br>5. Cantidades se ajustan automaticamente<br>6. Movimiento original queda vinculado al reverso |
| **Caso de uso** | CU-ReversoMovimiento |
| **Roles autorizados** | Segun jerarquia de roles (ver reglas de reverso) |

**Reglas de autorizacion para reverso:**
- El creador siempre puede revertir sus propios movimientos
- Roles GLOBAL (ADMIN, DT) pueden revertir cualquier movimiento
- Roles CALIDAD solo pueden revertir movimientos de usuarios CALIDAD de menor nivel
- Roles PLANTA solo pueden revertir movimientos de usuarios PLANTA de menor nivel
- AUDITOR nunca puede revertir (solo lectura)
- Movimientos sin creador (legacy): solo ADMIN puede revertir

#### RF-017: Muestreo de Bultos

| Campo | Valor |
|-------|-------|
| **ID** | RF-017 |
| **Nombre** | Muestreo de Bultos para Analisis |
| **Descripcion** | El sistema debe permitir registrar extraccion de muestras de bultos |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Usuario selecciona bulto y cantidad de muestra<br>2. Se descuenta cantidad del bulto<br>3. Se registra movimiento tipo BAJA con motivo MUESTREO<br>4. Opcionalmente crea registro de analisis vinculado |
| **Caso de uso** | CU-BajaMuestreoBulto |
| **Roles autorizados** | ADMIN, DT, GERENTE_CONTROL_CALIDAD, ANALISTA_CONTROL_CALIDAD |

### 3.3 Gestion de Analisis

#### RF-021: Registro de Resultado de Analisis

| Campo | Valor                                                                                                                                                                                                                                                                                                                   |
|-------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ID** | RF-021                                                                                                                                                                                                                                                                                                                  |
| **Nombre** | Registro de Resultado de Analisis de Calidad                                                                                                                                                                                                                                                                            |
| **Descripcion** | El sistema debe permitir registrar resultados de analisis de laboratorio                                                                                                                                                                                                                                                |
| **Prioridad** | Alta                                                                                                                                                                                                                                                                                                                    |
| **Criterios de aceptacion** | 1. Usuario selecciona lote con dictamen CUARENTENA<br>2. Ingresa: numero de analisis, fecha, resultado (dictamen)<br>3. Sistema actualiza dictamen del lote segun resultado<br>4. Si APROBADO: dictamen pasa a APROBADO<br>5. Si RECHAZADO: dictamen pasa a RECHAZADO<br>6. Se registra movimiento tipo MODIFICACION con motivo ANALISIS |
| **Caso de uso** | CU-ModifResultadoAnalisis                                                                                                                                                                                                                                                                                               |
| **Roles autorizados** | ADMIN, DT, GERENTE_CONTROL_CALIDAD, ANALISTA_CONTROL_CALIDAD                                                                                                                                                                                                                                                            |

#### RF-022: Anulacion de Analisis

| Campo | Valor |
|-------|-------|
| **ID** | RF-022 |
| **Nombre** | Anulacion de Analisis Erroneo |
| **Descripcion** | El sistema debe permitir anular un analisis registrado incorrectamente |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Usuario selecciona analisis a anular<br>2. Ingresa motivo de anulacion (min 20 caracteres)<br>3. Analisis queda marcado como anulado (no se elimina)<br>4. Lote retorna a estado/dictamen anterior<br>5. Se registra en audit trail |
| **Caso de uso** | CU-ModifAnulacionAnalisis |
| **Roles autorizados** | ADMIN, DT, GERENTE_CONTROL_CALIDAD |

#### RF-023: Reanalisis de Lote

| Campo | Valor |
|-------|-------|
| **ID** | RF-023 |
| **Nombre** | Solicitud de Reanalisis de Lote |
| **Descripcion** | El sistema debe permitir solicitar nuevo analisis de un lote previamente analizado |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Usuario selecciona lote con analisis previo<br>2. Ingresa motivo de reanalisis<br>3. Dictamen del lote pasa a CUARENTENA nuevamente<br>4. Se crea nuevo registro de analisis pendiente<br>5. Se mantiene historial de analisis anteriores |
| **Caso de uso** | CU-ModifReanalisisLote |
| **Roles autorizados** | ADMIN, DT, GERENTE_GARANTIA_CALIDAD, GERENTE_CONTROL_CALIDAD |

#### RF-024: Dictamen de Cuarentena

| Campo | Valor |
|-------|-------|
| **ID** | RF-024 |
| **Nombre** | Cambio de Dictamen a Cuarentena |
| **Descripcion** | El sistema debe permitir poner un lote en cuarentena por sospecha de calidad |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Usuario selecciona lote activo<br>2. Ingresa motivo de cuarentena<br>3. Dictamen del lote cambia a CUARENTENA<br>4. Lote queda bloqueado para venta hasta nueva aprobacion<br>5. Se registra movimiento tipo MODIFICACION |
| **Caso de uso** | CU-ModifDictamenCuarentena |
| **Roles autorizados** | ADMIN, DT, GERENTE_GARANTIA_CALIDAD, GERENTE_CONTROL_CALIDAD |

#### RF-025: Confirmacion de Lote Liberado

| Campo | Valor |
|-------|-------|
| **ID** | RF-025 |
| **Nombre** | Confirmacion de Liberacion de Lote |
| **Descripcion** | El sistema debe permitir confirmar la liberacion formal de un lote aprobado |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario selecciona lote con dictamen APROBADO<br>2. Confirma liberacion para venta<br>3. Dictamen cambia a LIBERADO<br>4. Lote disponible para operaciones de venta |
| **Caso de uso** | CU-ModifConfirmarLoteLiberado |
| **Roles autorizados** | ADMIN, DT, GERENTE_GARANTIA_CALIDAD |

### 3.4 Trazabilidad

#### RF-031: Registro de Traza de Lote

| Campo | Valor |
|-------|-------|
| **ID** | RF-031 |
| **Nombre** | Registro de Trazabilidad entre Lotes |
| **Descripcion** | El sistema debe permitir vincular lotes de materia prima con lotes producidos |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario selecciona lote producido (destino)<br>2. Selecciona lote(s) de materia prima (origen)<br>3. Ingresa cantidad utilizada de cada origen<br>4. Sistema crea registros de traza<br>5. Trazas permiten rastrear genealogia completa |
| **Caso de uso** | CU-ModifTrazadoLote |
| **Roles autorizados** | ADMIN, DT, SUPERVISOR_PLANTA, ANALISTA_PLANTA |

#### RF-032: Consulta de Trazabilidad

| Campo | Valor |
|-------|-------|
| **ID** | RF-032 |
| **Nombre** | Consulta de Trazabilidad de Lote |
| **Descripcion** | El sistema debe permitir consultar la cadena de trazabilidad de un lote |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario selecciona lote<br>2. Sistema muestra: lotes de origen (materias primas)<br>3. Sistema muestra: lotes derivados (productos terminados)<br>4. Permite navegacion a cualquier lote relacionado |
| **Roles autorizados** | Todos los roles |

### 3.5 Control de Acceso

#### RF-041: Autenticacion de Usuario

| Campo | Valor |
|-------|-------|
| **ID** | RF-041 |
| **Nombre** | Autenticacion de Usuario |
| **Descripcion** | El sistema debe autenticar usuarios mediante credenciales (usuario/contrasena) |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario ingresa credenciales en pantalla de login<br>2. Sistema valida contra base de datos<br>3. Credenciales validas: acceso concedido, redirige a inicio<br>4. Credenciales invalidas: mensaje de error generico (sin revelar cual fallo)<br>5. Intento registrado en log de seguridad |
| **Referencia ANMAT** | Anexo 6, Requisito 12.1 |

#### RF-042: Autorizacion por Rol

| Campo | Valor |
|-------|-------|
| **ID** | RF-042 |
| **Nombre** | Control de Acceso Basado en Roles |
| **Descripcion** | El sistema debe restringir acceso a funciones segun rol del usuario |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Cada usuario tiene asignado un unico rol<br>2. Cada funcion tiene roles autorizados definidos<br>3. Intento de acceso no autorizado: redirige a error 403<br>4. Menu muestra solo opciones autorizadas para el rol |
| **Referencia ANMAT** | Anexo 6, Requisito 12.2 |

#### RF-043: Bloqueo por Intentos Fallidos

| Campo | Valor |
|-------|-------|
| **ID** | RF-043 |
| **Nombre** | Bloqueo de Cuenta por Intentos Fallidos |
| **Descripcion** | El sistema debe bloquear cuentas tras multiples intentos fallidos de login |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Tras 5 intentos fallidos consecutivos, cuenta se bloquea<br>2. Bloqueo dura 15 minutos (configurable)<br>3. Durante bloqueo, login rechazado aunque credenciales sean correctas<br>4. Contador se resetea tras login exitoso<br>5. Admin puede desbloquear manualmente |
| **Referencia ANMAT** | Anexo 6, Requisito 12.1 |

#### RF-044: Expiracion de Contrasena

| Campo | Valor |
|-------|-------|
| **ID** | RF-044 |
| **Nombre** | Expiracion Periodica de Contrasenas |
| **Descripcion** | El sistema debe forzar cambio de contrasena periodicamente |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Contrasenas expiran tras 180 dias (configurable)<br>2. Al expirar, usuario es redirigido a cambio de contrasena<br>3. No puede acceder a otras funciones hasta cambiar<br>4. Sistema registra fecha de ultimo cambio |

#### RF-045: Cambio de Contrasena Obligatorio

| Campo | Valor |
|-------|-------|
| **ID** | RF-045 |
| **Nombre** | Cambio de Contrasena en Primer Login |
| **Descripcion** | El sistema debe forzar cambio de contrasena en primer acceso |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Nuevo usuario tiene flag mustChangePassword=true<br>2. En primer login, redirige a pantalla de cambio<br>3. No puede acceder a otras funciones hasta cambiar<br>4. Flag se desactiva tras cambio exitoso |

#### RF-046: Cierre de Sesion

| Campo | Valor |
|-------|-------|
| **ID** | RF-046 |
| **Nombre** | Cierre de Sesion de Usuario |
| **Descripcion** | El sistema debe permitir al usuario cerrar sesion voluntariamente |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Opcion de cerrar sesion visible en todas las pantallas<br>2. Al cerrar, invalida sesion del servidor<br>3. Redirige a pantalla de login<br>4. Intentos de acceso posteriores requieren nuevo login |

#### RF-047: Timeout de Sesion

| Campo | Valor |
|-------|-------|
| **ID** | RF-047 |
| **Nombre** | Expiracion Automatica de Sesion |
| **Descripcion** | El sistema debe cerrar sesiones inactivas automaticamente |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Sesion expira tras 30 minutos de inactividad (PROD) o 5 horas (DEV)<br>2. Al expirar, siguiente peticion redirige a login<br>3. Mensaje indica que sesion expiro |

### 3.6 Auditoria

#### RF-051: Registro de Cambios en Audit Trail

| Campo | Valor |
|-------|-------|
| **ID** | RF-051 |
| **Nombre** | Registro Automatico de Cambios (Audit Trail) |
| **Descripcion** | El sistema debe registrar automaticamente todos los cambios en datos GxP relevantes |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Cada cambio registra: usuario, fecha/hora, campo, valor anterior, valor nuevo, motivo<br>2. Motivo es obligatorio con minimo 20 caracteres<br>3. Registros de audit trail son inmutables (no modificables ni eliminables)<br>4. Audit trail disponible para consulta |
| **Referencia ANMAT** | Anexo 6, Requisito 9 |

#### RF-052: Consulta de Historial de Cambios

| Campo | Valor |
|-------|-------|
| **ID** | RF-052 |
| **Nombre** | Consulta de Audit Trail |
| **Descripcion** | El sistema debe permitir consultar el historial de cambios de cualquier registro |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Desde detalle de lote, acceso a historial de cambios<br>2. Muestra lista cronologica de cambios<br>3. Para cada cambio: fecha, usuario, campo, valor anterior, valor nuevo, motivo<br>4. No hay opcion de editar ni eliminar registros |
| **Referencia ANMAT** | Anexo 6, Requisito 9 |

#### RF-053: Motivo de Cambio Obligatorio

| Campo | Valor |
|-------|-------|
| **ID** | RF-053 |
| **Nombre** | Motivo Obligatorio para Cambios |
| **Descripcion** | El sistema debe requerir motivo para cualquier modificacion de datos GxP |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Campo "motivo" obligatorio en todos los formularios de cambio<br>2. Minimo 20 caracteres<br>3. Validacion en frontend y backend<br>4. No se puede guardar sin motivo valido |
| **Referencia ANMAT** | Anexo 6, Requisito 9 |

#### RF-054: Registro de Acceso de Auditores

| Campo | Valor |
|-------|-------|
| **ID** | RF-054 |
| **Nombre** | Registro Especial de Accesos de Auditores |
| **Descripcion** | El sistema debe registrar todos los accesos de usuarios con rol AUDITOR |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Cada acceso de AUDITOR registra: usuario, URL, metodo HTTP, IP, user-agent, fecha/hora<br>2. Registros almacenados en tabla auditoria_acceso<br>3. Registros tambien en archivo de log dedicado<br>4. Intentos de modificacion detectados y alertados |
| **Referencia ANMAT** | Cumplimiento GxP |

### 3.7 Contingencias

#### RF-061: Retiro de Mercado (Recall)

| Campo | Valor |
|-------|-------|
| **ID** | RF-061 |
| **Nombre** | Registro de Retiro de Mercado |
| **Descripcion** | El sistema debe permitir registrar retiros de mercado de lotes |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Usuario selecciona lote(s) afectados<br>2. Ingresa motivo del retiro<br>3. Dictamen de lotes cambia a RETIRO_MERCADO<br>4. Estado de lotes cambia a RECALL (si cantidad se reduce a cero)<br>5. Sistema bloquea operaciones de venta<br>6. Se registra movimiento especial de RETIRO |
| **Caso de uso** | CU-ModifRetiroMercado |
| **Roles autorizados** | ADMIN, DT, GERENTE_GARANTIA_CALIDAD |

#### RF-062: Recall Masivo

| Campo | Valor |
|-------|-------|
| **ID** | RF-062 |
| **Nombre** | Recall de Multiples Lotes |
| **Descripcion** | El sistema debe permitir aplicar recall a multiples lotes simultaneamente |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Usuario selecciona multiples lotes por criterio (producto, fecha, proveedor)<br>2. Aplica recall masivo con motivo unico<br>3. Todos los lotes seleccionados quedan afectados |
| **Caso de uso** | CU-ModifRecall |
| **Roles autorizados** | ADMIN, DT |

### 3.8 Administracion

#### RF-071: Gestion de Usuarios

| Campo | Valor |
|-------|-------|
| **ID** | RF-071 |
| **Nombre** | Administracion de Usuarios del Sistema |
| **Descripcion** | El sistema debe permitir gestionar usuarios (ABM) |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Crear usuario: username, nombre, email, rol, contrasena temporal<br>2. Modificar usuario: datos, rol, estado activo/inactivo<br>3. Desactivar usuario: soft delete (no se elimina)<br>4. Resetear contrasena: genera temporal, fuerza cambio<br>5. Desbloquear cuenta bloqueada |
| **Roles autorizados** | ADMIN |

#### RF-072: Gestion de Productos

| Campo | Valor |
|-------|-------|
| **ID** | RF-072 |
| **Nombre** | Administracion de Productos |
| **Descripcion** | El sistema debe permitir gestionar catalogo de productos |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Crear producto: codigo, nombre, tipo, descripcion<br>2. Modificar producto: datos editables<br>3. Desactivar producto: soft delete<br>4. Producto desactivado no aparece en seleccion pero se conserva historial |
| **Roles autorizados** | ADMIN, DT |

#### RF-073: Gestion de Proveedores

| Campo | Valor |
|-------|-------|
| **ID** | RF-073 |
| **Nombre** | Administracion de Proveedores |
| **Descripcion** | El sistema debe permitir gestionar catalogo de proveedores |
| **Prioridad** | Alta |
| **Criterios de aceptacion** | 1. Crear proveedor: codigo, razon social, CUIT, contacto<br>2. Modificar proveedor: datos editables<br>3. Desactivar proveedor: soft delete<br>4. Incluye fabricantes como subtipo de proveedor |
| **Roles autorizados** | ADMIN, DT |

#### RF-074: Configuracion del Sistema

| Campo | Valor |
|-------|-------|
| **ID** | RF-074 |
| **Nombre** | Gestion de Configuracion del Sistema |
| **Descripcion** | El sistema debe permitir modificar parametros de configuracion |
| **Prioridad** | Media |
| **Criterios de aceptacion** | 1. Lista parametros configurables<br>2. Cada parametro: clave, valor, tipo, descripcion<br>3. Validacion de valores segun tipo<br>4. Algunos parametros requieren reinicio (marcados)<br>5. Cambios registrados en audit trail |
| **Roles autorizados** | ADMIN |

---

## 4. Requerimientos No Funcionales

### 4.1 Seguridad

#### RNF-001: Politica de Contrasenas

| Campo | Valor |
|-------|-------|
| **ID** | RNF-001 |
| **Nombre** | Politica de Complejidad de Contrasenas |
| **Descripcion** | Las contrasenas deben cumplir requisitos minimos de complejidad |
| **Especificacion** | Minimo 8 caracteres, al menos 1 mayuscula, 1 minuscula, 1 numero |
| **Verificacion** | Test funcional en IQ/OQ (TC-041) |
| **Referencia ANMAT** | Anexo 6, Requisito 12.1 |

#### RNF-002: Bloqueo de Cuenta

| Campo | Valor |
|-------|-------|
| **ID** | RNF-002 |
| **Nombre** | Bloqueo por Intentos Fallidos |
| **Descripcion** | Cuenta se bloquea tras intentos fallidos de login |
| **Especificacion** | 5 intentos fallidos = bloqueo 15 minutos (configurable) |
| **Verificacion** | Test funcional en IQ/OQ (TC-003) |
| **Referencia ANMAT** | Anexo 6, Requisito 12.1 |

#### RNF-003: Expiracion de Sesion

| Campo | Valor |
|-------|-------|
| **ID** | RNF-003 |
| **Nombre** | Timeout de Sesion Inactiva |
| **Descripcion** | Sesiones inactivas deben expirar automaticamente |
| **Especificacion** | PROD: 30 minutos, DEV: 5 horas |
| **Verificacion** | Test funcional en IQ/OQ (TC-042) |

#### RNF-004: Encriptacion de Contrasenas

| Campo | Valor |
|-------|-------|
| **ID** | RNF-004 |
| **Nombre** | Almacenamiento Seguro de Contrasenas |
| **Descripcion** | Contrasenas deben almacenarse encriptadas |
| **Especificacion** | BCrypt con salt automatico |
| **Verificacion** | Inspeccion de base de datos |

#### RNF-005: HTTPS Obligatorio

| Campo | Valor |
|-------|-------|
| **ID** | RNF-005 |
| **Nombre** | Comunicacion Encriptada |
| **Descripcion** | Toda comunicacion debe usar HTTPS en produccion |
| **Especificacion** | TLS 1.2 minimo, certificado valido |
| **Verificacion** | Verificacion de configuracion |

### 4.2 Rendimiento

#### RNF-011: Tiempo de Respuesta

| Campo | Valor |
|-------|-------|
| **ID** | RNF-011 |
| **Nombre** | Tiempo de Respuesta de Paginas |
| **Descripcion** | Paginas deben cargar en tiempo razonable |
| **Especificacion** | < 3 segundos para operaciones normales, < 10 segundos para reportes |
| **Verificacion** | Medicion durante OQ |

#### RNF-012: Usuarios Concurrentes

| Campo | Valor |
|-------|-------|
| **ID** | RNF-012 |
| **Nombre** | Capacidad de Usuarios Simultaneos |
| **Descripcion** | Sistema debe soportar multiples usuarios concurrentes |
| **Especificacion** | Hasta 20 usuarios simultaneos |
| **Verificacion** | Test de carga opcional |

### 4.3 Disponibilidad

#### RNF-016: Backup de Datos

| Campo | Valor |
|-------|-------|
| **ID** | RNF-016 |
| **Nombre** | Backup Automatico de Base de Datos |
| **Descripcion** | Sistema debe tener backup automatico configurado |
| **Especificacion** | Backup semanal minimo, retencion 26 backups (~180 dias), RPO 7 dias |
| **Verificacion** | Ver SOP-001, Test TC-051 en IQ/OQ |
| **Referencia ANMAT** | Anexo 6, Requisito 7.2 |

#### RNF-017: Recuperacion de Datos

| Campo | Valor |
|-------|-------|
| **ID** | RNF-017 |
| **Nombre** | Capacidad de Restore |
| **Descripcion** | Backups deben ser restaurables |
| **Especificacion** | RTO maximo 4 horas, prueba trimestral obligatoria |
| **Verificacion** | Prueba de restore trimestral, Test TC-052 en IQ/OQ |
| **Referencia ANMAT** | Anexo 6, Requisito 7.2 |

### 4.4 Integridad de Datos

#### RNF-021: Soft Delete

| Campo | Valor |
|-------|-------|
| **ID** | RNF-021 |
| **Nombre** | Eliminacion Logica de Registros |
| **Descripcion** | Los registros nunca se eliminan fisicamente |
| **Especificacion** | @SQLDelete marca activo=false, datos siempre recuperables |
| **Verificacion** | Inspeccion de base de datos |
| **Referencia ANMAT** | Anexo 6, Requisito 7.1 |

#### RNF-022: Validacion de Entrada

| Campo | Valor |
|-------|-------|
| **ID** | RNF-022 |
| **Nombre** | Validacion de Datos de Entrada |
| **Descripcion** | Todos los campos deben validarse antes de guardar |
| **Especificacion** | Validacion frontend y backend, mensajes claros de error |
| **Verificacion** | Tests funcionales |
| **Referencia ANMAT** | Anexo 6, Requisito 6 |

#### RNF-023: Precision Decimal

| Campo | Valor |
|-------|-------|
| **ID** | RNF-023 |
| **Nombre** | Precision de Cantidades |
| **Descripcion** | Cantidades deben mantener precision adecuada |
| **Especificacion** | BigDecimal precision 12, escala 4 |
| **Verificacion** | Verificacion de calculos |

#### RNF-024: Conversion de Unidades

| Campo | Valor |
|-------|-------|
| **ID** | RNF-024 |
| **Nombre** | Conversion Correcta de Unidades de Medida |
| **Descripcion** | Conversiones entre unidades deben ser precisas |
| **Especificacion** | Usar UnidadMedidaUtils con factores definidos (KG, G, L, ML, UNIDAD) |
| **Verificacion** | Tests unitarios |

### 4.5 Cumplimiento Regulatorio

#### RNF-026: Audit Trail ANMAT

| Campo | Valor |
|-------|-------|
| **ID** | RNF-026 |
| **Nombre** | Cumplimiento de Audit Trail ANMAT |
| **Descripcion** | Audit trail debe cumplir requisitos ANMAT Disposicion 4159 |
| **Especificacion** | Registro de: quien, cuando, que, antes, despues, motivo (obligatorio) |
| **Verificacion** | Tests TC-031, TC-032, TC-033 en IQ/OQ |
| **Referencia ANMAT** | Anexo 6, Requisito 9 |

#### RNF-027: Retencion de Logs

| Campo | Valor |
|-------|-------|
| **ID** | RNF-027 |
| **Nombre** | Retencion de Logs de Auditoria |
| **Descripcion** | Logs de auditoria deben conservarse por tiempo definido |
| **Especificacion** | Retencion **indefinida** (ANMAT Anexo 6 Req. 9), tablas *_AUD append-only, sin purga automatica |
| **Verificacion** | Verificacion de configuracion logback |
| **Referencia ANMAT** | Anexo 6, Requisito 7.1 |

#### RNF-028: No Modificabilidad de Audit Trail

| Campo | Valor |
|-------|-------|
| **ID** | RNF-028 |
| **Nombre** | Inmutabilidad de Registros de Auditoria |
| **Descripcion** | Registros de audit trail no pueden modificarse ni eliminarse |
| **Especificacion** | Sin endpoints de edicion/eliminacion, tabla sin DELETE permitido |
| **Verificacion** | Test TC-033 en IQ/OQ |
| **Referencia ANMAT** | Anexo 6, Requisito 9 |

---

## 5. Interfaces del Sistema

### 5.1 Interfaces de usuario

| Caracteristica | Especificacion |
|----------------|----------------|
| Tipo | Interfaz web responsive |
| Tecnologia | HTML5, CSS3, Bootstrap, Thymeleaf |
| Navegacion | Menu lateral colapsable |
| Formularios | Validacion en tiempo real, mensajes de error claros |
| Tablas | Paginacion, ordenamiento, filtros por columna |
| Feedback | Mensajes de exito/error con colores distintivos |
| Accesibilidad | Contraste adecuado, etiquetas en formularios |

### 5.2 Interfaces de hardware

| Componente | Requerimiento |
|------------|---------------|
| Cliente | PC con navegador web moderno |
| Servidor | Servidor con Docker o Java 17+ |
| Resolucion | Minimo 1280x720 pixeles |
| Red | Conexion a internet (cliente-servidor) |

### 5.3 Interfaces de software

| Componente | Tecnologia | Version |
|------------|------------|---------|
| Base de datos | PostgreSQL | 17.x |
| Runtime | Java JDK | 17+ |
| Framework | Spring Boot | 3.4.1 |
| Contenedores | Docker | 24.x |
| Migraciones | Flyway | 10.x |

### 5.4 Interfaces de comunicacion

| Protocolo | Uso | Puerto |
|-----------|-----|--------|
| HTTPS | Comunicacion web (produccion) | 443 |
| HTTP | Desarrollo local | 8080 |
| JDBC | Conexion a PostgreSQL | 5432 |

---

## 6. Matriz de Trazabilidad

| Requerimiento | Descripcion | Test IQ/OQ | Prioridad |
|---------------|-------------|------------|-----------|
| RF-001 | Alta lote compra | TC-011 | Alta |
| RF-002 | Alta lote produccion | TC-012 | Alta |
| RF-003 | Consulta lotes | TC-013 | Alta |
| RF-011 | Egreso venta | TC-021 | Alta |
| RF-016 | Reverso movimiento | TC-022 | Alta |
| RF-021 | Resultado analisis | TC-023 | Alta |
| RF-031 | Registro traza | TC-024 | Alta |
| RF-041 | Autenticacion | TC-001 | Alta |
| RF-042 | Autorizacion rol | TC-004 | Alta |
| RF-043 | Bloqueo intentos | TC-003 | Alta |
| RF-051 | Audit trail | TC-031 | Alta |
| RF-052 | Consulta historial | TC-032 | Alta |
| RF-053 | Motivo obligatorio | TC-032 | Alta |
| RF-054 | Acceso auditor | TC-004 | Alta |
| RF-061 | Recall | TC-025 | Alta |
| RF-071 | Gestion usuarios | TC-005 | Alta |
| RNF-001 | Password policy | TC-041 | Alta |
| RNF-002 | Bloqueo cuenta | TC-003 | Alta |
| RNF-016 | Backup | TC-051 | Alta |
| RNF-017 | Restore | TC-052 | Alta |
| RNF-026 | Audit trail ANMAT | TC-031 | Alta |
| RNF-028 | Inmutabilidad audit | TC-033 | Alta |

---

## 7. Anexos

### 7.1 Diagramas de Dictamen y Estado de Lote

**IMPORTANTE:** El sistema maneja dos conceptos distintos:
- **Dictamen (DictamenEnum):** Resultado de evaluacion de calidad
- **Estado (EstadoEnum):** Condicion segun cantidad de stock

#### Diagrama de Dictamen (calidad)

```
                    +------------------+
                    |    RECIBIDO      |
                    |    (inicial)     |
                    +--------+---------+
                             |
                             v
                    +------------------+
                    |   CUARENTENA     |
                    | (en analisis)    |
                    +--------+---------+
                             |
              +--------------+---------------+
              |              |               |
              v              v               v
     +--------+----+  +------+------+  +-----+------+
     |  APROBADO   |  |  RECHAZADO  |  | CUARENTENA |
     |             |  |             |  |  (retorno) |
     +------+------+  +------+------+  +------------+
            |                |
            v                v
     +------+------+  +------+------+
     |  LIBERADO   |  | DESCARTADO  |
     | (para venta)|  | (destruido) |
     +------+------+  +-------------+
            |
            v
     +------+------+
     |RETIRO_MERCADO|
     |   (recall)   |
     +-------------+
```

#### Diagrama de Estado (cantidad)

```
     +-------------+
     |    NUEVO    |  <-- Alta (cantidad inicial = actual)
     +------+------+
            |
            v
     +------+------+
     |   EN_USO    |  <-- Baja parcial (cantidad inicial > actual > 0)
     +------+------+
            |
     +------+------+------+------+------+
     |      |      |      |      |      |
     v      v      v      v      v      v
+-------+ +------+ +------+ +------+ +------+ +----------+
|VENDIDO| |CONSU-| |DEVUEL| |RECALL| |DESCAR| |DISPONIBLE|
|(venta)| |MIDO  | |TO    | |      | |TADO  | |(traza)   |
+-------+ +------+ +------+ +------+ +------+ +----------+

Estados finales: cantidad = 0
```

### 7.2 Diagrama de Flujo - Alta de Lote

```
[Inicio] --> [Seleccionar Producto]
                    |
                    v
         [Ingresar datos lote]
         (nro, cantidad, vencimiento)
                    |
                    v
         [Seleccionar Proveedor]
                    |
                    v
         [Agregar Bultos] (opcional)
                    |
                    v
         [Ingresar Motivo]
         (min 20 caracteres)
                    |
                    v
         [Validar datos]
                    |
           +-------+-------+
           |               |
           v               v
      [Error]         [Guardar]
           |               |
           v               v
      [Mostrar         [Crear Lote]
       mensaje]        [Crear Movimiento ALTA]
                       [Registrar Audit Trail]
                              |
                              v
                         [Fin - Exito]
```

### 7.3 Modelo de Datos Simplificado

```
+-------------+     +---------------+     +-------------+
|   PRODUCTO  |     |     LOTE      |     |  PROVEEDOR  |
+-------------+     +---------------+     +-------------+
| id          |<--->| id            |<--->| id          |
| codigo      |     | numero_lote   |     | razon_social|
| nombre      |     | cantidad      |     | cuit        |
| tipo        |     | estado        |     +-------------+
+-------------+     | dictamen      |
                    | vencimiento   |
                    | lote_origen_id|--+
                    +-------+-------+  |
                            |          |
                            v          |
                    +-------+-------+  |
                    |  MOVIMIENTO   |  |
                    +---------------+  |
                    | id            |  |
                    | tipo          |  |
                    | motivo_enum   |  |
                    | cantidad      |  |
                    | fecha         |  |
                    | usuario_id    |  |
                    +---------------+  |
                            |          |
                            v          |
                    +-------+-------+  |
                    |   ANALISIS    |  |
                    +---------------+  |
                    | id            |  |
                    | numero        |  |
                    | dictamen      |  |
                    | fecha         |  |
                    +---------------+  |
                                       |
                    +---------------+  |
                    |    TRAZA      |<-+
                    +---------------+
                    | id            |
                    | lote_origen_id|
                    | lote_dest_id  |
                    | cantidad      |
                    +---------------+
```

---

**Fin del documento**

---

**Aprobaciones finales:**

| | Firma | Fecha |
|---|-------|-------|
| **Elaborado por:** | _________________ | ____/____/____ |
| **Revisado por (QA):** | _________________ | ____/____/____ |
| **Aprobado por (System Owner):** | _________________ | ____/____/____ |
