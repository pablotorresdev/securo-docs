# CU23 - DEVOLUCION VENTA: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** ALTA
**Motivo:** DEVOLUCION_VENTA

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Devolucion](#4-formulario-de-devolucion)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite **registrar la devolucion de productos vendidos por parte de clientes**. Cuando un cliente devuelve productos, se crea un nuevo lote derivado del lote original de venta, con las unidades devueltas. Este lote derivado queda en estado DEVUELTO para su evaluacion posterior.

### 1.2 Concepto de Devolucion de Venta

La devolucion de venta:
- **Crea un nuevo lote** derivado del lote original (con sufijo `_D_N`)
- **Transfiere las trazas** de las unidades devueltas al nuevo lote
- **Cambia el estado** de las trazas devueltas a DEVUELTO
- El lote original **no cambia** su dictamen ni estado
- Se registra un **movimiento de ALTA** con motivo DEVOLUCION_VENTA

**Importante:** A diferencia de CU4 (Devolucion Compra), CU23 NO descuenta stock del lote original. Crea un lote nuevo con las unidades devueltas.

### 1.3 Cuando Usar Este CU

Usar CU23 cuando:
- Un cliente devuelve productos que fueron vendidos
- Se necesita registrar el ingreso de unidades devueltas
- Se requiere mantener trazabilidad de las unidades devueltas

**NO usar CU23 cuando:**
- Se quiere devolver al proveedor → usar **CU4 (Devolucion Compra)**
- Se quiere registrar una venta → usar **CU22 (Venta Producto)**
- El lote esta en RECALL → usar flujo de recall correspondiente

### 1.4 Resultado del CU

Al completar exitosamente:
- Se crea un **nuevo lote** con codigo `{codigoOriginal}_D_{N}` (N = numero secuencial)
- El nuevo lote tiene estado **DEVUELTO** y dictamen **DEVOLUCION_CLIENTES**
- Las **trazas** de las unidades devueltas se transfieren al nuevo lote con estado DEVUELTO
- Se registra un **movimiento de ALTA** con motivo DEVOLUCION_VENTA
- El movimiento referencia al movimiento de venta original

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Gerente Garantia Calidad | SI | Usuario principal para devoluciones |
| Administrador | SI | Acceso completo al sistema |
| Director Tecnico (DT) | NO | No participa en devoluciones |
| Gerente Control Calidad | NO | No tiene acceso a devoluciones |
| Analista Control Calidad | NO | No tiene acceso a devoluciones |
| Supervisor de Planta | NO | No tiene acceso a ventas |
| Analista de Planta | NO | No tiene acceso a ventas |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar que las unidades devueltas corresponden a la venta indicada
- Seleccionar correctamente las trazas o cantidades devueltas
- Documentar el motivo de la devolucion (mínimo 20 caracteres)
- Coordinar la recepcion fisica de las unidades devueltas

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para devolucion de venta:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **NO en RECALL** | El lote no puede estar en estado RECALL |
| **Con ventas** | Debe tener trazas VENDIDO o movimientos de VENTA |

### 3.2 Lotes Elegibles

El sistema muestra lotes que cumplan **alguna** de estas condiciones:

| Condicion | Descripcion |
|-----------|-------------|
| Trazas VENDIDO | Tiene al menos una traza en estado VENDIDO |
| Movimiento VENTA | Tiene al menos un movimiento con motivo VENTA |

### 3.3 Estados del Lote

| Estado | Puede Recibir Devolucion | Motivo |
|--------|-------------------------|--------|
| NUEVO | SI* | Si tiene ventas |
| DISPONIBLE | SI* | Si tiene ventas |
| EN_USO | SI* | Si tiene ventas |
| CONSUMIDO | SI* | Si tiene ventas |
| RECALL | NO | Lote en recall |

*Siempre que tenga trazas VENDIDO o movimientos de VENTA.

### 3.4 Modalidades de Devolucion

Existen dos modalidades segun el tipo de lote:

| Modalidad | Aplica a | Seleccion |
|-----------|----------|-----------|
| **Por Trazas** | Lotes trazados | Se seleccionan trazas individuales |
| **Por Cantidades** | Lotes no trazados | Se indican cantidades por bulto |

---

## 4. Formulario de Devolucion

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote** | SI | Selector | Lote del cual se devuelven unidades | - |
| **Movimiento de Venta** | SI | Selector | Movimiento de venta origen | - |
| **Trazas a Devolver** | SI* | Checkboxes | Trazas vendidas a devolver (lotes trazados) | - |
| **Cantidades por Bulto** | SI* | Numeros | Cantidades a devolver por bulto (lotes no trazados) | 0 |
| **Fecha Devolucion** | SI | Fecha | Fecha de la devolucion | Hoy |
| **Motivo del Cambio** | SI | Textarea | Descripcion del motivo | - |

*Segun modalidad: trazas para lotes trazados, cantidades para no trazados.

### 4.2 Informacion del Lote en el Selector

El selector muestra para cada lote elegible:

| Campo | Ejemplo |
|-------|---------|
| Producto | Ibuprofeno 400mg Blister x 20 |
| Codigo interno | UDV-001/25120001 |
| Fecha ingreso | 01/12/2025 |
| Rango Trazas | 50001 / 51000 |

### 4.3 Informacion del Movimiento de Venta

Al seleccionar un lote, se cargan los movimientos de venta disponibles:

| Campo | Ejemplo |
|-------|---------|
| Fecha | 05/12/2025 |
| Cantidad | 100 UNIDAD |

### 4.4 Seleccion de Trazas (Lotes Trazados)

Para lotes trazados, se muestran las trazas vendidas agrupadas por bulto:

```
Bulto 1
  [ ] Traza 50001
  [ ] Traza 50002
  [ ] Traza 50003

Bulto 2
  [ ] Traza 50101
  [ ] Traza 50102
```

El usuario selecciona con checkbox las trazas que estan siendo devueltas.

### 4.5 Cantidades por Bulto (Lotes No Trazados)

Para lotes no trazados, se muestran campos numericos por bulto:

| Bulto | Cantidad Maxima | Cantidad a Devolver |
|-------|-----------------|---------------------|
| Bulto 1 | 50 | [____] |
| Bulto 2 | 50 | [____] |

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "El lote no existe." |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado." |
| 3 | Movimiento Origen | Debe seleccionar uno | "No se encontro el movimiento de venta origen" |
| 4 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de devolucion debe ser igual o posterior a la fecha de ingreso del lote | "La fecha del movmiento no puede ser anterior a la fecha de ingreso del lote" |
| La fecha de devolucion debe ser igual o posterior a la fecha del movimiento de venta | "La fecha de devolución no puede ser anterior a la fecha del movimiento de venta." |

### 5.3 Validaciones de Trazas (Lotes Trazados)

| Validacion | Mensaje de Error |
|------------|------------------|
| Debe seleccionar al menos una traza | "Debe seleccionar al menos una traza para devolver." |
| Las trazas deben estar en estado VENDIDO | (implícito - solo se muestran vendidas) |

### 5.4 Validaciones de Cantidades (Lotes No Trazados)

| Validacion | Mensaje de Error |
|------------|------------------|
| Debe ingresar al menos una cantidad > 0 | "Ingrese al menos una cantidad valida (> 0) y no superior al maximo permitido." |
| Cantidad no puede exceder el maximo | "Ingrese al menos una cantidad valida (> 0) y no superior al maximo permitido." |

---

## 6. Resultado de la Operacion

### 6.1 Nuevo Lote Creado

| Atributo | Valor |
|----------|-------|
| Codigo Lote | `{codigoOriginal}_D_{N}` |
| Lote Origen | Referencia al lote original |
| Estado | DEVUELTO |
| Dictamen | DEVOLUCION_CLIENTES |
| Unidad de Medida | UNIDAD |
| Cantidad Inicial | Total de unidades devueltas |
| Cantidad Actual | Total de unidades devueltas |
| Trazado | Mismo valor que el lote original |
| Producto | Mismo del lote original |
| Proveedor | Mismo del lote original |
| Fabricante | Mismo del lote original |
| Activo | SI |

**Ejemplo de codigo:** Si el lote original es `UDV-001/25120001` y es la primera devolucion, el nuevo lote sera `UDV-001/25120001_D_1`.

### 6.2 Bultos del Nuevo Lote

Para cada grupo de trazas o cantidades devueltas:

| Atributo | Valor |
|----------|-------|
| Numero de Bulto | Numero del bulto original |
| Cantidad Inicial | Cantidad devuelta de ese bulto |
| Cantidad Actual | Cantidad devuelta de ese bulto |
| Unidad de Medida | UNIDAD |
| Estado | DEVUELTO |
| Activo | SI |

### 6.3 Cambios en las Trazas (Lotes Trazados)

Para cada traza devuelta:

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Lote | Lote original | Nuevo lote devolucion |
| Bulto | Bulto original | Bulto del nuevo lote |
| Estado | VENDIDO | DEVUELTO |

### 6.4 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | ALTA |
| Motivo | DEVOLUCION_VENTA |
| Lote | Nuevo lote de devolucion |
| Movimiento Origen | Movimiento de venta seleccionado |
| Cantidad | Total de unidades devueltas |
| Unidad de Medida | UNIDAD |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de devolucion indicada |
| Motivo del cambio | Motivo del cambio ingresado |
| Activo | SI |

### 6.5 Detalles del Movimiento

Para cada bulto con unidades devueltas:

| Atributo | Valor |
|----------|-------|
| Bulto | Bulto del nuevo lote |
| Cantidad | Cantidad devuelta de ese bulto |
| Unidad de Medida | UNIDAD |
| Trazas | Lista de trazas devueltas de ese bulto |
| Activo | SI |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Devolucion Venta] ──────────────────┐
      |                                           |
      | (Seleccionar lote, movimiento, trazas)    | (Cancelar)
      |                                           |
      v                                           v
