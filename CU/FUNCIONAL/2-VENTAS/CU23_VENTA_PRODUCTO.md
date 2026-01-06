# CU23 - VENTA PRODUCTO: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** BAJA
**Motivo:** VENTA

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Venta](#4-formulario-de-venta)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite registrar la **venta de productos terminados** (Unidades de Venta) a clientes. Representa la salida comercial del producto del sistema, descontando stock de los bultos y actualizando las trazas a estado VENDIDO para mantener la trazabilidad completa hasta el cliente final.

### 1.2 Cuando Usar Este CU

Usar CU23 cuando:
- Se concreta una venta de productos terminados a un cliente
- El lote esta liberado y listo para comercializacion
- Se necesita registrar la salida de stock por motivo de venta

**NO usar CU23 cuando:**
- El lote no esta liberado → usar primero **CU22 (Confirmar Lote Liberado)**
- El lote no es de tipo Unidad de Venta → las materias primas no se venden
- El lote esta en RECALL → no puede venderse
- Se quiere registrar una devolucion → usar **CU24 (Devolucion Venta)**

### 1.3 Contexto en el Ciclo de Vida

```
Produccion/Compra -> Cuarentena -> Analisis -> Aprobado -> LIBERADO -> VENTA
     (CU1/CU20)        (CU2)       (CU5)                    (CU22)    [CU23]
```

### 1.4 Resultado del CU

Al completar exitosamente:
- Se **descuenta el stock** de los bultos vendidos
- Las **trazas** de las unidades vendidas cambian a estado VENDIDO
- Se registra un **movimiento de BAJA** con motivo VENTA
- El estado del bulto/lote cambia a EN_USO o CONSUMIDO segun stock restante
- Si el lote queda sin stock y tenia analisis en curso, este se **CANCELA**

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Analista de Planta | SI | Usuario principal para esta operacion |
| Administrador | SI | Acceso completo al sistema |
| Supervisor de Planta | NO | No tiene acceso a ventas |
| Analista Control Calidad | NO | Su rol es analizar, no vender |
| Gerentes | NO | Roles de supervision, no operativos |
| DT | NO | Rol de liberacion, no de venta |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar que el lote seleccionado corresponde al pedido del cliente
- Indicar correctamente las cantidades a vender de cada bulto
- Documentar el motivo de la venta (mínimo 20 caracteres)
- Coordinar el retiro fisico de las unidades segun las trazas indicadas
- Confirmar los datos antes de ejecutar la operacion

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para venta, debe cumplir **todas** las siguientes condiciones:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Dictamen LIBERADO** | El lote debe haber sido liberado (CU22) |
| **Estado NO RECALL** | El lote no puede estar en proceso de recall |
| **Tipo Unidad de Venta** | Solo productos terminados pueden venderse |
| **Con stock** | Debe tener al menos un bulto con cantidad actual > 0 |

### 3.2 Tipos de Producto

| Tipo de Producto | Puede Venderse | Motivo |
|------------------|----------------|--------|
| Unidad de Venta | SI | Producto terminado para comercializacion |
| Semielaborado | NO | Producto intermedio, no comercializable |
| Granel | NO | Producto intermedio |
| API | NO | Materia prima |
| Excipiente | NO | Materia prima |
| Acondicionamiento | NO | Material de empaque |

### 3.3 Estados del Lote

| Estado/Dictamen Actual | Puede Venderse | Motivo |
|------------------------|----------------|--------|
| RECIBIDO | NO | Requiere pasar por analisis y liberacion |
| CUARENTENA | NO | Requiere pasar por analisis y liberacion |
| APROBADO | NO | Requiere liberacion (CU22) |
| RECHAZADO | NO | Lote rechazado no puede venderse |
| LIBERADO | SI | Estado correcto para venta |
| DEVUELTO | NO | Fue devuelto |
| RECALL | NO | Esta en proceso de retiro |

### 3.4 Flujo Previo Requerido

```
CU22 (Confirmar Lote Liberado)
         |
         | dictamen: LIBERADO
         v
CU23 (Venta Producto)
         |
         v
    [Lote Disponible para venta]
```

---

## 4. Formulario de Venta

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote a Vender** | SI | Selector | Lote del cual se venderan unidades | - |
| **Cantidades por Bulto** | SI | Lista | Cantidad a vender de cada bulto | 0 |
| **Unidades por Bulto** | SI | Lista | Siempre UNIDAD (automatico) | UNIDAD |
| **Fecha de Venta** | SI | Fecha | Fecha de la operacion de venta | Hoy |
| **Motivo del Cambio** | SI | Texto | Descripcion de la venta | - |

### 4.2 Informacion del Lote en el Selector

El selector muestra para cada lote elegible:

| Campo | Ejemplo |
|-------|---------|
| Codigo | UDV-001/25120001 |
| Producto | Ibuprofeno 400mg Blister x 20 |
| Nro Analisis | ANA-2025-0185 |
| Rango Trazas | 50001/51000 |

### 4.3 Informacion Detallada del Lote (Solo Lectura)

Al seleccionar un lote, se muestra:

| Campo | Descripcion |
|-------|-------------|
| Codigo interno | Codigo del lote |
| Producto | Nombre del producto |
| Producto destino | Producto de destino (si aplica) |
| Cantidad inicial | Cantidad original del lote |
| Stock actual | Cantidad disponible actualmente |
| Bultos totales | Numero de bultos del lote |
| Nro analisis actual | Numero del analisis aprobado |
| Fecha realizado analisis | Cuando se aprobo |
| Fecha vencimiento | Vencimiento del lote |
| Rango de trazas | Trazas min/max del lote |

### 4.4 Detalle de Cantidades por Bulto

Para cada bulto se muestra una tarjeta con:

| Campo | Descripcion |
|-------|-------------|
| Numero de bulto | Identificador del bulto (1, 2, 3...) |
| Stock disponible | Cantidad actual del bulto y unidad |
| Cantidad a vender | Campo numerico para indicar cuanto vender |
| Unidad de medida | Siempre UNIDAD (fijo) |

**Nota:** Se puede indicar 0 en bultos de los cuales no se vendera en esta operacion.

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado" |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado" |
| 3 | Fecha de Venta | Debe ingresar fecha | "La fecha es obligatoria" |
| 4 | Motivo del Cambio | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 5 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 6 | Motivo del Cambio | Maximo 300 caracteres | "El motivo no puede exceder 300 caracteres" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de venta debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de venta no puede ser anterior a la fecha de ingreso del lote" |

### 5.3 Validaciones de Cantidades

| Validacion | Mensaje de Error |
|------------|------------------|
| Al menos una cantidad debe ser mayor a 0 | "Debe ingresar al menos una cantidad a vender" |
| Cantidad por bulto no puede exceder stock del bulto | "La cantidad supera el stock del bulto X" |
| Las cantidades deben ser numeros enteros | "La cantidad debe ser un numero entero" |

---

## 6. Resultado de la Operacion

### 6.1 Cambios en el Lote

| Atributo | Cambio |
|----------|--------|
| Cantidad Actual | Se reduce en la cantidad total vendida |
| Estado | Cambia a EN_USO si cantidad > 0, o CONSUMIDO si cantidad = 0 |

### 6.2 Cambios en los Bultos

Para cada bulto del que se vende:

| Atributo | Cambio |
|----------|--------|
| Cantidad Actual | Se reduce en la cantidad vendida |
| Estado | Cambia a EN_USO si cantidad > 0, o CONSUMIDO si cantidad = 0 |

### 6.3 Cambios en las Trazas

Para cada unidad vendida, la traza correspondiente:

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Estado | LIBERADO | VENDIDO |

El sistema selecciona automaticamente las primeras trazas disponibles de cada bulto (metodo FIFO - First In, First Out).

### 6.4 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | BAJA |
| Motivo | VENTA |
| Cantidad | Cantidad total vendida |
| Unidad de Medida | UNIDAD |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de venta indicada |
| Motivo del cambio | "[CU23] " + Motivo del cambio ingresado |
| Activo | SI |

### 6.5 Detalles del Movimiento

Para cada bulto del que se vende:

| Atributo | Valor |
|----------|-------|
| Bulto | Referencia al bulto |
| Cantidad | Cantidad vendida de ese bulto |
| Unidad de Medida | UNIDAD |
| Trazas | Lista de trazas vendidas de ese bulto |
| Activo | SI |

### 6.6 Cancelacion de Analisis en Curso

Si el lote queda con cantidad = 0 y tenia un **analisis sin dictaminar** (reanalisis en curso):

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
[Formulario Venta Producto] ────────────────────┐
      |                                          |
      | (Seleccionar lote, indicar cantidades)   | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Exito] ◄────────────────────── [Menu Principal]
      |
      | (Nueva Venta) o (Ir a Inicio)
      v
