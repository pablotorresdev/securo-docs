# CU30 - AJUSTE DE STOCK: Especificacion Funcional

**Version:** 1.2
**Fecha:** 2025-12-06
**Ultima actualizacion:** 2025-12-28
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** BAJA
**Motivo:** AJUSTE

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Ajuste](#4-formulario-de-ajuste)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite **registrar ajustes de inventario** cuando hay discrepancias entre el stock fisico y el stock registrado en el sistema. Los ajustes pueden deberse a mermas, roturas, vencimientos, perdidas, o cualquier otra contingencia que requiera regularizar el stock.

### 1.2 Concepto de Ajuste de Stock

El ajuste de stock:
- **Descuenta cantidad** del bulto y lote seleccionado
- Para lotes trazados, **marca las trazas** como CONSUMIDO
- Actualiza el **estado** del bulto y lote (EN_USO o CONSUMIDO)
- Si el lote queda sin stock, **cancela el analisis en curso** (si existe)
- Registra un **movimiento de BAJA** con motivo AJUSTE

### 1.3 Cuando Usar Este CU

Usar CU30 cuando:
- Hay diferencia entre el stock fisico y el del sistema
- Se detectan mermas, roturas o perdidas
- Producto venció y debe descartarse
- Se necesita regularizar inventario por contingencias

**NO usar CU30 cuando:**
- Se quiere vender producto → usar **CU23 (Venta Producto)**
- Se consume en produccion → usar **CU7 (Consumo Produccion)**
- Se devuelve al proveedor → usar **CU4 (Devolucion Compra)**
- Se retira del mercado → usar **CU25 (Retiro Mercado)**

### 1.4 Resultado del CU

Al completar exitosamente:
- Se **descuenta la cantidad** del bulto y del lote
- Las **trazas seleccionadas** pasan a estado CONSUMIDO (lotes trazados)
- El **bulto** cambia a estado EN_USO o CONSUMIDO
- El **lote** cambia a estado EN_USO o CONSUMIDO (si todos los bultos estan consumidos)
- Se registra un **movimiento de BAJA** con motivo AJUSTE
- Si el lote queda sin stock y tenia **analisis en curso**, este se cancela

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Administrador | SI | Acceso completo al sistema |
| Supervisor de Planta | SI | Usuario principal para ajustes |
| Analista de Planta | SI | Puede registrar ajustes |
| Director Tecnico (DT) | NO | No participa en ajustes |
| Gerente Garantia Calidad | NO | No tiene acceso a contingencias |
| Gerente Control Calidad | NO | No tiene acceso a contingencias |
| Analista Control Calidad | NO | No tiene acceso a contingencias |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar fisicamente la discrepancia de stock
- Seleccionar correctamente el bulto y cantidades a ajustar
- Documentar detalladamente el motivo del ajuste (mínimo 20 caracteres)
- Coordinar con supervision si el ajuste es significativo

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para ajuste de stock:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Con stock** | Debe tener al menos un bulto con cantidad > 0 |

### 3.2 Lotes Elegibles

El sistema muestra todos los lotes activos que tengan al menos un bulto con stock disponible (cantidad actual > 0).

### 3.3 Modalidades de Ajuste

Existen dos modalidades segun el tipo de lote:

| Modalidad | Aplica a | Seleccion |
|-----------|----------|-----------|
| **Por Trazas** | Lotes trazados | Se seleccionan trazas individuales DISPONIBLE |
| **Por Cantidades** | Lotes no trazados | Se indica cantidad y unidad de medida |

---

## 4. Formulario de Ajuste

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Fecha del Ajuste** | SI | Fecha | Fecha del ajuste de stock | Hoy |
| **Lote a Ajustar** | SI | Selector | Lote del cual se ajusta stock | - |
| **Numero del Bulto** | SI | Selector | Bulto especifico a ajustar | - |
| **Trazas a Ajustar** | SI* | Checkboxes | Trazas disponibles a ajustar (lotes trazados) | - |
| **Cantidad a Ajustar** | SI* | Numero | Cantidad a descontar (lotes no trazados) | - |
| **Unidad de Medida** | SI* | Selector | Unidad de la cantidad (lotes no trazados) | - |
| **Motivo del Cambio** | SI | Textarea | Descripcion del motivo del ajuste | - |

*Segun modalidad: trazas para lotes trazados, cantidad/unidad para no trazados.

### 4.2 Informacion del Lote en el Selector

El selector muestra para cada lote elegible:

| Campo | Ejemplo |
|-------|---------|
| Nombre producto | Ibuprofeno 400mg Blister x 20 |
| Lote proveedor | LOTE-2025-001 |
| Dictamen | APROBADO |

### 4.3 Informacion del Bulto

Al seleccionar un bulto, se muestra:

| Campo | Descripcion |
|-------|-------------|
| Cantidad del Bulto | Cantidad actual disponible |
| Unidad de Medida | Unidad de medida del bulto |

### 4.4 Seleccion de Trazas (Lotes Trazados)

Para lotes trazados, se muestran las trazas DISPONIBLE del bulto seleccionado:

```
Trazas Disponibles
  [ ] 50001
  [ ] 50002
  [ ] 50003
```

El usuario selecciona con checkbox las trazas que seran ajustadas (consumidas).

### 4.5 Cantidad y Unidad (Lotes No Trazados)

Para lotes no trazados:

| Campo | Descripcion |
|-------|-------------|
| Cantidad a Ajustar | Numero positivo, maximo = cantidad del bulto |
| Unidad de Medida | Lista de subunidades compatibles con el bulto |

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado." |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado." |
| 3 | Bulto | Debe seleccionar uno | "Bulto no encontrado." |
| 4 | Bulto | Debe existir y estar activo | "Bulto no encontrado." |
| 5 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha del ajuste debe ser igual o posterior a la fecha de ingreso del lote | "La fecha del movimiento no puede ser anterior a la fecha de ingreso del lote" |

### 5.3 Validaciones de Trazas (Lotes Trazados)

| Validacion | Mensaje de Error |
|------------|------------------|
| Debe seleccionar al menos una traza | "Debe seleccionar al menos una traza a ajustar." |

### 5.4 Validaciones de Cantidades (Lotes No Trazados)

| Validacion | Mensaje de Error |
|------------|------------------|
| La cantidad debe ser > 0 | (validacion de formulario) |
| La cantidad no puede exceder la cantidad disponible del bulto | "La cantidad excede el stock disponible del bulto." |
| La unidad debe ser compatible | (validacion de conversion) |

### 5.5 Validaciones Especiales

| Validacion | Mensaje de Error |
|------------|------------------|
| Para trazas, la unidad debe ser UNIDAD | "La traza solo es aplicable a UNIDADES" |
| Para trazas, la cantidad debe ser entera | "La cantidad de Unidades debe ser entero" |

---

## 6. Resultado de la Operacion

### 6.1 Cambios en el Bulto

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Cantidad Actual | (cantidad anterior) | (cantidad anterior - cantidad ajustada) |
| Estado | (anterior) | EN_USO o CONSUMIDO (si cantidad = 0) |

### 6.2 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Cantidad Actual | (cantidad anterior) | (cantidad anterior - cantidad ajustada) |
| Estado | (anterior) | EN_USO o CONSUMIDO (si todos los bultos estan consumidos) |

### 6.3 Cambios en las Trazas (Lotes Trazados)

Para cada traza seleccionada:

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Estado | DISPONIBLE | CONSUMIDO |

### 6.4 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | BAJA |
| Motivo | AJUSTE |
| Lote | Lote ajustado |
| Cantidad | Cantidad ajustada |
| Unidad de Medida | Unidad seleccionada |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha del ajuste indicada |
| Motivo del cambio | Motivo del ajuste ingresado |
| Activo | SI |

### 6.5 Detalle del Movimiento

| Atributo | Valor |
|----------|-------|
| Bulto | Bulto ajustado |
| Cantidad | Cantidad ajustada |
| Unidad de Medida | Unidad seleccionada |
| Trazas | Lista de trazas ajustadas (lotes trazados) |
| Activo | SI |

### 6.6 Cancelacion de Analisis en Curso

Si el lote queda con cantidad = 0 y tenia un analisis en curso (sin dictamen):

| Atributo | Valor |
|----------|-------|
| Dictamen | CANCELADO |

### 6.7 Proteccion de Lotes en RECALL

Si el lote o bulto esta en estado **RECALL** (retiro de mercado), el sistema protege su estado:

| Comportamiento | Descripcion |
|----------------|-------------|
| Estado del bulto | NO se modifica (permanece RECALL) |
| Estado del lote | NO se modifica (permanece RECALL) |
| Cancelacion de analisis | NO se ejecuta automaticamente |
| Cantidad | SI se descuenta normalmente |
| Trazas | SI se marcan como CONSUMIDO (si aplica) |
| Movimiento | SI se registra normalmente |

**Motivo:** Los lotes en proceso de retiro de mercado deben mantener su estado RECALL hasta que se complete el proceso de recall, independientemente de ajustes de stock que se realicen.

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Ajuste Stock] ──────────────────┐
      |                                      |
      | (Vista Previa)                       | (Cancelar)
      |                                      |
      v                                      v
