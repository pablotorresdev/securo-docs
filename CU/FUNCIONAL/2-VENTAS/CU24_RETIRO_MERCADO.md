# CU24 - RETIRO DE MERCADO (RECALL): Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** ALTA + MODIFICACION
**Motivo:** RETIRO_MERCADO

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Retiro](#4-formulario-de-retiro)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite **registrar el retiro de productos del mercado (recall)** cuando se detecta un problema de calidad, seguridad o cumplimiento normativo. El recall es un proceso critico que retira productos vendidos y los pone en cuarentena para su evaluacion.

### 1.2 Concepto de Retiro de Mercado

El retiro de mercado (recall) ejecuta **DOS operaciones simultaneas**:

**Operacion 1 - ALTA:**
- Crea un **nuevo lote derivado** con las unidades retiradas (sufijo `_R_N`)
- El nuevo lote tiene estado **RECALL** y dictamen **RETIRO_MERCADO**
- Las trazas de las unidades retiradas se transfieren al nuevo lote

**Operacion 2 - MODIFICACION:**
- Cambia el estado del **lote original** a RECALL
- Marca los bultos con stock disponible como RECALL
- Cambia las trazas DISPONIBLE del lote original a RECALL
- **Cancela el analisis en curso** si existe uno sin dictamen

### 1.3 Cuando Usar Este CU

Usar CU24 cuando:
- Se detecta un defecto de calidad en productos vendidos
- Hay una alerta sanitaria que requiere retiro del mercado
- Autoridades regulatorias (ANMAT) ordenan el retiro
- Se identifica riesgo para la salud del consumidor

**NO usar CU24 cuando:**
- El cliente devuelve por motivos comerciales -> usar **CU23 (Devolucion Venta)**
- Se quiere devolver al proveedor -> usar **CU4 (Devolucion Compra)**
- El lote ya esta completamente en RECALL

### 1.4 Resultado del CU

Al completar exitosamente:
- Se crea un **nuevo lote** con codigo `{codigoOriginal}_R_{N}` (N = numero secuencial)
- El nuevo lote tiene estado **RECALL** y dictamen **RETIRO_MERCADO**
- El **lote original** cambia su estado a **RECALL**
- Las **trazas DISPONIBLE** del lote original pasan a estado RECALL
- Las **trazas VENDIDO** seleccionadas se transfieren al nuevo lote con estado RECALL
- Se registra un **movimiento de ALTA** con motivo RETIRO_MERCADO
- Se registra un **movimiento de MODIFICACION** con motivo RETIRO_MERCADO
- Si habia un **analisis en curso**, queda con dictamen **CANCELADO**

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Gerente Garantia Calidad | SI | Usuario principal para recalls |
| Administrador | SI | Acceso completo al sistema |
| Director Tecnico (DT) | NO | No participa directamente |
| Gerente Control Calidad | NO | No tiene acceso a recalls |
| Analista Control Calidad | NO | No tiene acceso a recalls |
| Supervisor de Planta | NO | No tiene acceso a ventas |
| Analista de Planta | NO | No tiene acceso a ventas |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Evaluar la gravedad del problema que motiva el recall
- Identificar todos los lotes afectados
- Coordinar con clientes el retiro de unidades
- Documentar el motivo del recall para trazabilidad
- Notificar a las autoridades regulatorias si corresponde

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para recall:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Con ventas** | Debe tener trazas VENDIDO o movimientos de VENTA |

### 3.2 Lotes Elegibles

El sistema muestra lotes que cumplan **alguna** de estas condiciones:

| Condicion | Descripcion |
|-----------|-------------|
| Trazas VENDIDO | Tiene al menos una traza en estado VENDIDO |
| Movimiento VENTA | Tiene al menos un movimiento con motivo VENTA |

**Nota:** A diferencia de CU23 (Devolucion Venta), el recall NO excluye lotes ya en estado RECALL. Esto permite hacer recalls parciales o adicionales.

### 3.3 Modalidades de Retiro

Existen dos modalidades segun el tipo de lote:

| Modalidad | Aplica a | Seleccion |
|-----------|----------|-----------|
| **Por Trazas** | Lotes trazados | Se seleccionan trazas individuales VENDIDO |
| **Por Cantidades** | Lotes no trazados | Se indican cantidades por bulto |

---

## 4. Formulario de Retiro

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote** | SI | Selector | Lote del cual se retiran unidades | - |
| **Movimiento de Venta** | SI | Selector | Movimiento de venta origen | - |
| **Trazas a Retirar** | SI* | Checkboxes | Trazas vendidas a retirar (lotes trazados) | - |
| **Cantidades por Bulto** | SI* | Numeros | Cantidades a retirar por bulto (lotes no trazados) | 0 |
| **Fecha del Recall** | SI | Fecha | Fecha del retiro de mercado | Hoy |
| **Motivo del Recall** | SI | Textarea | Descripcion del motivo (minimo 20 caracteres) | - |

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
[x] Seleccionar todas

Bulto 1
  [ ] Traza 50001
  [ ] Traza 50002
  [ ] Traza 50003

Bulto 2
  [ ] Traza 50101
  [ ] Traza 50102
```

El usuario selecciona con checkbox las trazas que seran retiradas del mercado.

### 4.5 Cantidades por Bulto (Lotes No Trazados)

Para lotes no trazados, se muestran campos numericos por bulto:

| Bulto | Cantidad Maxima | Cantidad a Retirar |
|-------|-----------------|---------------------|
| Bulto 1 | 50 | [____] |
| Bulto 2 | 50 | [____] |

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado." |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado." |
| 3 | Movimiento Origen | Debe seleccionar uno | "No se encontro el movimiento de venta origen" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha del recall debe ser igual o posterior a la fecha de ingreso del lote | "La fecha del movmiento no puede ser anterior a la fecha de ingreso del lote" |
| La fecha del recall debe ser igual o posterior a la fecha del movimiento de venta | "La fecha de devolución no puede ser anterior a la fecha del movimiento de venta." |

### 5.3 Validaciones de Trazas (Lotes Trazados)

| Validacion | Mensaje de Error |
|------------|------------------|
| Debe seleccionar al menos una traza | "Debe seleccionar al menos una traza para devolver." |
| Las trazas deben estar en estado VENDIDO | (implicito - solo se muestran vendidas) |

### 5.4 Validaciones de Cantidades (Lotes No Trazados)

| Validacion | Mensaje de Error |
|------------|------------------|
| Debe ingresar al menos una cantidad > 0 | "Ingrese al menos una cantidad valida (> 0) y no superior al maximo permitido." |
| Cantidad no puede exceder el maximo | "Ingrese al menos una cantidad valida (> 0) y no superior al maximo permitido." |

---

## 6. Resultado de la Operacion

### 6.1 Nuevo Lote Recall Creado (ALTA)

| Atributo | Valor |
|----------|-------|
| Codigo Lote | `{codigoOriginal}_R_{N}` |
| Lote Origen | Referencia al lote original |
| Estado | RECALL |
| Dictamen | RETIRO_MERCADO |
| Unidad de Medida | UNIDAD |
| Cantidad Inicial | Total de unidades retiradas |
| Cantidad Actual | Total de unidades retiradas |
| Trazado | Mismo valor que el lote original |
| Producto | Mismo del lote original |
| Proveedor | Mismo del lote original |
| Fabricante | Mismo del lote original |
| Activo | SI |

**Ejemplo de codigo:** Si el lote original es `UDV-001/25120001` y es el primer recall, el nuevo lote sera `UDV-001/25120001_R_1`.

### 6.2 Bultos del Nuevo Lote

Para cada grupo de trazas o cantidades retiradas:

| Atributo | Valor |
|----------|-------|
| Numero de Bulto | Numero del bulto original |
| Cantidad Inicial | Cantidad retirada de ese bulto |
| Cantidad Actual | Cantidad retirada de ese bulto |
| Unidad de Medida | UNIDAD |
| Estado | RECALL |
| Activo | SI |

### 6.3 Cambios en las Trazas Seleccionadas (Lotes Trazados)

Para cada traza seleccionada para recall:

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Lote | Lote original | Nuevo lote recall |
| Bulto | Bulto original | Bulto del nuevo lote |
| Estado | VENDIDO | RECALL |

### 6.4 Movimiento de ALTA Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | ALTA |
| Motivo | RETIRO_MERCADO |
| Lote | Nuevo lote de recall |
| Movimiento Origen | Movimiento de venta seleccionado |
| Cantidad | Total de unidades retiradas |
| Unidad de Medida | UNIDAD |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha del recall indicada |
| Motivo del cambio | Motivo del recall ingresado |
| Activo | SI |

### 6.5 Cambios en el Lote Original (MODIFICACION)

Si el lote original **no estaba ya en RECALL**:

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Estado | (anterior) | RECALL |

### 6.6 Cambios en Trazas DISPONIBLE del Lote Original

Para cada traza en estado DISPONIBLE del lote original:

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Estado | DISPONIBLE | RECALL |

### 6.7 Cambios en Bultos del Lote Original

Bultos que contenian trazas DISPONIBLE o tenian stock:

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Estado | (anterior) | RECALL |

### 6.8 Movimiento de MODIFICACION Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION |
| Motivo | RETIRO_MERCADO |
| Lote | Lote original de venta |
| Movimiento Origen | Movimiento de venta seleccionado |
| Dictamen Inicial | Dictamen anterior del lote |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha del recall indicada |
| Motivo del cambio | Motivo del recall ingresado |
| Activo | SI |

### 6.9 Cancelacion de Analisis en Curso

Si el lote original tenia un analisis en curso (sin dictamen), este queda con:

| Atributo | Valor |
|----------|-------|
| Dictamen | CANCELADO |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Retiro Mercado] ────────────────────┐
      |                                          |
      | (Seleccionar lote, movimiento, trazas)   | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Confirmacion] ◄──────────────  [Menu Principal]
      |
      | (Confirmar)
      v
[Pantalla de Exito]
      |
      | (Nuevo Recall) o (Ir a Inicio)
      v
[Formulario Retiro] o [Menu Principal]
```

### 7.2 Pantalla de Confirmacion

Antes de procesar, se muestra una vista previa con:
- Datos del lote original
- Producto y codigo
- Movimiento de venta seleccionado
- Resumen de trazas/cantidades a retirar
- Motivo del recall

El usuario debe confirmar para proceder.

### 7.3 Pantalla de Exito

Muestra:
- Mensaje de exito
- Codigo del nuevo lote de recall
- Producto
- Cantidad retirada
- Estado: RECALL
- Dictamen: RETIRO_MERCADO

Opciones: "Registrar otro recall" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU24

| CU | Nombre | Cuando Aplicar |
|----|--------|----------------|
| CU2 | Dictamen Cuarentena | Para analizar las unidades retiradas |
| CU26 | Reverso | Si el recall fue incorrecto |
| CU25 | Ajuste Stock | Para destruir unidades defectuosas |

### 8.2 Flujo Tipico Post-Recall

```
CU24: Retiro Mercado
         |
         | Nuevo lote con estado RECALL
         | Lote original en estado RECALL
         v
    +----+----+----+
    |         |    |
    v         v    v
Analizar   Destruir  Documentar
Unidades   Unidades  para ANMAT
    |         |
    v         v
CU2:       CU25:
Dictamen   Ajuste
Cuarentena Stock
```

### 8.3 Diferencia con Devolucion Venta

| Aspecto | Retiro Mercado (CU24) | Devolucion Venta (CU23) |
|---------|------------------------|-------------------------|
| Tipo operacion | ALTA + MODIFICACION | ALTA |
| Crea lote | SI (lote derivado `_R_N`) | SI (lote derivado `_D_N`) |
| Modifica lote original | SI (estado RECALL) | NO |
| Estado del nuevo lote | RECALL | DEVUELTO |
| Dictamen del nuevo lote | RETIRO_MERCADO | DEVOLUCION_CLIENTES |
| Cancela analisis en curso | SI | NO |
| Motivo obligatorio | NO | SI (20 caracteres) |
| Trazas DISPONIBLE afectadas | SI (pasan a RECALL) | NO |

---

## 9. Casos de Uso Ejemplos

### 9.1 Recall de Lote Trazado

**Situacion:** Se detecta un defecto de fabricacion en lote vendido. Se deben retirar 5 unidades vendidas.

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
| Fecha Recall | 06/12/2025 |
| Motivo del Recall | Defecto de fabricacion detectado en control interno - Referencia: RECALL-2025-001 |

**Resultado:**
1. **Nuevo lote creado:** UDV-001/25120001_R_1
   - Estado: RECALL
   - Dictamen: RETIRO_MERCADO
   - Cantidad: 5 UNIDAD
   - Trazas 50001-50005: Estado RECALL

2. **Lote original modificado:** UDV-001/25120001
   - Estado: RECALL (era el anterior)
   - Trazas DISPONIBLE restantes: Estado RECALL

3. **Movimientos registrados:**
   - ALTA con motivo RETIRO_MERCADO (nuevo lote)
   - MODIFICACION con motivo RETIRO_MERCADO (lote original)

### 9.2 Recall de Lote No Trazado

**Situacion:** Recall de 30 unidades de un lote no trazado por alerta sanitaria.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-002/25110050 |
| Producto | Jarabe Pediatrico 100ml |
| Trazado | NO |

**Cantidades por bulto:**
| Bulto | Cantidad Vendida | Cantidad a Retirar |
|-------|------------------|---------------------|
| Bulto 1 | 50 | 20 |
| Bulto 2 | 50 | 10 |

**Datos adicionales:**
| Campo | Valor |
|-------|-------|
| Fecha Recall | 06/12/2025 |
| Motivo del Recall | Alerta ANMAT N° 1234/2025 |

**Resultado:**
- Nuevo lote: UDV-002/25110050_R_1
- Estado: RECALL
- Cantidad: 30 UNIDAD (20 + 10)
- Lote original: Estado RECALL
- Movimientos de ALTA y MODIFICACION registrados

### 9.3 Segundo Recall del Mismo Lote

**Situacion:** Otro cliente reporta problemas con el mismo lote que ya tuvo un recall.

**Resultado:**
- Nuevo lote: UDV-001/25120001_R_2 (segundo recall)
- El numero secuencial aumenta automaticamente
- El lote original ya estaba en RECALL, no se modifica nuevamente

### 9.4 Recall con Analisis en Curso

**Situacion:** El lote tiene un analisis en curso (sin dictamen) cuando se inicia el recall.

**Resultado:**
- El analisis en curso queda con dictamen **CANCELADO**
- El lote pasa a estado RECALL
- No se puede completar el analisis despues del recall

### 9.5 Caso Error: No Selecciona Trazas

**Situacion:** El usuario no selecciona ninguna traza para retirar.

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "Debe seleccionar al menos una traza para devolver."

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿Por que el recall ejecuta DOS operaciones?**
R: Porque el recall tiene dos efectos: (1) crear un lote con las unidades retiradas del mercado, y (2) bloquear el lote original para evitar nuevas ventas.

**P: ¿Que pasa con las unidades que quedaron sin vender?**
R: Las trazas DISPONIBLE del lote original pasan a estado RECALL. No se pueden vender unidades de un lote en RECALL.

**P: ¿El motivo del recall es obligatorio?**
R: No es obligatorio en el formulario, pero es altamente recomendable documentarlo para cumplimiento regulatorio y trazabilidad ante ANMAT.

**P: ¿Que diferencia hay con la devolucion de venta?**
R: La devolucion (CU23) es por motivos comerciales y solo crea un lote nuevo. El recall (CU24) es por seguridad/calidad, crea un lote nuevo Y bloquea el lote original.

### 10.2 Sobre los Lotes

**P: ¿Por que no veo un lote en la lista?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Tenga al menos una traza VENDIDO o un movimiento de VENTA

**P: ¿Que significa el codigo _R_1, _R_2?**
R: Es el sufijo de recall. `_R_1` es el primer recall, `_R_2` el segundo, etc.

**P: ¿Puedo hacer recall de un lote que ya esta en RECALL?**
R: Si. Puede seleccionar trazas VENDIDO adicionales. El lote original no cambia (ya esta en RECALL).

### 10.3 Sobre el Analisis en Curso

**P: ¿Que pasa si hay un analisis en curso?**
R: El analisis queda con dictamen CANCELADO. No se puede completar un analisis de un lote en recall.

**P: ¿Se puede analizar el lote despues del recall?**
R: Se pueden analizar las unidades del nuevo lote recall (CU2), pero el lote original queda bloqueado.

### 10.4 Sobre Errores

**P: ¿Que hago si registre un recall incorrecto?**
R: Use CU26 (Reverso) para revertir la operacion.

**P: ¿Puedo modificar las trazas retiradas despues de confirmar?**
R: No. Debe usar Reverso y registrar el recall nuevamente.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Gerente Garantia Calidad o Administrador
2. **Para que:** Retirar productos del mercado por problemas de calidad/seguridad
3. **Tipo de movimiento:** ALTA + MODIFICACION
4. **Motivo del movimiento:** RETIRO_MERCADO
5. **Pre-requisito:** Lote con ventas (trazas VENDIDO o movimientos VENTA)
6. **Crea lote:** SI, con codigo `{original}_R_{N}`
7. **Estado del nuevo lote:** RECALL
8. **Dictamen del nuevo lote:** RETIRO_MERCADO
9. **Modifica lote original:** SI, estado RECALL
10. **Cancela analisis en curso:** SI, dictamen CANCELADO
11. **Modalidades:** Por trazas (lotes trazados) o por cantidades (no trazados)
12. **Motivo del cambio:** Opcional pero recomendado

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU24)

| CU | Nombre | Resultado que Habilita |
|----|--------|------------------------|
| CU22 | Venta Producto | Lote con trazas VENDIDO |

### Operaciones Siguientes (despues de CU24)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU2 | Dictamen Cuarentena | Para analizar unidades retiradas |
| CU25 | Ajuste Stock | Para destruir unidades defectuosas |
| CU26 | Reverso | Si se necesita revertir el recall |

---

**Fin del Documento - CU24_RETIRO_MERCADO v1.0 - Especificacion Funcional**