[Formulario Venta] o [Menu Principal]
```

### 7.2 Pantalla de Exito

Muestra:
- Mensaje: "Venta de producto exitosa"
- Codigo del lote vendido
- Producto vendido
- Cantidad total vendida
- Stock restante del lote

**Informacion importante de trazas:**

> **ATENCION - Unidades a Retirar**
> Retirar del stock, para su venta, las unidades con los siguientes numeros de traza:
> - Bulto Nro 1: [50001] [50002] [50003]
> - Bulto Nro 2: [50101] [50102]

Esta informacion permite al operador identificar fisicamente que unidades debe retirar del almacen.

Opciones: "Registrar otra venta" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU23

| CU | Nombre | Cuando Aplicar |
|----|--------|----------------|
| CU23 | Venta adicional | Si quedan unidades del lote para vender |
| CU24 | Devolucion Venta | Si el cliente devuelve producto |
| CU31 | Reverso | Si la venta fue incorrecta |
| CU21 | Trazado | Para consultar trazabilidad del lote vendido |

### 8.2 Flujo Tipico Completo de Unidad de Venta

```
CU20: Ingreso Produccion (RECIBIDO)
         |
         v
CU2: Cuarentena (CUARENTENA + Nro Analisis)
         |
         v
CU3: Muestreo (muestra para analisis)
         |
         v