[Pantalla de Confirmacion] ────────┐     [Menu Principal]
      |                            |
      | (Confirmar y Guardar)      | (Volver a Editar)
      |                            |
      v                            v
[Pantalla de Exito]        [Formulario Ajuste Stock]
      |
      | (Nuevo Ajuste) o (Ir a Inicio)
      v
[Formulario Ajuste] o [Menu Principal]
```

### 7.2 Pantalla de Confirmacion

Muestra vista previa de los datos antes de persistir:
- Datos del lote: Codigo interno, producto, proveedor
- Datos del ajuste: Fecha, bulto seleccionado
- Cantidad y unidad (lotes no trazados) o trazas seleccionadas (lotes trazados)
- Motivo del cambio

Opciones: "Volver a Editar" (mantiene los datos) o "Confirmar y Guardar"

### 7.3 Pantalla de Exito

Muestra:
- Mensaje de exito: "Ajuste registrado correctamente."
- Datos del lote ajustado
- Bulto afectado
- Trazas ajustadas (si aplica)

Opciones: "Registrar otro ajuste" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU30

| CU | Nombre | Cuando Aplicar |
|----|--------|----------------|
| CU31 | Reverso | Si el ajuste fue incorrecto |

### 8.2 Flujo Tipico Post-Ajuste

```
CU30: Ajuste Stock
         |
         | Cantidad descontada
         v
    +----+----+
    |         |
    v         v
