# CU3 - MUESTREO: Especificacion Funcional

**Version:** 1.1
**Fecha:** 2025-12-09
**Ultima actualizacion:** 2025-12-28
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** BAJA
**Motivo:** MUESTREO

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Modalidades de Muestreo](#4-modalidades-de-muestreo)
5. [Formulario de Muestreo Trazable](#5-formulario-de-muestreo-trazable)
6. [Formulario de Muestreo Multi-Bulto](#6-formulario-de-muestreo-multi-bulto)
7. [Validaciones](#7-validaciones)
8. [Resultado de la Operacion](#8-resultado-de-la-operacion)
9. [Flujo de Pantallas](#9-flujo-de-pantallas)
10. [Operaciones Posteriores](#10-operaciones-posteriores)
11. [Casos de Uso Ejemplos](#11-casos-de-uso-ejemplos)
12. [Preguntas Frecuentes](#12-preguntas-frecuentes)
13. [Especificacion Tecnica](#13-especificacion-tecnica)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite a Control de Calidad **registrar el consumo de muestras** de un lote para su analisis de laboratorio. El muestreo es el paso donde se extrae fisicamente una porcion del material para realizar los ensayos de calidad.

### 1.2 Cuando Usar Este CU

Usar CU3 cuando:
- Se necesita tomar una muestra fisica de un lote en cuarentena para analizarlo
- Se requiere muestrear un lote ya dictaminado (APROBADO) para verificaciones adicionales
- Se debe registrar el consumo de material por muestreo de laboratorio

**NO usar CU3 cuando:**
- El lote aun no tiene numero de analisis asignado → usar primero **CU2 (Cuarentena)**
- El lote tiene dictamen RECIBIDO → usar primero **CU2**
- Se quiere consumir material para produccion → usar **CU7 (Consumo Produccion)**

### 1.3 Modalidades de Muestreo

El sistema ofrece **dos modalidades** de muestreo segun el tipo de producto:

| Modalidad | Cuando Usar | Productos |
|-----------|-------------|-----------|
| **Muestreo Trazable** | Productos con trazabilidad individual (unidades) | Unidades de Venta con trazas activas |
| **Muestreo Multi-Bulto** | Productos a granel o sin trazas individuales | API, Excipiente, Acond. Primario/Secundario, Semielaborado |

### 1.4 Resultado del CU

Al completar exitosamente:
- Se **descuenta la cantidad muestreada** del stock del bulto y del lote
- Se registra un **movimiento de BAJA** con motivo MUESTREO
- El movimiento queda **asociado al numero de analisis** del lote
- En muestreo trazable: las trazas seleccionadas cambian a estado **CONSUMIDO**
- El estado del bulto/lote puede cambiar a **EN_USO** o **CONSUMIDO** segun cantidades restantes

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Analista Control Calidad | SI | Usuario principal para esta operacion |
| Administrador | SI | Acceso completo al sistema |
| Analista de Planta | NO | No tiene acceso a operaciones de Calidad |
| Supervisor de Planta | NO | No tiene acceso a operaciones de Calidad |
| Gerentes | NO | Roles de supervision, no operativos |
| DT | NO | Rol de aprobacion final |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar fisicamente el lote y bulto a muestrear
- Tomar la muestra siguiendo los protocolos de muestreo
- Registrar correctamente las cantidades extraidas
- En muestreo trazable: seleccionar las unidades especificas muestreadas
- Documentar el motivo del muestreo (mínimo 20 caracteres)

---

## 3. Pre-condiciones

### 3.1 Requisitos Comunes

Para que un lote este disponible para muestreo, debe cumplir:

| Requisito | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Numero de Analisis** | Debe tener al menos un analisis asignado (CU2 previo) |
| **Stock disponible** | Al menos un bulto con cantidad actual > 0 |
| **Dictamen** | NO puede ser RECIBIDO (requiere pasar primero por CU2) |

### 3.2 Requisitos Adicionales por Modalidad

#### Para Muestreo Trazable:
| Requisito | Descripcion |
|-----------|-------------|
| **Trazas activas** | El lote debe tener trazas activas disponibles |
| **Tipo de producto** | Normalmente UNIDAD_VENTA con control individual |
| **Estado NO RECALL** | El lote no puede estar en estado RECALL (retiro de mercado) |

#### Para Muestreo Multi-Bulto:
| Requisito | Descripcion |
|-----------|-------------|
| **Sin trazas activas** | El lote NO debe tener trazas activas |
| **Tipo de producto** | API, Excipiente, Acond. Primario/Secundario, Semielaborado |
| **Estado NO RECALL** | El lote no puede estar en estado RECALL (retiro de mercado) |

### 3.3 Dictamenes Compatibles

| Dictamen | Puede Muestrearse | Situacion Tipica |
|----------|-------------------|------------------|
| RECIBIDO | NO | Requiere CU2 primero |
| CUARENTENA | SI | Caso mas comun - analisis en curso |
| APROBADO | SI | Muestreo adicional de verificacion |
| RECHAZADO | SI | Muestreo de contramuestra |
| LIBERADO | SI | Muestreo post-liberacion |

---

## 4. Modalidades de Muestreo

### 4.1 Muestreo Trazable

Esta modalidad aplica a **productos con trazabilidad individual**, tipicamente Unidades de Venta donde cada unidad tiene un numero de traza unico.

**Caracteristicas:**
- Se selecciona **un bulto especifico**
- Se seleccionan las **trazas individuales** a muestrear (checkboxes)
- La cantidad se calcula automaticamente segun las trazas seleccionadas
- La unidad de medida siempre es **UNIDAD**
- Las trazas seleccionadas cambian a estado **CONSUMIDO**

**Ejemplo:** Muestrear 3 comprimidos de un blister de 20 → Se seleccionan 3 trazas especificas.

### 4.2 Muestreo Multi-Bulto

Esta modalidad aplica a **productos a granel** o sin trazabilidad individual.

**Caracteristicas:**
- Se pueden muestrear **varios bultos simultaneamente**
- Se indica la **cantidad a extraer de cada bulto**
- Se puede usar cualquier **unidad de medida compatible** con el bulto
- Soporta **conversion automatica de unidades** (ej: gramos de un bulto en kg)

**Ejemplo:** Muestrear 50g del bulto 1 y 30g del bulto 2 de un lote de API.

---

## 5. Formulario de Muestreo Trazable

### 5.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Fecha de Muestreo** | SI | Fecha | Fecha de extraccion de la muestra | Hoy |
| **Lote** | SI | Selector | Lote del cual tomar la muestra | - |
| **Bulto** | SI | Selector | Bulto especifico a muestrear | - |
| **Trazas** | SI | Checkboxes | Trazas individuales a muestrear | - |
| **Motivo del Cambio/Observaciones** | SI | Texto | Descripcion del motivo del muestreo (min 20 chars) | - |

### 5.2 Informacion Mostrada del Lote (Solo Lectura)

Al seleccionar un lote, se muestra:

| Campo | Descripcion |
|-------|-------------|
| Numero de Analisis | Nro del ultimo analisis asociado al lote |
| Dictamen | Estado del dictamen actual |

### 5.3 Informacion del Bulto Seleccionado

Al seleccionar un bulto, se muestra:

| Campo | Descripcion |
|-------|-------------|
| Cantidad del Bulto | Stock disponible en el bulto |
| Unidad de Medida | Unidad del bulto |
| Trazas Disponibles | Lista de trazas activas para seleccionar |

### 5.4 Seleccion de Trazas

- Se muestra una lista de checkboxes con las trazas activas del bulto
- Cada traza se identifica por su numero: [1], [2], [3], etc.
- Se puede usar "Seleccionar todas" para marcar todas las trazas
- **Debe seleccionarse al menos una traza**
- La cantidad y unidad se calculan automaticamente (N UNIDADES)

---

## 6. Formulario de Muestreo Multi-Bulto

### 6.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote** | SI | Selector | Lote del cual tomar la muestra | - |
| **Cantidades por Bulto** | SI | Lista | Cantidad a muestrear de cada bulto | 0 |
| **Unidades por Bulto** | SI | Lista | Unidad de medida de cada cantidad | Unidad del bulto |
| **Fecha de Muestreo** | SI | Fecha | Fecha de extraccion de la muestra | Hoy |
| **Motivo del Cambio/Observaciones** | SI | Texto | Descripcion del motivo del muestreo (min 20 chars) | - |

### 6.2 Informacion Mostrada del Lote (Solo Lectura)

Al seleccionar un lote, se muestra:

| Campo | Descripcion |
|-------|-------------|
| Producto | Nombre del producto |
| Codigo interno | Codigo del lote |
| Producto destino | Producto de destino (si aplica) |
| Cantidad inicial | Cantidad original del lote |
| Stock actual | Cantidad disponible actualmente |
| Bultos totales | Numero de bultos del lote |
| Nro analisis dictaminado | Ultimo analisis con dictamen |
| Fechas de analisis | Fechas relevantes del analisis |

### 6.3 Detalle de Cantidades por Bulto

Para cada bulto se muestra una tarjeta con:

| Campo | Descripcion |
|-------|-------------|
| Numero de bulto | Identificador del bulto |
| Stock disponible | Cantidad actual del bulto y unidad |
| Cantidad a Muestrear | Campo numerico para indicar cuanto extraer |
| Unidad de Medida | Selector con unidades compatibles |

**Nota:** Se puede indicar 0 en bultos que no se muestrearan.

---

## 7. Validaciones

### 7.1 Validaciones Comunes

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado." |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado." |
| 3 | Lote | Debe tener analisis asociado | "El lote no tiene Analisis asociado." |
| 4 | Motivo del Cambio | Obligatorio, min 20 chars | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

### 7.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de muestreo debe ser igual o posterior a la fecha de ingreso del lote | "La fecha del movimiento no puede ser anterior a la fecha de ingreso del lote" |
| La fecha de analisis debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de realizado el analisis no puede ser anterior a la fecha de ingreso del lote" |

### 7.3 Validaciones Muestreo Trazable

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Bulto | Debe seleccionar uno | "Bulto no encontrado." |
| 2 | Bulto | Debe existir y estar activo | "Bulto no encontrado." |
| 3 | Trazas | Debe seleccionar al menos una | "Debe seleccionar al menos una unidad a muestrear." |
| 4 | Cantidad | Debe ser menor o igual al stock del bulto | "La cantidad excede el stock disponible del bulto." |
| 5 | Nro Analisis | Debe coincidir con el analisis en curso | "El número de análisis no coincide con el análisis en curso" |

### 7.4 Validaciones Muestreo Multi-Bulto

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Cantidades | Al menos una cantidad debe ser > 0 | "Debe ingresar las cantidades a consumir" |
| 2 | Cantidad por bulto | No puede exceder stock del bulto | "La cantidad ingresada ({cant} {um}) no puede superar el stock actual del bulto {n} ({stock} {um})" |
| 3 | Unidades | Deben ser compatibles con el producto | "Unidad no compatible con el producto." |

---

## 8. Resultado de la Operacion

### 8.1 Cambios en el Lote

| Atributo | Cambio |
|----------|--------|
| Cantidad Actual | Se reduce en la cantidad muestreada (convertida) |
| Estado | Cambia a EN_USO si cantidad > 0, o CONSUMIDO si cantidad = 0 |

### 8.2 Cambios en el Bulto

| Atributo | Cambio |
|----------|--------|
| Cantidad Actual | Se reduce en la cantidad muestreada (convertida) |
| Estado | Cambia a EN_USO si cantidad > 0, o CONSUMIDO si cantidad = 0 |

### 8.3 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | BAJA |
| Motivo | MUESTREO |
| Cantidad | Cantidad total muestreada |
| Unidad de Medida | Unidad del muestreo (UNIDAD para trazable, mayor unidad para multi-bulto) |
| Numero de Analisis | Nro del analisis asociado |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de muestreo indicada |
| Motivo del cambio | Motivo del cambio ingresado |
| Activo | SI |

### 8.4 Detalles del Movimiento

Para cada bulto muestreado se registra un detalle:

| Atributo | Valor |
|----------|-------|
| Bulto | Referencia al bulto muestreado |
| Cantidad | Cantidad extraida de ese bulto |
| Unidad de Medida | Unidad indicada |
| Activo | SI |

### 8.5 Cambios en Trazas (Solo Muestreo Trazable)

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Estado | DISPONIBLE | CONSUMIDO |
| Detalles | - | Se asocia al detalle del movimiento |

---

## 9. Flujo de Pantallas

### 9.1 Muestreo Trazable - Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Muestreo Trazable] ─────────────────┐
      |                                          |
      | (Seleccionar lote, bulto, trazas)        | (Cancelar)
      | (Completar datos y guardar)              |
      v                                          v
[Pantalla de Exito] ◄────────────────────── [Menu Principal]
      |
      | (Nuevo Muestreo) o (Ir a Inicio)
      v
[Formulario Muestreo] o [Menu Principal]
```

### 9.2 Muestreo Multi-Bulto - Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Muestreo Multi-Bulto] ──────────────┐
      |                                          |
      | (Seleccionar lote, indicar cantidades)   | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Vista Previa/Confirmacion] ───┐  [Menu Principal]
      |                                     |
      | (Confirmar)                         | (Volver a editar)
      v                                     v
[Pantalla de Exito] ◄────────────── [Formulario Muestreo]
      |
      | (Nuevo Muestreo) o (Ir a Inicio)
      v
[Formulario Muestreo] o [Menu Principal]
```

### 9.3 Pantalla de Exito

Muestra:
- Mensaje: "Muestreo registrado correctamente"
- Codigo del lote muestreado
- Cantidades y bultos afectados
- Opciones: "Realizar otro muestreo" o "Ir al inicio"

---

## 10. Operaciones Posteriores

### 10.1 Flujo Normal Despues de CU3

El flujo tipico despues de muestrear un lote es:

```
CU3: Muestreo (muestra tomada)
         |
         v
[Analisis de Laboratorio]
    - Se realizan ensayos fisicoquimicos y/o microbiologicos
         |
         v
CU5 o CU6: Resultado de Analisis
    - CU5: APROBADO → lote disponible para uso
    - CU6: RECHAZADO → lote debe devolverse o descartarse
         |
    +----+----+
    |         |
    v         v
Produccion  Devolucion
  (CU7)      (CU4)
```

### 10.2 Operaciones Posibles Despues de CU3

| CU | Nombre | Cuando Aplicar | Usuario |
|----|--------|----------------|---------|
| CU5 | Resultado Aprobado | Cuando el analisis da conforme | Analista Control Calidad |
| CU6 | Resultado Rechazado | Cuando el analisis da no conforme | Gerente Control Calidad |
| CU11 | Anulacion Analisis | Si el analisis debe cancelarse | Gerente Control Calidad |
| CU3 | Muestreo adicional | Si se requieren mas muestras | Analista Control Calidad |

### 10.3 Operaciones NO Posibles Mientras en Cuarentena

| CU | Nombre | Por Que No |
|----|--------|------------|
| CU7 | Consumo Produccion | Requiere lote APROBADO |
| CU22 | Liberacion | Requiere lote APROBADO tipo Unidad de Venta |
| CU23 | Venta | Requiere lote LIBERADO |

---

## 11. Casos de Uso Ejemplos

### 11.1 Muestreo Trazable: Comprimidos para Analisis

**Situacion:** Se necesita muestrear 5 comprimidos de un blister para analisis de control de calidad.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-001/25120001 |
| Producto | Ibuprofeno 400mg Blister x 20 |
| Dictamen Actual | CUARENTENA |
| Nro Analisis | ANA-2025-0150 |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Fecha de Muestreo | 06/12/2025 |
| Lote | UDV-001/25120001 |
| Bulto | 1 (20 UNIDAD disponibles) |
| Trazas seleccionadas | [1], [2], [3], [4], [5] |
| Motivo del Cambio | Muestreo para analisis de disolucion segun protocolo ANA-DISOL-001 |

**Resultado:**
- Se descuentan 5 UNIDADES del bulto 1
- Trazas 1-5 cambian a estado CONSUMIDO
- Movimiento de BAJA por MUESTREO asociado a ANA-2025-0150
- Bulto cambia a estado EN_USO (quedan 15 unidades)

### 11.2 Muestreo Multi-Bulto: API para Ensayos

**Situacion:** Se necesita muestrear Paracetamol de varios bultos para analisis completo.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05701_25110005 |
| Producto | Paracetamol (1-05701) |
| Dictamen Actual | CUARENTENA |
| Stock Actual | 75 KILOGRAMO en 3 bultos |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Fecha de Muestreo | 06/12/2025 |
| Lote | 1-05701_25110005 |
| Bulto 1 (30 KG) | 100 GRAMO |
| Bulto 2 (25 KG) | 50 GRAMO |
| Bulto 3 (20 KG) | 50 GRAMO |
| Motivo del Cambio | Muestreo para analisis fisicoquimico completo - protocolo APICQ-2025 |

**Resultado:**
- Bulto 1: 30 KG → 29.9 KG
- Bulto 2: 25 KG → 24.95 KG
- Bulto 3: 20 KG → 19.95 KG
- Total muestreado: 200 GRAMO = 0.2 KG
- Todos los bultos quedan en estado EN_USO

### 11.3 Muestreo de Lote Aprobado: Verificacion Adicional

**Situacion:** Un cliente reporta una queja sobre un lote ya aprobado y se necesita muestrear para verificacion.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05710_25090002 |
| Producto | Lactosa (1-05710) |
| Dictamen Actual | APROBADO |
| Nro Ultimo Analisis | ANA-2025-0089 |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Fecha de Muestreo | 06/12/2025 |
| Lote | 1-05710_25090002 |
| Bulto 1 (40 KG) | 200 GRAMO |
| Motivo del Cambio | Muestreo adicional por reclamo de cliente - Ticket RCLA-2025-045 |

**Resultado:**
- Se descuentan 200g del bulto 1
- Movimiento asociado al analisis existente ANA-2025-0089
- Lote mantiene dictamen APROBADO
- Se procedera a analisis de verificacion

### 11.4 Caso Error: Sin Seleccionar Trazas

**Situacion:** El usuario intenta guardar muestreo trazable sin seleccionar ninguna traza.

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "Debe seleccionar al menos una unidad a muestrear."
- El usuario debe seleccionar las trazas a muestrear

### 11.5 Caso Error: Cantidad Excede Stock

**Situacion:** El usuario intenta muestrear mas de lo disponible en el bulto.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Bulto 1 | Stock: 5 KG, Muestreo solicitado: 10 KG |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "La cantidad ingresada (10 KG) no puede superar el stock actual del bulto 1 (5 KG)"
- El usuario debe corregir la cantidad

---

## 12. Preguntas Frecuentes

### 12.1 Sobre el Proceso

**P: ¿Cual es la diferencia entre muestreo trazable y multi-bulto?**
R: El muestreo trazable es para productos con trazabilidad individual (unidades identificables). El multi-bulto es para productos a granel donde no hay trazas individuales.

**P: ¿Puedo muestrear un lote que no esta en cuarentena?**
R: Si, siempre que tenga un numero de analisis asignado. Se pueden muestrear lotes APROBADOS, RECHAZADOS o LIBERADOS para verificaciones adicionales.

**P: ¿Puedo muestrear un lote sin numero de analisis?**
R: No. Primero debe ejecutar CU2 (Cuarentena) para asignar un numero de analisis al lote.

**P: ¿El muestreo cambia el dictamen del lote?**
R: No. El dictamen solo cambia con CU5 (Aprobado), CU6 (Rechazado) o CU11 (Anulacion).

### 12.2 Sobre las Cantidades

**P: ¿Puedo muestrear de varios bultos a la vez?**
R: Si, en el modo Multi-Bulto puede indicar cantidades para cada bulto del lote.

**P: ¿Que pasa si muestreo todo el contenido de un bulto?**
R: El bulto cambia a estado CONSUMIDO y ya no estara disponible para futuras operaciones.

**P: ¿Puedo usar unidades diferentes a la del bulto?**
R: Si, siempre que sean compatibles (ej: gramos de un bulto en kilos). El sistema convierte automaticamente.

### 12.3 Sobre las Trazas

**P: ¿Que es una traza?**
R: Es un identificador unico para cada unidad individual de un producto (ej: cada comprimido de un blister, cada frasco de un lote).

**P: ¿Que pasa con las trazas muestreadas?**
R: Cambian a estado CONSUMIDO y ya no estan disponibles para otras operaciones.

**P: ¿Puedo muestrear una traza que ya fue consumida?**
R: No. Solo aparecen en la lista las trazas con estado DISPONIBLE o similar.

### 12.4 Sobre Errores

**P: ¿Que hago si muestree el bulto incorrecto?**
R: Puede usar CU31 (Reverso Movimiento) para anular la operacion si tiene permisos. Luego repita el muestreo correctamente.

**P: ¿Que hago si la cantidad muestreada fue incorrecta?**
R: Si muestreo de mas, use CU31 (Reverso Movimiento) para anular. Si muestreo de menos, puede realizar otro muestreo adicional.

**P: ¿Puedo muestrear el mismo lote varias veces?**
R: Si, siempre que haya stock disponible. Cada muestreo genera un movimiento separado.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Analista Control Calidad o Administrador
2. **Para que:** Registrar la extraccion de muestras para analisis de laboratorio
3. **Modalidades:** Trazable (unidades individuales) y Multi-Bulto (productos a granel)
4. **Pre-requisito:** El lote debe tener numero de analisis (CU2 previo)
5. **Tipo de movimiento:** BAJA
6. **Motivo del movimiento:** MUESTREO
7. **Campo critico:** Motivo del Cambio/Observaciones (mínimo 20 caracteres)
8. **Restriccion importante:** En muestreo trazable, debe seleccionar al menos una traza

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU3)

| CU | Nombre | Resultado que Habilita CU3 |
|----|--------|----------------------------|
| CU2 | Dictamen Cuarentena | Asigna Nro de Analisis |
| CU5 | Resultado Aprobado | Permite muestreo adicional |
| CU6 | Resultado Rechazado | Permite contramuestra |

### Operaciones Siguientes (despues de CU3)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU5 | Resultado Aprobado | Lote en CUARENTENA con muestreo realizado |
| CU6 | Resultado Rechazado | Lote en CUARENTENA con muestreo realizado |
| CU11 | Anulacion Analisis | Lote con analisis en curso |
| CU31 | Reverso Movimiento | Ultimo movimiento fue muestreo |

---

## 13. Especificacion Tecnica

### 13.1 Arquitectura de Servicios

El CU3 esta implementado mediante tres servicios coordinados:

| Servicio | Responsabilidad | URL Pattern |
|----------|-----------------|-------------|
| `BajaMuestreoBultoService` | Coordinador - delega a servicios especializados | - |
| `MuestreoTrazableService` | Procesa muestreo de productos con trazas individuales | `/calidad/baja/muestreo-trazable` |
| `MuestreoMultiBultoService` | Procesa muestreo de productos a granel | `/calidad/baja/muestreo-multi-bulto` |

### 13.2 Permisos en Sistema (PermisosCasoUsoEnum)

El sistema define DOS entradas de permisos separadas:

```java
CU3_MUESTREO_MULTI_BULTO(
    "Retiro por Muestreo Multi Bulto",
    "/calidad/baja/muestreo-multi-bulto",
    Arrays.asList(RoleEnum.ADMIN, RoleEnum.ANALISTA_CONTROL_CALIDAD)
),
CU3_MUESTREO_TRAZABLE(
    "Retiro por Muestreo Trazable",
    "/calidad/baja/muestreo-trazable",
    Arrays.asList(RoleEnum.ADMIN, RoleEnum.ANALISTA_CONTROL_CALIDAD)
)
```

### 13.3 MuestreoTrazableService

**Clase:** `com.mb.conitrack.service.cu.MuestreoTrazableService`

**Metodos Publicos:**

| Metodo | Descripcion |
|--------|-------------|
| `procesarMuestreoTrazable(MovimientoDTO, User)` | Procesa muestreo con trazabilidad individual |
| `validarMuestreoTrazableInput(MovimientoDTO, BindingResult)` | Valida entrada del formulario |
| `persistirMovimientoMuestreo(MovimientoDTO, Bulto, User)` | Crea movimiento BAJA/MUESTREO |

**Validaciones Especificas:**
- Motivo del cambio obligatorio (min 20 chars)
- Numero de analisis no nulo
- Lote debe existir y estar activo
- Para UNIDAD_VENTA: debe seleccionar al menos una traza
- Fecha de movimiento >= fecha ingreso lote
- Fecha de analisis >= fecha ingreso lote
- Bulto debe existir y estar activo
- Cantidad <= stock disponible

**Procesamiento de Trazas:**
- Marca trazas seleccionadas como CONSUMIDO
- Asocia trazas al detalle del movimiento
- Solo aplica a productos tipo UNIDAD_VENTA

### 13.4 MuestreoMultiBultoService

**Clase:** `com.mb.conitrack.service.cu.MuestreoMultiBultoService`

**Metodos Publicos:**

| Metodo | Descripcion |
|--------|-------------|
| `procesarMuestreoMultiBulto(LoteDTO, User)` | Procesa muestreo de multiples bultos |
| `validarMuestreoMultiBultoInput(LoteDTO, BindingResult)` | Valida entrada del formulario |
| `persistirMovimientoBajaMuestreoMultiBulto(LoteDTO, Lote, User)` | Crea movimiento con detalles por bulto |

**Validaciones Especificas:**
- Motivo del cambio obligatorio (min 20 chars)
- Lote debe existir y estar activo
- Lote debe tener analisis asociado
- Fecha de egreso >= fecha ingreso lote
- Al menos una cantidad > 0
- Cantidad por bulto <= stock del bulto

**Conversion de Unidades:**
- Soporta unidades diferentes por bulto
- Convierte automaticamente entre unidades compatibles
- El movimiento usa la mayor unidad de medida

### 13.5 Audit Trail (ANMAT Disp. 4159)

Ambos servicios registran auditoria mediante:

```java
auditarCambioCantidad(loteGuardado, movimiento,
    cantidadAnterior, cantidadActual,
    motivoDelCambio, "CU3-BAJA-MUESTREO-{TIPO}", getCurrentRequest());
```

Codigos de auditoria:
- `CU3-BAJA-MUESTREO-TRAZABLE`
- `CU3-BAJA-MUESTREO-MULTIBULTO`

---

**Fin del Documento - CU3_MUESTREO v1.1 - Especificacion Funcional**
