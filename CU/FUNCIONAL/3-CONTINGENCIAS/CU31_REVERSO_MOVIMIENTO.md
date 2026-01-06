# CU31 - REVERSO DE MOVIMIENTO: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** Variable (segun movimiento original)
**Motivo:** REVERSO

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Reverso](#4-formulario-de-reverso)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Movimientos Reversables](#8-movimientos-reversables)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite **revertir el ultimo movimiento registrado** de un lote. El reverso deshace los efectos del movimiento original, restaurando el estado previo del lote, bultos y trazas.

### 1.2 Concepto de Reverso

El reverso de movimiento:
- **Deshace los efectos** del movimiento original
- **Restaura cantidades** de bultos y lotes
- **Restaura estados** de trazas, bultos y lotes
- **Marca el movimiento original** como inactivo
- **Registra un movimiento de reverso** para auditoria
- Respeta **reglas de autorizacion** basadas en jerarquia

### 1.3 Cuando Usar Este CU

Usar CU31 cuando:
- Se registro un movimiento por error
- Se necesita corregir una operacion incorrecta
- Se detecto un error de datos posterior al registro

**NO usar CU31 cuando:**
- Se quiere ajustar stock → usar **CU30 (Ajuste Stock)**
- El movimiento es de vencimiento (CU10) → irreversible
- El movimiento es de expiracion de analisis (CU9) → irreversible
- El lote tiene un recall asociado (MODIFICACION con motivo RETIRO_MERCADO)

### 1.4 Resultado del CU

Al completar exitosamente:
- El **movimiento original** se marca como inactivo
- Las **cantidades** del lote y bultos se restauran
- Los **estados** de trazas, bultos y lote se restauran
- Se registra un **movimiento de reverso** para trazabilidad
- El lote queda en el **estado anterior** al movimiento reversado

---

## 2. Perfiles de Usuario

### 2.1 Autorizacion por Jerarquia y Areas

El CU31 tiene un sistema de autorizacion basado en **jerarquia de roles** y **areas funcionales**:

| Regla | Descripcion |
|-------|-------------|
| **Creador** | El usuario que creo el movimiento SIEMPRE puede reversarlo |
| **GLOBAL Superior** | ADMIN y DT pueden reversar movimientos de CUALQUIER area |
| **Misma Area** | Solo pueden reversar si tienen nivel SUPERIOR dentro de la MISMA area |
| **Areas Diferentes** | CALIDAD y PLANTA NO tienen jerarquia entre si |
| **Auditor** | AUDITOR NUNCA puede reversar (rol de solo lectura) |
| **Legacy** | Movimientos sin creador (legacy): solo ADMIN puede reversar |

### 2.2 Jerarquia de Roles por Area

**Area GLOBAL** (superiores/inferiores a todos):

| Rol | Nivel | Puede Reversar |
|-----|-------|----------------|
| ADMIN | 6 | Todos los movimientos de cualquier area |
| DT | 5 | Propios y de cualquier area con nivel inferior |
| AUDITOR | 1 | NINGUNO (solo lectura) |

**Area CALIDAD** (jerarquia interna):

| Rol | Nivel | Puede Reversar |
|-----|-------|----------------|
| GERENTE_GARANTIA_CALIDAD | 4 | Propios y de CALIDAD con nivel inferior |
| GERENTE_CONTROL_CALIDAD | 3 | Propios y de CALIDAD con nivel inferior |
| ANALISTA_CONTROL_CALIDAD | 2 | Solo propios |

**Area PLANTA** (jerarquia interna):

| Rol | Nivel | Puede Reversar |
|-----|-------|----------------|
| SUPERVISOR_PLANTA | 3 | Propios y de PLANTA con nivel inferior |
| ANALISTA_PLANTA | 2 | Solo propios |

**Importante:** Un GERENTE_CONTROL_CALIDAD (area CALIDAD) NO puede reversar movimientos de un ANALISTA_PLANTA (area PLANTA) y viceversa, aunque tengan nivel superior.

### 2.3 Quien Puede Acceder a la Pantalla

| Perfil | Puede Acceder | Puede Reversar |
|--------|---------------|----------------|
| Administrador | SI | Todos los movimientos |
| Director Tecnico (DT) | SI | Segun jerarquia |
| Gerente Garantia Calidad | SI | Segun jerarquia |
| Gerente Control Calidad | SI | Segun jerarquia |
| Supervisor de Planta | SI | Segun jerarquia |
| Analista Control Calidad | SI | Solo propios |
| Analista de Planta | SI | Solo propios |
| Auditor | NO | Nunca |

### 2.4 Responsabilidades del Usuario

- Verificar que el reverso es necesario y justificado
- Documentar el motivo del reverso (mínimo 20 caracteres)
- Notificar al supervisor si el reverso afecta a otro usuario
- Registrar incidencia si corresponde

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para reverso:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Dictamen valido** | No puede tener dictamen VENCIDO ni ANALISIS_EXPIRADO |
| **Con movimientos** | Debe tener al menos un movimiento activo |

### 3.2 Requisitos del Movimiento

Para que un movimiento pueda ser reversado:

| Condicion | Descripcion |
|-----------|-------------|
| **Unico** | Debe existir exactamente un movimiento con ese codigo |
| **Activo** | El movimiento debe estar activo |
| **Tipo soportado** | El tipo/motivo debe ser soportado para reverso |
| **Autorizacion** | El usuario debe tener permiso segun jerarquia |

### 3.3 Lotes Elegibles

El sistema muestra lotes que cumplan:
- Activo = true
- Dictamen diferente de VENCIDO
- Dictamen diferente de ANALISIS_EXPIRADO

---

## 4. Formulario de Reverso

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote** | SI | Selector | Lote del cual se reversara movimiento | - |
| **Movimiento a Revertir** | SI (auto) | Texto readonly | Ultimo movimiento del lote | Automatico |
| **Fecha del Reverso** | SI | Fecha (hidden) | Fecha del reverso | Hoy |
| **Motivo del Cambio** | SI | Textarea | Descripcion del motivo | - |

### 4.2 Informacion del Lote en el Selector

El selector muestra para cada lote elegible:

| Campo | Ejemplo |
|-------|---------|
| Nombre producto | Ibuprofeno 400mg Blister x 20 |
| Codigo | UDV-001/25120001 |
| Estado | DISPONIBLE |
| Trazas | 50001 / 51000 |

### 4.3 Informacion del Movimiento

Al seleccionar un lote, se muestra automaticamente el ultimo movimiento:

| Campo | Descripcion |
|-------|-------------|
| Fecha | Fecha del movimiento |
| Tipo | ALTA, BAJA o MODIFICACION |
| Motivo | Motivo del movimiento |
| Cantidad | Cantidad y unidad de medida |
| Codigo | Codigo del movimiento |

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | (validacion de formulario) |
| 2 | Movimiento | Debe existir el movimiento | "El Movimiento no existe." |
| 3 | Movimiento | Debe ser unico | "Cantidad incorrecta de movimientos" |
| 4 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

### 5.2 Validaciones de Autorizacion

| Validacion | Mensaje de Error |
|------------|------------------|
| Auditor intenta reversar | "Los auditores no tienen permisos para reversar movimientos. El rol AUDITOR es de solo lectura." |
| Movimiento legacy sin permisos | "No tiene permisos para reversar este movimiento legacy. El movimiento no tiene usuario asignado (datos anteriores al sistema de tracking). Solo el administrador puede reversar movimientos legacy. Su rol: {rol} (nivel {nivel})." |
| Usuario sin jerarquia suficiente | "No tiene permisos para reversar este movimiento. Fue creado por {usuario} (rol: {rol}, nivel {nivel}). Su rol es {rol_actual} (nivel {nivel_actual}). Solo puede reversarlo el usuario que lo creó o un superior jerárquico." |

### 5.3 Validaciones de Tipo de Movimiento

| Validacion | Mensaje de Error |
|------------|------------------|
| Reverso de VENCIMIENTO (CU10) | "No se puede reversar un vencimiento de lote (CU10). El vencimiento es un proceso automático irreversible." |
| Reverso de EXPIRACION_ANALISIS (CU9) | "No se puede reversar una expiración de análisis (CU9). La expiración es un proceso automático." |
| Reverso de RETIRO_MERCADO (MODIF) | "El lote origen tiene un recall asociado, no se puede reversar el movimiento." |

---

## 6. Resultado de la Operacion

### 6.1 Efecto General

| Aspecto | Cambio |
|---------|--------|
| Movimiento original | Marcado como inactivo |
| Lote | Restaurado al estado previo |
| Bultos | Cantidades y estados restaurados |
| Trazas | Estados restaurados |
| Nuevo movimiento | Registrado para auditoria |

### 6.2 Segun Tipo de Movimiento Original

#### Reverso de ALTA

| Elemento | Efecto |
|----------|--------|
| Lote | Se elimina logicamente (si es CU1/CU20) |
| Lote derivado | Se elimina logicamente (si es CU24/CU25) |
| Bultos | Se eliminan logicamente |
| Trazas | Se eliminan logicamente o vuelven al lote original |
| Cantidades | Se revierten |

#### Reverso de BAJA

| Elemento | Efecto |
|----------|--------|
| Lote | Cantidad restaurada |
| Bultos | Cantidad restaurada |
| Trazas | Estado restaurado (DISPONIBLE en vez de VENDIDO/CONSUMIDO) |
| Estados | Restaurados al previo |

#### Reverso de MODIFICACION

| Elemento | Efecto |
|----------|--------|
| Lote | Dictamen restaurado al anterior |
| Analisis | Restaurado si fue afectado |
| Trazas | Estados restaurados |

### 6.3 Movimiento de Reverso Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION (o segun caso) |
| Motivo | REVERSO |
| Lote | Lote afectado |
| Movimiento Origen | Referencia al movimiento reversado |
| Usuario | Usuario que realizo el reverso |
| Fecha | Fecha del reverso |
| Motivo del cambio | Motivo del reverso ingresado |
| Activo | SI |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Reverso] ────────────────────────┐
      |                                       |
      | (Seleccionar lote)                    | (Cancelar)
      |                                       |
      v                                       v
