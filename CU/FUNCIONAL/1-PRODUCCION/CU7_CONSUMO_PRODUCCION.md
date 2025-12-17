# CU7 - CONSUMO PRODUCCION: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** BAJA
**Motivo:** CONSUMO_PRODUCCION

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Consumo](#4-formulario-de-consumo)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite registrar el **consumo de materiales aprobados** en el proceso de produccion interna. Cuando se fabrica un producto (semielaborado o producto terminado), se descuentan las materias primas utilizadas de su stock disponible.

### 1.2 Cuando Usar Este CU

Usar CU7 cuando:
- Se va a iniciar un lote de produccion y se necesitan materiales
- Se consume API, Excipiente o material de acondicionamiento en fabricacion
- Se descuenta stock de materiales previamente aprobados por Control de Calidad

**NO usar CU7 cuando:**
- El lote no esta aprobado → debe pasar primero por **CU5 (Resultado Aprobado)**
- El producto es una Unidad de Venta → estas se venden con **CU22**, no se consumen en produccion
- Se quiere muestrear para analisis → usar **CU3 (Muestreo)**
- Se quiere ajustar stock sin produccion real → usar **CU25 (Ajuste Stock)**

### 1.3 Resultado del CU

Al completar exitosamente:
- Se **descuenta la cantidad consumida** del stock de cada bulto utilizado
- Se registra un **movimiento de BAJA** con motivo CONSUMO_PRODUCCION
- El estado del bulto cambia a **EN_USO** o **CONSUMIDO** segun stock restante
- El estado del lote cambia a **EN_USO** o **CONSUMIDO** segun stock restante
- Si el lote queda sin stock y tenia analisis en curso, este se **CANCELA**

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Supervisor de Planta | SI | Usuario principal para esta operacion |
| Administrador | SI | Acceso completo al sistema |
| Analista de Planta | NO | No tiene acceso a consumo de produccion |
| Analista Control Calidad | NO | Su rol es analizar, no producir |
| Gerentes | NO | Roles de supervision, no operativos |
| DT | NO | Rol de aprobacion final |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar que el lote a consumir esta aprobado y disponible
- Registrar correctamente las cantidades consumidas de cada bulto
- Indicar la orden de produccion a la que corresponde el consumo
- Documentar el motivo del consumo (mínimo 20 caracteres)
- Coordinar con produccion para asegurar trazabilidad

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para consumo en produccion:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Dictamen APROBADO** | El lote debe estar aprobado por Control de Calidad |
| **Tipo de producto valido** | NO puede ser Unidad de Venta |
| **Con stock** | Debe tener al menos un bulto con cantidad actual > 0 |

### 3.2 Tipos de Producto Permitidos

| Tipo de Producto | Permitido | Motivo |
|------------------|-----------|--------|
| API | SI | Materia prima principal |
| Excipiente | SI | Componente de formulacion |
| Acondicionamiento Primario | SI | Envase primario |
| Acondicionamiento Secundario | SI | Envase secundario |
| Semielaborado | SI | Producto intermedio de produccion |
| Unidad de Venta | NO | Producto terminado, se vende no se consume |

### 3.3 Estados del Lote

| Estado Actual | Puede Consumirse | Motivo |
|---------------|------------------|--------|
| NUEVO | SI* | Si tiene dictamen APROBADO |
| DISPONIBLE | SI | Estado normal para consumo |
| EN_USO | SI | Ya se esta usando, puede seguir consumiendo |
| CONSUMIDO | NO | No queda stock disponible |
| DEVUELTO | NO | Fue devuelto al proveedor |

*El estado NUEVO con dictamen APROBADO es valido para consumo.

---

## 4. Formulario de Consumo

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote a Consumir** | SI | Selector | Lote del cual se consumira material | - |
| **Cantidades por Bulto** | SI | Lista | Cantidad a consumir de cada bulto | 0 |
| **Unidades por Bulto** | SI | Lista | Unidad de medida de cada cantidad | Unidad del bulto |
| **Fecha de Consumo** | SI | Fecha | Fecha de la operacion de consumo | Hoy |
| **Orden de Produccion** | SI | Texto | Numero de orden de produccion | - |
| **Motivo del Cambio** | SI | Texto | Descripcion del consumo | - |

### 4.2 Informacion del Lote en el Selector

El selector muestra para cada lote elegible:

| Campo | Ejemplo |
|-------|---------|
| Nombre Producto | Paracetamol |
| Nro Analisis | ANA-2025-0175 |
| Codigo Interno | 1-05701_25110005 |

### 4.3 Informacion Detallada del Lote (Solo Lectura)

Al seleccionar un lote, se muestra:

| Campo | Descripcion |
|-------|-------------|
| Producto | Nombre del producto |
| Codigo interno | Codigo del lote |
| Producto destino | Producto de destino (si aplica) |
| Cantidad inicial | Cantidad original del lote |
| Stock actual | Cantidad disponible actualmente |
| Bultos totales | Numero de bultos del lote |
| Nro analisis actual | Numero del analisis aprobado |
| Fecha realizado analisis | Cuando se aprobo |
| Fecha re-analisis | Proxima fecha de reanalisis |
| Fecha vencimiento analisis | Vencimiento del analisis |
| Re-analisis proveedor | Fecha del proveedor |
| Vto. proveedor | Vencimiento segun proveedor |

### 4.4 Detalle de Cantidades por Bulto

Para cada bulto se muestra una tarjeta con:

| Campo | Descripcion |
|-------|-------------|
| Numero de bulto | Identificador del bulto (1, 2, 3...) |
| Cantidad maxima | Stock disponible del bulto y unidad |
| Cantidad a consumir | Campo numerico para indicar cuanto usar |
| Unidad de medida | Selector con unidades compatibles |

**Nota:** Se puede indicar 0 en bultos que no se consumiran en esta orden.

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado" |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado" |
| 3 | Fecha de Consumo | Debe ingresar fecha | "La fecha es obligatoria" |
| 4 | Orden de Produccion | Debe ingresar valor | "La orden de produccion es obligatoria" |
| 5 | Motivo del Cambio | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 6 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 7 | Motivo del Cambio | Maximo 300 caracteres | "El motivo no puede exceder 300 caracteres" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de consumo debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de egreso no puede ser anterior a la fecha de ingreso del lote" |

### 5.3 Validaciones de Cantidades

| Validacion | Mensaje de Error |
|------------|------------------|
| Al menos una cantidad debe ser mayor a 0 | "Debe ingresar al menos una cantidad a consumir" |
| Cantidad por bulto no puede exceder stock del bulto | "La cantidad supera el stock del bulto X" |
| Unidad debe ser compatible con la unidad base del bulto | "Unidad de medida no compatible" |

---

## 6. Resultado de la Operacion

### 6.1 Cambios en el Lote

| Atributo | Cambio |
|----------|--------|
| Cantidad Actual | Se reduce en la cantidad total consumida (convertida a unidad del lote) |
| Estado | Cambia a EN_USO si cantidad > 0, o CONSUMIDO si cantidad = 0 |

### 6.2 Cambios en los Bultos

Para cada bulto consumido:

| Atributo | Cambio |
|----------|--------|
| Cantidad Actual | Se reduce en la cantidad consumida (convertida si es necesario) |
| Estado | Cambia a EN_USO si cantidad > 0, o CONSUMIDO si cantidad = 0 |

### 6.3 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | BAJA |
| Motivo | CONSUMO_PRODUCCION |
| Cantidad | Cantidad total consumida (en la mayor unidad de los bultos) |
| Unidad de Medida | Mayor unidad entre todos los bultos consumidos |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de consumo indicada |
| Motivo del cambio | Motivo del cambio ingresado |
| Orden Produccion | Numero de orden indicado |
| Activo | SI |

### 6.4 Detalles del Movimiento

Para cada bulto con consumo > 0:

| Atributo | Valor |
|----------|-------|
| Bulto | Referencia al bulto consumido |
| Cantidad | Cantidad consumida de ese bulto |
| Unidad de Medida | Unidad indicada para ese bulto |
| Activo | SI |

### 6.5 Cancelacion de Analisis en Curso

Si el lote queda con cantidad = 0 y tenia un **analisis sin dictaminar** (en curso):

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Dictamen del Analisis | null | CANCELADO |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Consumo Produccion] ────────────────┐
      |                                          |
      | (Seleccionar lote, indicar cantidades)   | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Exito] ◄────────────────────── [Menu Principal]
      |
      | (Nuevo Consumo) o (Ir a Inicio)
      v
