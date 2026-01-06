# CU11 - ANULACION ANALISIS: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** MODIFICACION
**Motivo:** ANULACION_ANALISIS

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Anulacion](#4-formulario-de-anulacion)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite **anular un analisis en curso o dictaminado**. La anulacion es necesaria cuando:
- Se detecta un error en la asignacion del numero de analisis
- El analisis fue iniciado incorrectamente
- Se requiere cancelar un reanalisis que ya no es necesario
- Hubo un problema con la muestra y el analisis debe repetirse

### 1.2 Concepto de Anulacion de Analisis

La anulacion de analisis:
- Marca el analisis como ANULADO
- Restaura el dictamen del lote al estado que tenia antes del analisis
- Registra un movimiento de MODIFICACION con motivo ANULACION_ANALISIS
- Permite que el lote pueda recibir un nuevo analisis si es necesario

**Importante:** Solo se pueden anular analisis en curso (sin dictamen). Los analisis ya aprobados o rechazados no pueden anularse directamente.

### 1.3 Cuando Usar Este CU

Usar CU11 cuando:
- Se inicio un analisis con datos incorrectos
- El numero de analisis fue asignado por error
- Se requiere cancelar un analisis en curso
- La muestra fue contaminada o invalida

**NO usar CU11 cuando:**
- El analisis ya tiene resultado (APROBADO/RECHAZADO) → usar **CU31 (Reverso)**
- Se quiere registrar un resultado → usar **CU5/CU6 (Resultado Analisis)**
- Se quiere iniciar un nuevo analisis → usar **CU2 (Cuarentena)** o **CU8 (Reanalisis)**

### 1.4 Resultado del CU

Al completar exitosamente:
- El analisis se marca como **ANULADO**
- El dictamen del lote se **restaura** al estado anterior al analisis
- Se registra un **movimiento de MODIFICACION** con motivo ANULACION_ANALISIS
- El lote queda disponible para recibir un nuevo analisis

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Gerente Control Calidad | SI | Usuario principal para anular analisis |
| Administrador | SI | Acceso completo al sistema |
| Director Tecnico (DT) | NO | No participa en anulaciones |
| Gerente Garantia Calidad | NO | No tiene acceso a anulaciones |
| Analista Control Calidad | NO | No puede anular analisis |
| Supervisor de Planta | NO | No tiene acceso a calidad |
| Analista de Planta | NO | No tiene acceso a calidad |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar que el analisis debe ser realmente anulado
- Documentar claramente el motivo de la anulacion (mínimo 20 caracteres)
- Coordinar con laboratorio si hay muestras pendientes
- Evaluar si se requiere iniciar un nuevo analisis

---

## 3. Pre-condiciones

### 3.1 Requisitos del Analisis

Para que un analisis este disponible para anulacion:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El analisis debe estar activo (no eliminado) |
| **Sin dictamen** | El analisis no debe tener resultado (dictamen = null) |
| **Sin fecha realizado** | El analisis no debe tener fecha de realizacion |

### 3.2 Estados del Analisis

| Dictamen Analisis | Puede Anularse | Motivo |
|-------------------|----------------|--------|
| null (En curso) | SI | Estado correcto para anulacion |
| APROBADO | NO | Ya tiene dictamen, usar Reverso |
| RECHAZADO | NO | Ya tiene dictamen, usar Reverso |
| ANULADO | NO | Ya esta anulado |
| CANCELADO | NO | Ya fue cancelado |

### 3.3 Analisis Elegibles

El sistema muestra todos los analisis que cumplan **todas** las siguientes condiciones:

| Condicion | Valor Requerido |
|-----------|-----------------|
| Activo | SI (true) |
| Dictamen | null (sin resultado) |
| Fecha Realizado | null (sin completar) |

**Nota importante:** A diferencia de CU5/CU6 (Resultado Analisis), CU11 no requiere que el lote esté en CUARENTENA. Puede anular tanto analisis iniciales como reanalisis (de lotes APROBADOS).

---

## 4. Formulario de Anulacion

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Analisis en Curso a Anular** | SI | Selector | Analisis en curso a anular | - |
| **Fecha de Movimiento** | SI | Fecha (oculto) | Fecha de la operacion | Hoy (automatico) |
| **Codigo Lote** | SI | Texto (oculto) | Se completa automaticamente | (del analisis) |
| **Motivo del Cambio** | SI | Textarea | Descripcion del motivo de anulacion | - |

### 4.2 Informacion del Analisis en el Selector

El selector muestra para cada analisis elegible:

| Campo | Ejemplo |
|-------|---------|
| Numero de Analisis | ANA-2025-0185 |
| Codigo Lote | 1-05701_25110001 |

Formato: `Nro Analisis: ANA-2025-0185 | Codigo Lote: 1-05701_25110001`

### 4.3 Detalles Mostrados al Seleccionar Analisis

Al seleccionar un analisis, se muestran dinamicamente los datos del lote:

| Campo | Descripcion |
|-------|-------------|
| Codigo Interno | Codigo del lote |
| Producto | Nombre del producto |
| Fecha Ingreso | Fecha de ingreso del lote |
| Fecha Reanalisis Proveedor | Fecha de reanalisis segun proveedor |
| Fecha Vencimiento Proveedor | Fecha de vencimiento segun proveedor |
| Analisis del Lote | Lista de todos los analisis del lote con: Nro, Fecha Realizado, Fecha Reanalisis, Fecha Vencimiento, Dictamen, Titulo |

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Numero de Analisis | Debe seleccionar/ingresar uno | "El Nro de Analisis es obligatorio" |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado" |
| 3 | Fecha de Anulacion | Debe ingresar fecha | "La fecha es obligatoria" |
| 4 | Motivo del Cambio | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 5 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de anulacion debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de movimiento no puede ser anterior a la fecha de ingreso del lote" |

### 5.3 Validacion de Integridad

El sistema verifica que exista exactamente un movimiento de analisis para el numero indicado. Si existen duplicados, se genera un error de integridad:
- Mensaje: "Existen 2 movimientos de analisis iguales para ese lote"

---

## 6. Resultado de la Operacion

### 6.1 Cambios en el Analisis

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Dictamen | null | ANULADO |
| Fecha Realizado | null | Fecha de anulacion |
| Motivo del cambio | (vacio) | Motivo del cambio |

### 6.2 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Dictamen | CUARENTENA | (restaurado al estado anterior) |

**Nota:** El dictamen del lote se restaura al que tenia antes de que se iniciara el analisis. Por ejemplo:
- Si era RECIBIDO antes del analisis → vuelve a RECIBIDO
- Si era APROBADO (reanalisis) → vuelve a APROBADO

### 6.3 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION |
| Motivo | ANULACION_ANALISIS |
| Numero Analisis | Numero del analisis anulado |
| Dictamen Inicial | Dictamen actual del lote |
| Dictamen Final | ANULADO |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de anulacion indicada |
| Motivo del cambio | "[CU11] " + Motivo del cambio |
| Activo | SI |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Anulacion Analisis] ─────────────────┐
      |                                            |
      | (Seleccionar analisis, completar datos)    | (Cancelar)
      |                                            |
      v                                            v
