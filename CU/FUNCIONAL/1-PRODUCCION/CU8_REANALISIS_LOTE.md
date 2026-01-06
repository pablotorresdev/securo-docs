# CU8 - REANALISIS LOTE: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** MODIFICACION
**Motivo:** ANALISIS

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Reanalisis](#4-formulario-de-reanalisis)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite **iniciar un nuevo analisis (reanalisis) para un lote previamente aprobado**. El reanalisis es necesario cuando:
- Se acerca la fecha de vencimiento del analisis original
- Se requiere verificar la estabilidad del producto
- Regulaciones exigen controles periodicos de calidad

### 1.2 Concepto de Reanalisis

El reanalisis es un proceso de control de calidad que se realiza sobre lotes ya aprobados para:
- Extender su vida util si el resultado es favorable
- Verificar que el producto mantiene sus especificaciones
- Cumplir con requerimientos regulatorios de estabilidad

**Importante:** El reanalisis NO cambia el dictamen actual del lote (permanece APROBADO). Solo crea un nuevo registro de analisis pendiente de resultado.

### 1.3 Cuando Usar Este CU

Usar CU8 cuando:
- Un lote aprobado se acerca a su fecha de reanalisis/vencimiento
- Se requiere un control de calidad adicional sobre un lote en stock
- Regulaciones exigen verificacion periodica

**NO usar CU8 cuando:**
- El lote aun no fue aprobado → usar primero **CU5 (Resultado Aprobado)**
- El lote tiene un analisis en curso → esperar resultado o anular (CU11)
- Se quiere registrar el resultado de un analisis → usar **CU5/CU6 (Resultado Analisis)**
- El lote fue rechazado → no se puede reanalizar un lote rechazado

### 1.4 Resultado del CU

Al completar exitosamente:
- Se crea un **nuevo registro de Analisis** vinculado al lote
- El analisis queda **pendiente** (sin dictamen, sin fecha realizado)
- El lote mantiene su dictamen **APROBADO** (sin cambio)
- Se registra un **movimiento de MODIFICACION** con motivo ANALISIS

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Gerente Control Calidad | SI | Usuario principal para iniciar reanalisis |
| Administrador | SI | Acceso completo al sistema |
| Director Tecnico (DT) | NO | No participa en inicio de analisis |
| Gerente Garantia Calidad | NO | Participa en liberacion, no en analisis |
| Analista Control Calidad | NO | Ejecuta analisis, no los inicia |
| Supervisor de Planta | NO | No tiene acceso a calidad |
| Analista de Planta | NO | No tiene acceso a calidad |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Identificar lotes que requieren reanalisis
- Asignar numero de analisis unico
- Documentar el motivo del reanalisis (mínimo 20 caracteres)
- Coordinar con laboratorio para ejecucion del analisis

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para reanalisis:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Dictamen APROBADO** | El lote debe estar aprobado |
| **Estado valido** | NUEVO, DISPONIBLE o EN_USO |
| **Sin analisis en curso** | No debe tener analisis pendiente (sin dictamen y sin fecha realizado) |

### 3.2 Estados del Lote

| Dictamen | Puede Reanalizarse | Motivo |
|----------|-------------------|--------|
| RECIBIDO | NO | Aun no tiene primer analisis |
| CUARENTENA | NO | Analisis inicial en curso |
| APROBADO | SI | Estado correcto para reanalisis |
| RECHAZADO | NO | Lote rechazado no se reanaliza |
| LIBERADO | NO* | Ya liberado, normalmente no se reanaliza |

*Los lotes LIBERADO no aparecen porque la consulta filtra por APROBADO.

### 3.3 Restriccion de Analisis en Curso

Un lote con analisis pendiente (dictamen = null Y fechaRealizado = null) no puede recibir otro reanalisis hasta que:
- Se complete el analisis actual (CU5/CU6)
- Se anule el analisis actual (CU11)

### 3.4 Estados Validos para Reanalisis

| Estado | Puede Reanalizarse | Motivo |
|--------|-------------------|--------|
| NUEVO | SI | Lote recien ingresado pero aprobado |
| DISPONIBLE | SI | Estado normal de lote aprobado |
| EN_USO | SI | Lote parcialmente usado pero con stock |
| CONSUMIDO | NO | Sin stock disponible |
| DEVUELTO | NO | Fue devuelto al proveedor |

---

## 4. Formulario de Reanalisis

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote a Reanalizar** | SI | Selector | Lote que recibira nuevo analisis | - |
| **Fecha del Reanalisis** | SI | Fecha | Fecha de inicio del reanalisis | Hoy |
| **Numero de Reanalisis** | SI | Mascara (Prefijo + Digitos) | Identificador unico: PREFIJO-N-NNNN (ej: MP-1-0001) | Prefijo: MP |
| **Motivo del Cambio** | SI | Texto | Descripcion del motivo del reanalisis | - |

### 4.2 Informacion del Lote en el Selector

El selector muestra para cada lote elegible:

| Campo | Ejemplo |
|-------|---------|
| Producto | Paracetamol 500mg |
| Dictamen | APROBADO |
| Codigo Interno | 1-05701_25110001 |
| Proveedor | Laboratorio XYZ |

### 4.3 Detalles Mostrados al Seleccionar Lote

Al seleccionar un lote, se muestran dinamicamente:

| Campo | Descripcion |
|-------|-------------|
| Codigo interno | Identificador del lote |
| Producto | Nombre del producto |
| Proveedor | Nombre del proveedor |
| Fecha de ingreso | Cuando ingreso el lote |
| Fecha reanalisis proveedor | Fecha indicada por proveedor |
| Fecha vencimiento proveedor | Vencimiento segun proveedor |
| Dictamen actual | Estado del lote |
| Analisis previos | Lista de analisis anteriores con fechas y dictamenes |

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado" |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado" |
| 3 | Fecha del Reanalisis | Debe ingresar fecha | "La fecha es obligatoria" |
| 4 | Numero de Reanalisis | Debe ingresar valor | "El numero de analisis es obligatorio" |
| 5 | Numero de Reanalisis | Debe ser unico en el sistema | "El numero de analisis ya existe" |
| 6 | Motivo del Cambio | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 7 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 8 | Motivo del Cambio | Maximo 300 caracteres | "El motivo no puede exceder 300 caracteres" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha del reanalisis debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de movimiento no puede ser anterior a la fecha de ingreso del lote" |

---

## 6. Resultado de la Operacion

### 6.1 Nuevo Analisis Creado

| Atributo | Valor |
|----------|-------|
| Numero Analisis | Numero ingresado por usuario |
| Lote | Referencia al lote |
| Dictamen | null (pendiente) |
| Fecha Realizado | null (pendiente) |
| Fecha Reanalisis | null |
| Fecha Vencimiento | null |
| Activo | SI |

### 6.2 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Dictamen | APROBADO | APROBADO (sin cambio) |
| Lista de Analisis | [analisis previos] | [analisis previos + nuevo] |

**Importante:** El dictamen del lote NO cambia. El lote permanece APROBADO y disponible para uso.

### 6.3 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION |
| Motivo | ANALISIS |
| Dictamen Inicial | APROBADO |
| Dictamen Final | APROBADO (sin cambio) |
| Numero Analisis | Numero del nuevo analisis |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha del reanalisis indicada |
| Motivo del cambio | "[CU8] " + Motivo del cambio |
| Activo | SI |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Reanalisis] ───────────────────────┐
      |                                          |
      | (Seleccionar lote, completar datos)      | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Exito] ◄────────────────────── [Menu Principal]
      |
      | (Otro Reanalisis) o (Ir a Inicio)
      v