[Formulario Consumo] o [Menu Principal]
```

### 7.2 Pantalla de Exito

Muestra:
- Mensaje: "Consumo registrado correctamente para la orden {OP}"
- Codigo del lote consumido
- Cantidad total consumida
- Stock restante del lote
- Opciones: "Registrar otro consumo" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU7

| CU | Nombre | Cuando Aplicar |
|----|--------|----------------|
| CU7 | Consumo adicional | Si se requiere mas material del mismo lote |
| CU20 | Ingreso Produccion | Para registrar el producto fabricado |
| CU26 | Reverso | Si el consumo fue incorrecto |

### 8.2 Flujo Tipico de Produccion

```
[Materiales Aprobados]
    |
    +--- API (Lote A) ────────┐
    |                         |
    +--- Excipiente (Lote B)──┤
    |                         v
    +--- Acond. (Lote C) ──> CU7: Consumo (x cada material)
                              |
                              v
                        [Proceso de Fabricacion]
                              |
                              v
                        CU20: Ingreso Produccion
                              |
                              v
                        [Semielaborado/UV Creado]
```

### 8.3 Relacion con Orden de Produccion

El campo **Orden de Produccion** permite:
- Vincular el consumo a una orden especifica
- Trazar que materiales se usaron en cada lote producido
- Auditar el uso de materiales por orden

---

## 9. Casos de Uso Ejemplos

### 9.1 Consumo Simple: Un Solo Bulto

**Situacion:** Se necesita consumir 5 kg de Paracetamol para una orden de produccion.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05701_25110005 |
| Producto | Paracetamol (1-05701) |
| Stock Actual | 25 KILOGRAMO en 1 bulto |
| Dictamen | APROBADO |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Bulto 1 (25 KG) | 5 KILOGRAMO |
| Fecha de Consumo | 06/12/2025 |
| Orden de Produccion | OP-2025-0150 |
| Motivo del Cambio | Consumo para fabricacion lote IBU-400 segun orden OP-2025-0150 |

**Resultado:**
- Bulto 1: 25 KG → 20 KG, Estado: EN_USO
- Lote: 25 KG → 20 KG, Estado: EN_USO
- Movimiento de BAJA por 5 KG registrado

### 9.2 Consumo Multi-Bulto con Conversion

**Situacion:** Se necesita consumir material de dos bultos en diferentes unidades.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05710_25110020 |
| Producto | Lactosa (1-05710) |
| Stock Actual | 40 KG en 2 bultos (20 KG c/u) |
| Dictamen | APROBADO |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Bulto 1 (20 KG) | 500 GRAMO |
| Bulto 2 (20 KG) | 1 KILOGRAMO |
| Fecha de Consumo | 06/12/2025 |
| Orden de Produccion | OP-2025-0151 |
| Motivo del Cambio | Consumo excipiente para lote de comprimidos segun formula maestra FM-001 |

**Resultado:**
- Bulto 1: 20 KG → 19.5 KG (descuento 500g = 0.5 kg)
- Bulto 2: 20 KG → 19 KG (descuento 1 kg)
- Lote: 40 KG → 38.5 KG
- Movimiento de BAJA por 1.5 KG (suma convertida a mayor unidad)
- Estado de ambos bultos y lote: EN_USO

### 9.3 Consumo Total de un Bulto

**Situacion:** Se consume todo el stock restante de un bulto.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | ACOND-001/25110030 |
| Producto | Frasco Ambar 100ml (ACOND-001) |
| Stock Actual | 150 UNIDAD en 3 bultos (50 c/u) |
| Dictamen | APROBADO |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Bulto 1 (50 UNI) | 50 UNIDAD |
| Bulto 2 (50 UNI) | 0 |
| Bulto 3 (50 UNI) | 20 UNIDAD |
| Fecha de Consumo | 06/12/2025 |
| Orden de Produccion | OP-2025-0152 |
| Motivo del Cambio | Envase primario para lote jarabe pediatrico JP-2025-010 |

**Resultado:**
- Bulto 1: 50 → 0 UNIDAD, Estado: **CONSUMIDO**
- Bulto 2: 50 → 50 UNIDAD (sin cambio)
- Bulto 3: 50 → 30 UNIDAD, Estado: EN_USO
- Lote: 150 → 80 UNIDAD, Estado: EN_USO
- Movimiento de BAJA por 70 UNIDAD

### 9.4 Consumo que Agota el Lote

**Situacion:** Se consume todo el stock restante del lote.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05702_25100015 |
| Producto | Ibuprofeno (1-05702) |
| Stock Actual | 2 KILOGRAMO en 1 bulto (ya parcialmente usado) |
| Dictamen | APROBADO |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Bulto 1 (2 KG) | 2 KILOGRAMO |
| Fecha de Consumo | 06/12/2025 |
| Orden de Produccion | OP-2025-0153 |
| Motivo del Cambio | Consumo final del lote para completar orden OP-2025-0153 |

**Resultado:**
- Bulto 1: 2 KG → 0 KG, Estado: **CONSUMIDO**
- Lote: 2 KG → 0 KG, Estado: **CONSUMIDO**
- Si habia analisis en curso: Dictamen cambia a CANCELADO
- Movimiento de BAJA por 2 KG

### 9.5 Caso Error: Lote No Aprobado

**Situacion:** El usuario intenta consumir un lote que aun esta en cuarentena.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes con dictamen APROBADO

### 9.6 Caso Error: Cantidad Excede Stock

**Situacion:** El usuario indica una cantidad mayor al stock disponible del bulto.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Bulto 1 | Stock: 10 KG, Consumo solicitado: 15 KG |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "La cantidad supera el stock del bulto"
- El usuario debe corregir la cantidad

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿Puedo consumir un lote que no esta aprobado?**
R: No. Solo los lotes con dictamen APROBADO aparecen disponibles para consumo.

**P: ¿Puedo consumir de varios lotes en la misma operacion?**
R: No. Cada operacion de consumo es para un solo lote. Si necesita consumir de varios lotes, debe realizar una operacion por cada uno.

**P: ¿Para que sirve el campo Orden de Produccion?**
R: Permite vincular el consumo a una orden especifica para trazabilidad. Todos los consumos de materiales de una orden quedan registrados con el mismo numero.

**P: ¿Que pasa si el lote tiene un analisis en curso cuando se agota?**
R: El analisis se cancela automaticamente (dictamen CANCELADO).

### 10.2 Sobre las Cantidades

**P: ¿Puedo consumir de varios bultos a la vez?**
R: Si. Puede indicar cantidad para cada bulto del lote. Los que no se usan se dejan en 0.

**P: ¿Puedo usar unidades diferentes a la del bulto?**
R: Si, siempre que sean compatibles (ej: gramos de un bulto en kilos). El sistema convierte automaticamente.

**P: ¿Que pasa si consumo todo el stock de un bulto?**
R: El bulto cambia a estado CONSUMIDO y ya no estara disponible para futuras operaciones.

### 10.3 Sobre Tipos de Producto

**P: ¿Por que no puedo consumir Unidades de Venta?**
R: Las Unidades de Venta son productos terminados que se venden, no se consumen en produccion. Use CU22 (Venta) para registrar salidas de UV.

**P: ¿Puedo consumir Semielaborados?**
R: Si. Los semielaborados pueden usarse como insumo para fabricar otros productos.

### 10.4 Sobre Errores

**P: ¿Que hago si registre mal la cantidad consumida?**
R: Use CU26 (Reverso) para anular el consumo y luego registrelo nuevamente con los datos correctos.

**P: ¿Que hago si seleccione el lote incorrecto?**
R: Use CU26 (Reverso) para anular la operacion completa y repita con el lote correcto.

**P: ¿Por que no veo un lote que deberia estar disponible?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Tenga dictamen APROBADO
- NO sea de tipo Unidad de Venta
- Tenga stock disponible (al menos un bulto con cantidad > 0)

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Supervisor de Planta o Administrador
2. **Para que:** Registrar consumo de materiales en produccion
3. **Pre-requisito:** Lote con dictamen APROBADO y tipo NO Unidad de Venta
4. **Tipo de movimiento:** BAJA
5. **Motivo del movimiento:** CONSUMO_PRODUCCION
6. **Campo adicional:** Orden de Produccion (para trazabilidad)
7. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)
8. **Restriccion importante:** Solo lotes APROBADOS y con stock disponible

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU7)

| CU | Nombre | Resultado que Habilita |
|----|--------|------------------------|
| CU5 | Resultado Aprobado | Lote con dictamen APROBADO |

### Operaciones Siguientes (despues de CU7)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU7 | Consumo adicional | Lote aun tiene stock |
| CU20 | Ingreso Produccion | Producto fabricado listo para ingresar |
| CU26 | Reverso | Si se necesita anular el consumo |

---

**Fin del Documento - CU7_CONSUMO_PRODUCCION v1.0 - Especificacion Funcional**