Lote con   Lote sin
stock      stock
    |         |
    v         v
Continua   CU31 (si error)
operando   o fin
```

---

## 9. Casos de Uso Ejemplos

### 9.1 Ajuste de Lote Trazado

**Situacion:** Se detectan 3 unidades danadas en un lote trazado.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Fecha del Ajuste | 06/12/2025 |
| Lote | UDV-001/25120001 |
| Bulto | Bulto #1 - Disponible: 50 UNIDAD |
| Trazas seleccionadas | 50001, 50002, 50003 |
| Motivo | Unidades danadas por caida de estanteria - Incidente #INC-2025-001 |

**Resultado:**
- Bulto #1: Cantidad 47 UNIDAD (50 - 3)
- Lote: Cantidad reducida en 3
- Trazas 50001, 50002, 50003: Estado CONSUMIDO
- Movimiento de BAJA con motivo AJUSTE registrado

### 9.2 Ajuste de Lote No Trazado

**Situacion:** Merma de 2.5 kg en un lote de materia prima.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Fecha del Ajuste | 06/12/2025 |
| Lote | 1-05701_25110010 |
| Bulto | Bulto #1 - Disponible: 25 KILOGRAMO |
| Cantidad a Ajustar | 2.5 |
| Unidad de Medida | KILOGRAMO |
| Motivo | Merma por evaporacion detectada en control rutinario - Lote muy antiguo |

**Resultado:**
- Bulto #1: Cantidad 22.5 KILOGRAMO (25 - 2.5)
- Lote: Cantidad reducida en 2.5 kg
- Estado del bulto: EN_USO
- Movimiento de BAJA registrado

### 9.3 Ajuste que Consume Completamente el Bulto

**Situacion:** Se ajustan todas las unidades restantes de un bulto.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Bulto | Bulto #3 - Disponible: 5 UNIDAD |
| Cantidad a Ajustar | 5 |
| Unidad de Medida | UNIDAD |
| Motivo | Vencimiento proximo - Destruccion por protocolo de calidad interno |

**Resultado:**
- Bulto #3: Cantidad 0 UNIDAD, Estado CONSUMIDO
- Si era el ultimo bulto con stock, el lote pasa a estado CONSUMIDO
- Si habia analisis en curso, queda con dictamen CANCELADO

### 9.4 Caso Error: Cantidad Excede Disponible

**Situacion:** Se intenta ajustar mas de lo disponible.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Bulto | Bulto #1 - Disponible: 10 UNIDAD |
| Cantidad a Ajustar | 15 |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "La cantidad excede el stock disponible del bulto."

### 9.5 Caso Error: No Selecciona Trazas

**Situacion:** Usuario no selecciona ninguna traza en un lote trazado.

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "Debe seleccionar al menos una traza a ajustar."

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿El ajuste de stock es reversible?**
R: Si, mediante CU31 (Reverso Movimiento) se puede revertir el ajuste.

**P: ¿Que pasa si el lote queda sin stock?**
R: El lote pasa a estado CONSUMIDO. Si tenia un analisis en curso, este se cancela automaticamente.

**P: ¿Puedo ajustar en una unidad diferente al bulto?**
R: Si, puede usar subunidades compatibles. El sistema convierte automaticamente (ej: gramos en un bulto de kilogramos).

**P: ¿Por que el motivo es obligatorio?**
R: Es requerimiento de ANMAT Disposicion 4159 documentar todos los ajustes de inventario para trazabilidad.

### 10.2 Sobre los Lotes

**P: ¿Por que no veo un lote en la lista?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Tenga al menos un bulto con cantidad > 0

**P: ¿Puedo ajustar de cualquier bulto?**
R: Si, puede seleccionar cualquier bulto del lote que tenga stock disponible.

### 10.3 Sobre las Trazas

**P: ¿Que trazas puedo seleccionar?**
R: Solo las trazas en estado DISPONIBLE del bulto seleccionado.

**P: ¿Que pasa con las trazas ajustadas?**
R: Pasan a estado CONSUMIDO y quedan vinculadas al movimiento de ajuste.

### 10.4 Sobre Errores

**P: ¿Que hago si registre un ajuste incorrecto?**
R: Use CU31 (Reverso) para revertir la operacion.

**P: ¿Puedo modificar el ajuste despues de confirmado?**
R: No directamente. Debe reversar y registrar nuevamente.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Administrador, Supervisor de Planta, Analista de Planta
2. **Para que:** Regularizar diferencias de inventario
3. **Tipo de movimiento:** BAJA
4. **Motivo del movimiento:** AJUSTE
5. **Pre-requisito:** Lote con bulto que tenga stock > 0
6. **Descuenta stock:** SI, del bulto y lote
7. **Afecta trazas:** SI, pasan a CONSUMIDO (lotes trazados)
8. **Cancela analisis:** SI, si el lote queda sin stock (excepto lotes en RECALL)
9. **Modalidades:** Por trazas (lotes trazados) o por cantidades (no trazados)
10. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)
11. **Proteccion RECALL:** Los lotes/bultos en RECALL mantienen su estado

---

## Relacion con Otros CUs

### Operaciones que Generan Stock (que CU30 puede ajustar)

| CU | Nombre | Tipo de Stock |
|----|--------|---------------|
| CU1 | Alta Ingreso Compra | Compras |
| CU20 | Ingreso Produccion | Produccion |
| CU24 | Devolucion Venta | Devoluciones |
| CU25 | Retiro Mercado | Recalls |

### Operaciones Siguientes (despues de CU30)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU31 | Reverso | Si se necesita revertir el ajuste |

---

**Fin del Documento - CU30_AJUSTE_STOCK v1.2 - Especificacion Funcional**