CU5: Resultado APROBADO
         |
         v
CU22: Confirmar Lote LIBERADO
         |
         v
CU23: Venta del Producto  ◄── [Este CU]
         |
         +───> Mas ventas del mismo lote (si queda stock)
         |
         +───> CU24: Si hay devolucion del cliente
         |
         v
    [FIN - Producto vendido al cliente]
```

---

## 9. Casos de Uso Ejemplos

### 9.1 Venta Completa de un Lote

**Situacion:** Se venden todas las unidades de un lote de 100 comprimidos en 2 bultos.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-001/25120001 |
| Producto | Ibuprofeno 400mg Blister x 20 |
| Dictamen | LIBERADO |
| Stock Actual | 100 UNIDAD en 2 bultos (50 c/u) |
| Rango Trazas | 50001/50100 |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Bulto 1 (50 UNI) | 50 UNIDAD |
| Bulto 2 (50 UNI) | 50 UNIDAD |
| Fecha de Venta | 06/12/2025 |
| Motivo del Cambio | Venta a Farmacia Central pedido #FC-2025-100 - Factura A-0001-00045678 |

**Resultado:**
- Bulto 1: 50 → 0 UNIDAD, Estado: CONSUMIDO
- Bulto 2: 50 → 0 UNIDAD, Estado: CONSUMIDO
- Lote: 100 → 0 UNIDAD, Estado: CONSUMIDO
- Trazas 50001-50100: Estado VENDIDO
- Movimiento de BAJA por 100 UNIDAD registrado
- Pantalla muestra trazas a retirar: 50001-50050 (bulto 1), 50051-50100 (bulto 2)

### 9.2 Venta Parcial de un Solo Bulto

**Situacion:** Se venden 30 unidades del primer bulto de un lote.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-002/25110050 |
| Producto | Jarabe Pediatrico 100ml |
| Stock Actual | 100 UNIDAD en 2 bultos (50 c/u) |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Bulto 1 (50 UNI) | 30 UNIDAD |
| Bulto 2 (50 UNI) | 0 |
| Fecha de Venta | 06/12/2025 |
| Motivo del Cambio | Venta a Drogueria del Norte - Pedido DN-2025-0890 |

**Resultado:**
- Bulto 1: 50 → 20 UNIDAD, Estado: EN_USO
- Bulto 2: Sin cambio
- Lote: 100 → 70 UNIDAD, Estado: EN_USO
- Trazas de las primeras 30 unidades: Estado VENDIDO
- Lote sigue disponible para mas ventas

### 9.3 Venta de Multiples Bultos

**Situacion:** Se vende de varios bultos a la vez para completar un pedido grande.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-003/25110080 |
| Producto | Comprimidos Paracetamol 500mg |
| Stock Actual | 500 UNIDAD en 5 bultos (100 c/u) |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Bulto 1 (100 UNI) | 100 UNIDAD |
| Bulto 2 (100 UNI) | 100 UNIDAD |
| Bulto 3 (100 UNI) | 50 UNIDAD |
| Bulto 4 (100 UNI) | 0 |
| Bulto 5 (100 UNI) | 0 |
| Fecha de Venta | 06/12/2025 |
| Motivo del Cambio | Pedido urgente Hospital Municipal - PO-2025-HOSP-450 |

**Resultado:**
- Bulto 1: 100 → 0 UNIDAD, Estado: CONSUMIDO
- Bulto 2: 100 → 0 UNIDAD, Estado: CONSUMIDO
- Bulto 3: 100 → 50 UNIDAD, Estado: EN_USO
- Bultos 4-5: Sin cambio
- Lote: 500 → 250 UNIDAD, Estado: EN_USO
- Movimiento de BAJA por 250 UNIDAD

### 9.4 Caso con Analisis en Curso (Reanalisis)

**Situacion:** Se venden todas las unidades de un lote que tiene un reanalisis pendiente.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-004/25100030 |
| Producto | Capsulas Vitamina D |
| Stock Actual | 50 UNIDAD |
| Analisis en Curso | ANA-2025-0220 (reanalisis sin dictaminar) |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Bulto 1 (50 UNI) | 50 UNIDAD |
| Fecha de Venta | 06/12/2025 |
| Motivo del Cambio | Venta completa del lote a Farmacia Sur - Pedido FS-890 |

**Resultado:**
- Lote: 50 → 0 UNIDAD, Estado: CONSUMIDO
- Analisis ANA-2025-0220: Dictamen cambia a CANCELADO (automatico)
- Movimiento de BAJA por 50 UNIDAD

### 9.5 Caso Error: Lote No Liberado

**Situacion:** El usuario intenta vender un lote que solo esta aprobado pero no liberado.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes con dictamen LIBERADO

### 9.6 Caso Error: Cantidad Excede Stock

**Situacion:** El usuario indica una cantidad mayor al stock disponible del bulto.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Bulto 1 | Stock: 20 UNIDAD, Venta solicitada: 30 UNIDAD |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "La cantidad supera el stock del bulto"
- El usuario debe corregir la cantidad

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿Puedo vender un lote que no esta liberado?**
R: No. Solo los lotes con dictamen LIBERADO aparecen disponibles para venta. Debe primero ejecutar CU22 (Confirmar Lote Liberado).

**P: ¿Puedo vender de varios bultos a la vez?**
R: Si. El formulario muestra todos los bultos con stock disponible y permite distribuir la venta entre ellos segun necesidad.

**P: ¿Para que sirven las trazas que muestra al final?**
R: Indica los numeros de traza especificos que deben retirarse fisicamente del almacen para entregar al cliente. Esto asegura trazabilidad ANMAT hasta el punto de venta.

**P: ¿Que pasa si el lote tiene un reanalisis en curso cuando lo vendo todo?**
R: El analisis se cancela automaticamente (dictamen CANCELADO) ya que no queda stock para analizar.

### 10.2 Sobre las Cantidades

**P: ¿Puedo vender mas de lo que hay en stock?**
R: No. El sistema valida que la cantidad a vender por bulto no exceda el stock disponible.

**P: ¿Por que la unidad siempre es UNIDAD?**
R: Los productos tipo Unidad de Venta se comercializan por unidad individual (frascos, blisters, cajas). El sistema fuerza la unidad a UNIDAD para ventas.

**P: ¿Puedo vender fracciones de unidad?**
R: No. Las cantidades deben ser numeros enteros ya que son unidades indivisibles.

### 10.3 Sobre Tipos de Producto

**P: ¿Por que no puedo vender API o Excipientes?**
R: Porque son materias primas que se consumen en produccion (CU7), no productos comercializables. Solo las Unidades de Venta pueden venderse a clientes.

**P: ¿Puedo vender Semielaborados?**
R: No. Los semielaborados son productos intermedios que se procesan internamente, no se comercializan.

### 10.4 Sobre las Trazas

**P: ¿Como selecciona el sistema que trazas vender?**
R: Automaticamente por FIFO (First In, First Out). Selecciona las primeras trazas disponibles de cada bulto segun la cantidad vendida.

**P: ¿Debo retirar fisicamente las unidades con esos numeros de traza?**
R: Si. Es fundamental para mantener la trazabilidad. Las unidades fisicas deben corresponder a los numeros de traza registrados en el sistema.

### 10.5 Sobre Errores

**P: ¿Que hago si registre mal la cantidad vendida?**
R: Use CU31 (Reverso) para anular la venta y luego registrela nuevamente con los datos correctos.

**P: ¿Que hago si vendi del lote incorrecto?**
R: Use CU31 (Reverso) para anular la operacion completa y repita con el lote correcto.

**P: ¿Por que no veo un lote que deberia estar disponible?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Tenga dictamen LIBERADO (no solo APROBADO)
- Sea de tipo Unidad de Venta
- NO este en estado RECALL
- Tenga stock disponible (al menos un bulto con cantidad > 0)

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Analista de Planta o Administrador
2. **Para que:** Registrar venta de productos terminados a clientes
3. **Pre-requisito:** Lote LIBERADO tipo Unidad de Venta con stock
4. **Tipo de movimiento:** BAJA
5. **Motivo del movimiento:** VENTA
6. **Unidad de medida:** Siempre UNIDAD
7. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)
8. **Trazas:** Cambian de LIBERADO a VENDIDO automaticamente (FIFO)
9. **Restriccion importante:** No puede vender lotes no liberados ni en RECALL

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU23)

| CU | Nombre | Resultado que Habilita |
|----|--------|------------------------|
| CU22 | Confirmar Lote Liberado | Lote con dictamen LIBERADO |

### Operaciones Siguientes (despues de CU23)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU23 | Venta adicional | Lote aun tiene stock |
| CU24 | Devolucion Venta | Cliente devuelve producto vendido |
| CU31 | Reverso | Si se necesita anular la venta |
| CU21 | Trazado | Consulta de trazabilidad del lote |

---

**Fin del Documento - CU23_VENTA_PRODUCTO v1.1 - Especificacion Funcional**