[Pantalla de Exito] ◄────────────────────── [Menu Principal]
      |
      | (Otra Anulacion) o (Ir a Inicio)
      v
[Formulario Anulacion] o [Menu Principal]
```

### 7.2 Pantalla de Exito

Muestra:
- Mensaje de exito: "Anulacion de analisis exitoso"
- Numero del analisis anulado
- Codigo del lote afectado
- Producto
- Dictamen restaurado del lote

Opciones: "Realizar otra anulacion" o "Volver al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU11

| CU | Nombre | Cuando Aplicar |
|----|--------|----------------|
| CU2 | Dictamen Cuarentena | Para asignar nuevo analisis al lote |
| CU8 | Reanalisis | Si el lote estaba aprobado y requiere nuevo analisis |
| CU31 | Reverso | Si la anulacion fue incorrecta |

### 8.2 Flujo Tipico de Anulacion

```
Lote con analisis en curso (error detectado)
         |
         v
CU11: Anulacion Analisis
         |
         | Dictamen restaurado
         v
    +----+----+
    |         |
    v         v
Si era      Si era
RECIBIDO    APROBADO
    |         |
    v         v
CU2:        CU8:
Nuevo       Nuevo
Analisis    Reanalisis
```

### 8.3 Diferencia con Reverso

| Aspecto | Anulacion (CU11) | Reverso (CU31) |
|---------|------------------|----------------|
| Aplica a | Analisis en curso | Cualquier movimiento |
| Dictamen del analisis | Solo sin dictamen | Con o sin dictamen |
| Restaura dictamen lote | SI | SI |
| Complejidad | Simple | Compleja (jerarquia permisos) |

---

## 9. Casos de Uso Ejemplos

### 9.1 Anulacion por Error en Numero de Analisis

**Situacion:** Se asigno un numero de analisis incorrecto y debe corregirse.

**Analisis seleccionado:**
| Campo | Valor |
|-------|-------|
| Numero Analisis | ANA-2025-0185 (incorrecto) |
| Lote | 1-05701_25110001 |
| Producto | Paracetamol API |
| Dictamen Lote | CUARENTENA |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Analisis a Anular | ANA-2025-0185 |
| Fecha de Anulacion | 06/12/2025 |
| Motivo del Cambio | Anulacion por error en numero de analisis. Numero correcto es ANA-2025-0186 |

**Resultado:**
- Analisis ANA-2025-0185: Dictamen = ANULADO
- Lote: Dictamen restaurado a RECIBIDO
- Movimiento de MODIFICACION/ANULACION_ANALISIS registrado
- Usuario puede ahora ejecutar CU2 con el numero correcto

### 9.2 Anulacion por Muestra Contaminada

**Situacion:** La muestra tomada para analisis resulto contaminada.

**Analisis seleccionado:**
| Campo | Valor |
|-------|-------|
| Numero Analisis | ANA-2025-0200 |
| Lote | 1-05710_25110050 |
| Producto | Lactosa Monohidrato |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Motivo del Cambio | Anulacion por contaminacion de muestra durante el muestreo. Se procedera a tomar nueva muestra |

**Resultado:**
- Analisis anulado
- Lote restaurado a estado anterior
- Se puede tomar nueva muestra (CU3) y asignar nuevo analisis (CU2)

### 9.3 Anulacion de Reanalisis Innecesario

**Situacion:** Se inicio un reanalisis que finalmente no era necesario.

**Analisis seleccionado:**
| Campo | Valor |
|-------|-------|
| Numero Analisis | REA-2025-0050 |
| Lote | 1-05705_25090010 |
| Dictamen Lote Previo | APROBADO |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Motivo del Cambio | Anulacion de reanalisis. Verificacion de documentacion confirmo que fecha de reanalisis es enero 2026 |

**Resultado:**
- Reanalisis anulado
- Lote restaurado a APROBADO
- Lote sigue disponible para uso sin cambios

### 9.4 Caso Error: Analisis con Dictamen

**Situacion:** El usuario intenta anular un analisis que ya fue aprobado.

**Resultado:**
- El analisis NO aparece en la lista de seleccion
- Solo aparecen analisis sin dictamen (en curso)
- Para revertir un analisis aprobado, usar CU31 (Reverso)

### 9.5 Caso Error: Motivo Muy Corto

**Situacion:** El usuario ingresa un motivo de menos de 20 caracteres.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Motivo del Cambio | "Error en numero" (16 caracteres) |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "El motivo del cambio es obligatorio (mínimo 20 caracteres)"

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿Puedo anular un analisis que ya tiene resultado?**
R: No. Solo se pueden anular analisis en curso (sin dictamen). Para revertir un analisis aprobado o rechazado, debe usar CU31 (Reverso).

**P: ¿Que pasa con el lote cuando anulo el analisis?**
R: El dictamen del lote se restaura al estado que tenia antes del analisis. Por ejemplo, si estaba RECIBIDO antes de entrar a cuarentena, vuelve a RECIBIDO.

**P: ¿Puedo asignar un nuevo analisis al lote despues de anular?**
R: Si. Una vez anulado el analisis, puede usar CU2 (Dictamen Cuarentena) para asignar un nuevo analisis.

**P: ¿La muestra fisica que se tomo, que pasa con ella?**
R: El sistema no gestiona muestras fisicas. Coordine con el laboratorio para disponer de la muestra segun procedimientos internos.

### 10.2 Sobre los Analisis

**P: ¿Por que no veo un analisis en la lista?**
R: Verificar que el analisis:
- Este activo (no eliminado)
- No tenga dictamen (solo en curso)
- No tenga fecha de realizacion

**P: ¿Puedo anular el mismo analisis dos veces?**
R: No. Una vez anulado, el analisis ya tiene dictamen ANULADO y no aparece en la lista.

### 10.3 Sobre Errores

**P: ¿Que hago si anule el analisis incorrecto?**
R: Use CU31 (Reverso) para revertir la operacion de anulacion.

**P: ¿Puedo modificar el motivo de anulacion despues?**
R: No. El motivo queda registrado en el movimiento y no puede modificarse.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Gerente Control Calidad o Administrador
2. **Para que:** Anular analisis en curso (sin dictamen)
3. **Restaura dictamen:** El lote vuelve al estado anterior al analisis
4. **Pre-requisito:** Analisis activo sin dictamen ni fecha realizado
5. **Tipo de movimiento:** MODIFICACION
6. **Motivo del movimiento:** ANULACION_ANALISIS
7. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)
8. **Resultado analisis:** ANULADO
9. **Siguiente paso:** CU2 (nuevo analisis) o CU8 (nuevo reanalisis)
10. **No aplica a:** Analisis ya aprobados o rechazados

---

## Relacion con Otros CUs

### Operaciones Previas (que generan analisis anulables)

| CU | Nombre | Analisis Generado |
|----|--------|-------------------|
| CU2 | Dictamen Cuarentena | Analisis inicial en curso |
| CU8 | Reanalisis | Reanalisis en curso |

### Operaciones Siguientes (despues de CU11)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU2 | Dictamen Cuarentena | Para asignar nuevo analisis |
| CU8 | Reanalisis | Si el lote estaba aprobado |
| CU31 | Reverso | Si se necesita revertir la anulacion |

---

**Fin del Documento - CU11_ANULACION_ANALISIS v1.0 - Especificacion Funcional**