[Se muestra ultimo movimiento]          [Menu Principal]
      |
      | (Completar formulario y confirmar)
      v
[Validacion de Autorizacion]
      |
      +──────────────────────────────+
      |                              |
      v                              v
[Autorizado]               [No Autorizado]
      |                              |
      v                              v
[Pantalla de Exito]        [Mensaje de Error]
      |                              |
      v                              v
[Menu Principal]           [Formulario Reverso]
```

### 7.2 Pantalla de Error de Autorizacion

Si el usuario no tiene permisos, se muestra:
- Icono de no autorizado
- Mensaje descriptivo explicando por que no puede reversar
- Informacion del creador del movimiento y su rol
- Informacion del rol del usuario actual

### 7.3 Pantalla de Exito

Muestra:
- Mensaje de exito: "Reverso de Movimiento exitoso"
- Datos del lote afectado

---

## 8. Movimientos Reversables

### 8.1 Movimientos de ALTA

| CU | Motivo | Servicio de Reverso | Efecto |
|----|--------|---------------------|--------|
| CU1 | COMPRA | ReversoAltaService.reversarAltaIngresoCompra | Elimina lote creado |
| CU20 | PRODUCCION_PROPIA | ReversoAltaService.reversarAltaIngresoProduccion | Elimina lote creado |
| CU24 | DEVOLUCION_VENTA | ReversoAltaService.reversarAltaDevolucionVenta | Elimina lote derivado, restaura trazas |
| CU25 | RETIRO_MERCADO (ALTA) | ReversoAltaService.reversarRetiroMercado | Elimina lote recall, restaura trazas |

### 8.2 Movimientos de BAJA

| CU | Motivo | Servicio de Reverso | Efecto |
|----|--------|---------------------|--------|
| CU3 | MUESTREO | ReversoBajaService.reversarBajaMuestreoBulto | Restaura cantidades muestreadas |
| CU4 | DEVOLUCION_COMPRA | ReversoBajaService.reversarBajaDevolucionCompra | Restaura cantidades devueltas |
| CU7 | CONSUMO_PRODUCCION | ReversoBajaService.reversarBajaConsumoProduccion | Restaura cantidades consumidas |
| CU23 | VENTA | ReversoBajaService.reversarBajaVentaProducto | Restaura cantidades vendidas, trazas a DISPONIBLE |
| CU30 | AJUSTE | ReversoBajaService.reversarBajaAjuste | Restaura cantidades ajustadas |

### 8.3 Movimientos de MODIFICACION

| CU | Motivo | Servicio de Reverso | Efecto |
|----|--------|---------------------|--------|
| CU2/CU8 | ANALISIS | ReversoModificacionService.reversarModifDictamenCuarentena | Restaura dictamen anterior |
| CU5/CU6 | RESULTADO_ANALISIS | ReversoModificacionService.reversarModifResultadoAnalisis | Restaura dictamen anterior del analisis |
| CU11 | ANULACION_ANALISIS | ReversoModificacionService.reversarAnulacionAnalisis | Reactiva analisis anulado |
| CU22 | LOTE_LIBERADO | ReversoModificacionService.reversarModifConfirmarLoteLiberado | Revierte liberacion |
| CU21 | TRAZADO | ReversoModificacionService.reversarModifTrazadoLote | Revierte asignacion de trazas |

### 8.4 Movimientos NO Reversables

| CU | Motivo | Razon |
|----|--------|-------|
| CU9 | EXPIRACION_ANALISIS | Proceso automatico irreversible |
| CU10 | VENCIMIENTO | Proceso automatico irreversible |
| CU25 (MODIF) | RETIRO_MERCADO | Recall asociado al lote |

---

## 9. Casos de Uso Ejemplos

### 9.1 Reverso de Venta por el Creador

**Situacion:** El analista de planta que registro una venta detecta un error.

**Datos:**
| Campo | Valor |
|-------|-------|
| Lote | UDV-001/25120001 |
| Movimiento | BAJA - VENTA - 10 UNIDAD - MOV-2025-001234 |
| Motivo del Reverso | Error de carga - Se vendieron 5 unidades, no 10. Referencia: INC-001 |

**Autorizacion:**
- El analista es el creador del movimiento → AUTORIZADO

**Resultado:**
- Venta reversada
- 10 unidades restauradas al bulto
- Trazas vendidas vuelven a DISPONIBLE
- Nuevo movimiento de reverso registrado

### 9.2 Reverso por Superior Jerarquico

**Situacion:** El Gerente de Control de Calidad detecta error en movimiento de analista.

**Datos:**
| Campo | Valor |
|-------|-------|
| Usuario que reversa | Gerente Control Calidad (nivel 3) |
| Creador del movimiento | Analista Control Calidad (nivel 2) |
| Movimiento | MODIFICACION - ANALISIS - MOV-2025-001235 |

**Autorizacion:**
- Nivel 3 > Nivel 2 → AUTORIZADO

**Resultado:**
- Movimiento reversado exitosamente

### 9.3 Reverso Denegado por Jerarquia

**Situacion:** Un analista intenta reversar movimiento de un gerente.

**Datos:**
| Campo | Valor |
|-------|-------|
| Usuario que reversa | Analista Planta (nivel 2) |
| Creador del movimiento | Gerente Garantia Calidad (nivel 4) |

**Autorizacion:**
- Nivel 2 < Nivel 4 → DENEGADO

**Resultado:**
- Mensaje: "No tiene permisos para reversar este movimiento. Fue creado por {gerente} (rol: GERENTE_GARANTIA_CALIDAD, nivel 4). Su rol es ANALISTA_PLANTA (nivel 2). Solo puede reversarlo el usuario que lo creó o un superior jerárquico."

### 9.4 Reverso Denegado a Auditor

**Situacion:** Un auditor intenta reversar cualquier movimiento.

**Resultado:**
- Mensaje: "Los auditores no tienen permisos para reversar movimientos. El rol AUDITOR es de solo lectura."

### 9.5 Reverso de Movimiento Legacy

**Situacion:** Se intenta reversar movimiento sin creador asignado.

**Datos:**
| Campo | Valor |
|-------|-------|
| Movimiento | Sin usuario creador (legacy) |
| Usuario que reversa | Supervisor Planta (nivel 3) |

**Autorizacion:**
- Movimiento legacy solo puede ser reversado por ADMIN → DENEGADO

**Resultado:**
- Mensaje: "No tiene permisos para reversar este movimiento legacy..."

### 9.6 Error: Movimiento No Reversable

**Situacion:** Se intenta reversar un vencimiento de lote.

**Resultado:**
- Mensaje: "No se puede reversar un vencimiento de lote (CU10). El vencimiento es un proceso automático irreversible."

---

## 10. Preguntas Frecuentes

### 10.1 Sobre Autorizacion

**P: ¿Por que no puedo reversar este movimiento?**
R: Verificar:
1. ¿Eres el creador del movimiento?
2. ¿Tu nivel jerarquico es superior al creador?
3. ¿No eres AUDITOR?
4. ¿El movimiento no es legacy? (si lo es, solo ADMIN puede)

**P: ¿Que es un movimiento legacy?**
R: Son movimientos que no tienen usuario creador asignado, generalmente de datos migrados o anteriores al sistema de tracking.

**P: ¿Por que el AUDITOR no puede reversar?**
R: El rol AUDITOR es de solo lectura por diseño. No debe modificar datos del sistema.

### 10.2 Sobre el Proceso

**P: ¿Puedo reversar cualquier movimiento?**
R: No. Hay movimientos automaticos que son irreversibles: vencimientos (CU10) y expiracion de analisis (CU9).

**P: ¿Que pasa si el lote tiene un recall?**
R: No se puede reversar el movimiento de MODIFICACION de recall. El recall es una operacion critica que bloquea reversos.

**P: ¿El reverso elimina el movimiento original?**
R: No lo elimina, lo marca como inactivo. Queda disponible para auditoria.

### 10.3 Sobre los Efectos

**P: ¿Que pasa con las trazas al reversar una venta?**
R: Las trazas que estaban en VENDIDO vuelven a DISPONIBLE.

**P: ¿Que pasa si reverso una devolucion de venta?**
R: El lote derivado (con sufijo _D_N) se elimina logicamente y las trazas vuelven al lote original.

**P: ¿Se puede reversar un reverso?**
R: No esta soportado actualmente.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Todos los roles excepto AUDITOR
2. **Para que:** Revertir movimientos erroneos
3. **Tipo de movimiento:** Variable segun el original
4. **Pre-requisito:** Movimiento activo, usuario autorizado
5. **Autorizacion:** Creador siempre, superior jerarquico si
6. **AUDITOR:** Nunca puede reversar
7. **Legacy:** Solo ADMIN puede reversar
8. **Irreversibles:** Vencimiento y Expiracion de Analisis
9. **Recall:** Bloquea reverso de modificaciones
10. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)

---

## Relacion con Otros CUs

### Movimientos que CU31 Puede Reversar

| CU | Tipo | Motivo |
|----|------|--------|
| CU1 | ALTA | COMPRA |
| CU2/CU8 | MODIFICACION | ANALISIS |
| CU3 | BAJA | MUESTREO |
| CU4 | BAJA | DEVOLUCION_COMPRA |
| CU5/CU6 | MODIFICACION | RESULTADO_ANALISIS |
| CU7 | BAJA | CONSUMO_PRODUCCION |
| CU11 | MODIFICACION | ANULACION_ANALISIS |
| CU20 | ALTA | PRODUCCION_PROPIA |
| CU22 | MODIFICACION | LOTE_LIBERADO |
| CU23 | BAJA | VENTA |
| CU24 | ALTA | DEVOLUCION_VENTA |
| CU25 | ALTA | RETIRO_MERCADO |
| CU30 | BAJA | AJUSTE |
| CU21 | MODIFICACION | TRAZADO |

### Movimientos que CU31 NO Puede Reversar

| CU | Motivo | Razon |
|----|--------|-------|
| CU9 | EXPIRACION_ANALISIS | Automatico |
| CU10 | VENCIMIENTO | Automatico |
| CU25 (MODIF) | RETIRO_MERCADO | Recall |

---

**Fin del Documento - CU31_REVERSO_MOVIMIENTO v1.0 - Especificacion Funcional**