[Pantalla de Confirmacion] ◄─────────────── [Menu Principal]
      |
      | (Confirmar)
      v
[Pantalla de Exito]
      |
      | (Nueva Devolucion) o (Ir a Inicio)
      v
[Formulario Devolucion] o [Menu Principal]
```

### 7.2 Pantalla de Confirmacion

Antes de procesar, se muestra una vista previa con:
- Datos del lote original
- Producto y codigo
- Movimiento de venta seleccionado
- Resumen de trazas/cantidades a devolver
- Motivo de la devolucion

El usuario debe confirmar para proceder.

### 7.3 Pantalla de Exito

Muestra:
- Mensaje de exito: "Ingreso de stock por devolucion exitoso"
- Codigo del nuevo lote de devolucion
- Producto
- Cantidad devuelta
- Estado: DEVUELTO
- Dictamen: DEVOLUCION_CLIENTES

Opciones: "Registrar otra devolucion" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU23

| CU | Nombre | Cuando Aplicar |
|----|--------|----------------|
| CU2 | Dictamen Cuarentena | Para analizar las unidades devueltas |
| CU26 | Reverso | Si la devolucion fue incorrecta |
| CU25 | Ajuste Stock | Si las unidades deben descartarse |

### 8.2 Flujo Tipico Post-Devolucion

```
CU23: Devolucion Venta
         |
         | Nuevo lote con estado DEVUELTO
         v
    +----+----+
    |         |
    v         v