[Formulario Reanalisis] o [Menu Principal]
```

### 7.2 Pantalla de Exito

Muestra:
- Mensaje de exito: "Analisis asignado con exito"
- Numero de analisis creado
- Codigo del lote
- Producto
- Dictamen del lote (permanece APROBADO)

Opciones: "Iniciar otro reanalisis" o "Volver al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU8

| CU | Nombre | Cuando Aplicar |
|----|--------|----------------|
| CU3 | Muestreo | Para tomar muestra del lote para analisis |
| CU5/CU6 | Resultado Analisis | Para registrar resultado del reanalisis |
| CU11 | Anulacion Analisis | Si se necesita cancelar el reanalisis |
| CU31 | Reverso | Si el registro fue incorrecto |

### 8.2 Flujo Tipico de Reanalisis

```
Lote APROBADO con analisis proximo a vencer
         |
         v
CU8: Reanalisis (crea nuevo analisis pendiente)
         |
         v
CU3: Muestreo (toma muestra para laboratorio)
         |
         v
    [Ensayos de Laboratorio]
         |
    +----+----+
    |         |
    v         v
 CU5:       CU6:
APROBADO   RECHAZADO
    |         |
    v         v
Extiende   Lote debe
vida util  ser retirado
```

### 8.3 Diferencia con Analisis Inicial

| Aspecto | Analisis Inicial (CU2) | Reanalisis (CU8) |
|---------|------------------------|------------------|
| Cuando se usa | Lote recien ingresado | Lote ya aprobado |
| Dictamen previo | RECIBIDO o CUARENTENA | APROBADO |
| Cambia dictamen | Si (a CUARENTENA) | No (permanece APROBADO) |
| Proposito | Primer control de calidad | Control periodico/estabilidad |
| Usuario tipico | Analista Control Calidad | Gerente Control Calidad |

---

## 9. Casos de Uso Ejemplos

### 9.1 Reanalisis por Proximidad a Vencimiento

**Situacion:** Un lote de API tiene fecha de reanalisis proxima a cumplirse.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05701_25110001 |
| Producto | Paracetamol API |
| Dictamen | APROBADO |
| Fecha Reanalisis Proveedor | 15/12/2025 |
| Analisis Previos | 1 (aprobado el 15/06/2025) |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote a Reanalizar | 1-05701_25110001 |
| Fecha del Reanalisis | 06/12/2025 |
| Numero de Reanalisis | MP-1-0150 (Prefijo: MP, Digitos: 1-0150) |
| Motivo del Cambio | Reanalisis programado por proximidad a fecha de reanalisis del proveedor 15/12/2025 |

**Resultado:**
- Nuevo analisis MP-1-0150 creado (pendiente)
- Lote mantiene dictamen APROBADO
- Movimiento de MODIFICACION/ANALISIS registrado
- Siguiente paso: CU3 (Muestreo)

### 9.2 Reanalisis por Control de Estabilidad

**Situacion:** Regulacion exige control semestral de estabilidad.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05710_25060010 |
| Producto | Lactosa Monohidrato |
| Dictamen | APROBADO |
| Analisis Previos | 2 (aprobados) |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Numero de Reanalisis | SE-1-0050 (Prefijo: SE, Digitos: 1-0050) |
| Fecha del Reanalisis | 06/12/2025 |
| Motivo del Cambio | Control de estabilidad semestral segun protocolo PE-EST-2025-001 |

**Resultado:**
- Nuevo analisis de estabilidad creado
- Lote sigue disponible para uso mientras se completa el analisis

### 9.3 Caso Error: Lote con Analisis en Curso

**Situacion:** El usuario intenta reanalizar un lote que ya tiene analisis pendiente.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes sin analisis en curso

### 9.4 Caso Error: Numero de Analisis Duplicado

**Situacion:** El usuario ingresa un numero de analisis que ya existe.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Numero de Reanalisis | MP-1-0100 (ya existe) |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "El numero de analisis ya existe"
- El usuario debe ingresar un numero unico

### 9.5 Caso Error: Lote Rechazado

**Situacion:** El usuario busca reanalizar un lote rechazado.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes con dictamen APROBADO

### 9.6 Caso Error: Lote No Aprobado

**Situacion:** El usuario busca reanalizar un lote en cuarentena.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Debe primero completar el analisis inicial (CU5/CU6)

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿El reanalisis cambia el dictamen del lote?**
R: No. El reanalisis solo crea un nuevo registro de analisis pendiente. El lote mantiene su dictamen APROBADO hasta que se registre el resultado del reanalisis (CU5/CU6).

**P: ¿Puedo usar el lote mientras el reanalisis esta en curso?**
R: Si. El lote permanece APROBADO y disponible para uso o venta mientras el reanalisis esta pendiente.

**P: ¿Que pasa si el reanalisis resulta rechazado?**
R: El lote cambiara a dictamen RECHAZADO (via CU6) y no podra usarse ni venderse mas.

**P: ¿Cuantos reanalisis puede tener un lote?**
R: No hay limite. Un lote puede tener multiples analisis a lo largo de su vida util.

### 10.2 Sobre los Lotes

**P: ¿Por que no veo un lote en la lista?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Tenga dictamen APROBADO
- Este en estado NUEVO, DISPONIBLE o EN_USO
- No tenga analisis en curso (pendiente sin resultado)

**P: ¿Puedo reanalizar un lote que ya fue vendido parcialmente?**
R: Si, siempre que tenga stock disponible y cumpla las pre-condiciones.

**P: ¿Puedo reanalizar un lote LIBERADO?**
R: Normalmente no aparece porque la consulta filtra por APROBADO. Los lotes LIBERADO ya pasaron por liberacion.

### 10.3 Sobre el Numero de Analisis

**P: ¿El numero de analisis debe ser unico?**
R: Si. El sistema valida que no exista otro analisis con el mismo numero en todo el sistema.

**P: ¿Que formato debe tener el numero?**
R: El sistema utiliza una mascara estructurada con el formato **PREFIJO-N-NNNN**:
- **Prefijo**: Selector con opciones MP, ME, MPD, PTD, SE, PT
- **Digitos**: 5 digitos en formato N-NNNN (ej: 1-0001)
- **Ejemplo completo**: MP-1-0001, ME-2-0150, PTD-1-0025

Este formato es consistente con el utilizado en Dictamen Cuarentena (CU10).

### 10.4 Sobre Errores

**P: ¿Que hago si inicie un reanalisis por error?**
R: Puede usar CU11 (Anulacion Analisis) para cancelar el analisis, o CU31 (Reverso) para revertir la operacion.

**P: ¿Puedo modificar el numero de analisis despues de crearlo?**
R: No. Debe anular el analisis y crear uno nuevo con el numero correcto.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Gerente Control Calidad o Administrador
2. **Para que:** Iniciar nuevo analisis en lote previamente aprobado
3. **NO cambia dictamen:** El lote permanece APROBADO
4. **Pre-requisito:** Lote APROBADO sin analisis en curso
5. **Tipo de movimiento:** MODIFICACION
6. **Motivo del movimiento:** ANALISIS
7. **Campo critico:** Numero de analisis (debe ser unico)
8. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)
9. **Resultado:** Nuevo analisis pendiente de resultado
10. **Siguiente paso:** CU3 (Muestreo) y luego CU5/CU6 (Resultado)

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU8)

| CU | Nombre | Resultado que Habilita |
|----|--------|------------------------|
| CU5 | Resultado Aprobado | Lote con dictamen APROBADO |

### Operaciones Siguientes (despues de CU8)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU3 | Muestreo | Lote con analisis pendiente |
| CU5/CU6 | Resultado Analisis | Analisis pendiente de resultado |
| CU11 | Anulacion Analisis | Si se necesita cancelar el reanalisis |
| CU31 | Reverso | Si se necesita revertir la operacion |

---

**Fin del Documento - CU8_REANALISIS_LOTE v1.2 - Especificacion Funcional**

*Cambios v1.2 (2025-12-26): Actualizado formato de numero de reanalisis con mascara PREFIJO-N-NNNN consistente con CU10 (Dictamen Cuarentena)*