Evaluar    Descartar
Unidades   Unidades
    |         |
    v         v
CU2:       CU25:
Analizar   Ajuste Stock
```

### 8.3 Diferencia con Devolucion Compra

| Aspecto | Devolucion Venta (CU23) | Devolucion Compra (CU4) |
|---------|-------------------------|-------------------------|
| Tipo operacion | ALTA | BAJA |
| Crea lote | SI (lote derivado) | NO |
| Descuenta stock original | NO | SI |
| Estado resultante | DEVUELTO | DEVUELTO |
| Direccion | Cliente → Empresa | Empresa → Proveedor |

---

## 9. Casos de Uso Ejemplos

### 9.1 Devolucion de Lote Trazado

**Situacion:** Un cliente devuelve 5 unidades de un lote de 100 comprimidos.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-001/25120001 |
| Producto | Ibuprofeno 400mg Blister x 20 |
| Rango Trazas | 50001 / 50100 |
| Trazado | SI |

**Movimiento de Venta seleccionado:**
| Campo | Valor |
|-------|-------|
| Fecha | 05/12/2025 |
| Cantidad | 10 UNIDAD |

**Trazas seleccionadas:**
- [x] Traza 50001
- [x] Traza 50002
- [x] Traza 50003
- [x] Traza 50004
- [x] Traza 50005

**Datos adicionales:**
| Campo | Valor |
|-------|-------|
| Fecha Devolucion | 06/12/2025 |
| Motivo del Cambio | Devolucion de cliente por fecha de vencimiento proxima - Ticket #DEV-2025-001 |

**Resultado:**
- Nuevo lote: UDV-001/25120001_D_1
- Estado: DEVUELTO
- Dictamen: DEVOLUCION_CLIENTES
- Cantidad: 5 UNIDAD
- Trazas 50001-50005: Estado DEVUELTO, vinculadas al nuevo lote
- Movimiento de ALTA registrado

### 9.2 Devolucion de Lote No Trazado

**Situacion:** Cliente devuelve 30 unidades de un lote no trazado.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-002/25110050 |
| Producto | Jarabe Pediatrico 100ml |
| Trazado | NO |

**Cantidades por bulto:**
| Bulto | Cantidad Vendida | Cantidad a Devolver |
|-------|------------------|---------------------|
| Bulto 1 | 50 | 20 |
| Bulto 2 | 50 | 10 |

**Datos adicionales:**
| Campo | Valor |
|-------|-------|
| Fecha Devolucion | 06/12/2025 |
| Motivo del Cambio | Devolucion por error en pedido del cliente - Factura NC-0001-00012345 |

**Resultado:**
- Nuevo lote: UDV-002/25110050_D_1
- Estado: DEVUELTO
- Cantidad: 30 UNIDAD (20 + 10)
- Bulto 1: 20 UNIDAD
- Bulto 2: 10 UNIDAD
- Movimiento de ALTA registrado

### 9.3 Segunda Devolucion del Mismo Lote

**Situacion:** Otro cliente devuelve unidades del mismo lote que ya tuvo una devolucion previa.

**Resultado:**
- Nuevo lote: UDV-001/25120001_D_2 (segunda devolucion)
- El numero secuencial aumenta automaticamente

### 9.4 Caso Error: No Selecciona Trazas

**Situacion:** El usuario no selecciona ninguna traza para devolver.

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "Seleccione al menos una traza"

### 9.5 Caso Error: Lote en RECALL

**Situacion:** El usuario busca devolver de un lote que esta en recall.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes con estado diferente a RECALL

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿La devolucion de venta afecta el stock del lote original?**
R: No directamente. CU23 crea un nuevo lote con las unidades devueltas. El lote original mantiene sus cantidades.

**P: ¿Que pasa con las unidades devueltas?**
R: Se crea un nuevo lote con estado DEVUELTO. Este lote puede ser analizado (CU2) para determinar si las unidades pueden reintegrarse al stock o deben descartarse.

**P: ¿Por que es un ALTA y no una BAJA?**
R: Porque estamos recibiendo unidades del cliente (ingreso). La venta fue una BAJA (salida).

**P: ¿Puedo devolver mas unidades de las que se vendieron?**
R: No. Solo se muestran las trazas vendidas o las cantidades maximas del movimiento de venta.

### 10.2 Sobre los Lotes

**P: ¿Por que no veo un lote en la lista?**
R: Verificar que el lote:
- Este activo (no eliminado)
- NO este en estado RECALL
- Tenga al menos una traza VENDIDO o un movimiento de VENTA

**P: ¿Que significa el codigo _D_1, _D_2?**
R: Es el sufijo de devolucion. `_D_1` es la primera devolucion, `_D_2` la segunda, etc.

**P: ¿El lote de devolucion hereda el analisis del lote original?**
R: No. El lote de devolucion tiene dictamen DEVOLUCION_CLIENTES y debe ser evaluado por separado si se desea reintegrar al stock.

### 10.3 Sobre las Trazas

**P: ¿Que trazas puedo seleccionar para devolver?**
R: Solo las trazas en estado VENDIDO del movimiento de venta seleccionado.

**P: ¿Puedo devolver parcialmente las trazas de una venta?**
R: Si. Puede seleccionar solo algunas de las trazas vendidas.

### 10.4 Sobre Errores

**P: ¿Que hago si registre una devolucion incorrecta?**
R: Use CU26 (Reverso) para revertir la operacion.

**P: ¿Puedo modificar las trazas devueltas despues de confirmar?**
R: No. Debe usar Reverso y registrar la devolucion nuevamente.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Gerente Garantia Calidad o Administrador
2. **Para que:** Registrar devolucion de productos vendidos por clientes
3. **Tipo de movimiento:** ALTA (ingreso de unidades devueltas)
4. **Motivo del movimiento:** DEVOLUCION_VENTA
5. **Pre-requisito:** Lote con ventas (trazas VENDIDO o movimientos VENTA)
6. **Crea lote:** SI, con codigo `{original}_D_{N}`
7. **Estado del nuevo lote:** DEVUELTO
8. **Dictamen del nuevo lote:** DEVOLUCION_CLIENTES
9. **Modalidades:** Por trazas (lotes trazados) o por cantidades (no trazados)
10. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU23)

| CU | Nombre | Resultado que Habilita |
|----|--------|------------------------|
| CU22 | Venta Producto | Lote con trazas VENDIDO |

### Operaciones Siguientes (despues de CU23)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU2 | Dictamen Cuarentena | Para analizar unidades devueltas |
| CU25 | Ajuste Stock | Si se descartan las unidades |
| CU26 | Reverso | Si se necesita revertir la devolucion |

---

**Fin del Documento - CU23_DEVOLUCION_VENTA v1.0 - Especificacion Funcional**
