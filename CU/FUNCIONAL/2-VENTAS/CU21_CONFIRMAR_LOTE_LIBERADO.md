# CU21 - CONFIRMAR LOTE LIBERADO: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** MODIFICACION
**Motivo:** LOTE_LIBERADO

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Confirmacion](#4-formulario-de-confirmacion)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite **confirmar que un lote fue liberado externamente** para su comercializacion. Es importante entender que:

- **Este sistema NO libera lotes** - La liberacion es un proceso externo (regulatorio/documental)
- **Este CU solo confirma** que el proceso externo de liberacion fue completado satisfactoriamente
- Aplica **solo a Unidades de Venta** que fueron aprobadas por Control de Calidad

### 1.2 Concepto de Liberacion

La "liberacion" de un lote farmaceutico es la autorizacion final que permite su comercializacion. Este proceso incluye:
- Revision del expediente del lote por parte del DT
- Verificacion de toda la documentacion de fabricacion y analisis
- Firma de la documentacion de liberacion
- Una vez completado, se confirma en el sistema mediante CU21

### 1.3 Cuando Usar Este CU

Usar CU21 cuando:
- El DT ha completado el proceso de liberacion del lote
- Toda la documentacion de liberacion esta firmada
- El lote esta listo para ser vendido

**NO usar CU21 cuando:**
- El lote aun no fue aprobado por Control de Calidad → usar primero **CU5 (Resultado Aprobado)**
- El lote no es de tipo Unidad de Venta → no requiere liberacion
- El lote ya fue liberado → ya tiene dictamen LIBERADO

### 1.4 Resultado del CU

Al completar exitosamente:
- El dictamen del lote cambia de **APROBADO** a **LIBERADO**
- Se asigna la **fecha de vencimiento** del analisis al lote
- Se registra un **movimiento de MODIFICACION** con motivo LOTE_LIBERADO
- El lote queda habilitado para **venta** (CU22)

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Director Tecnico (DT) | SI | Usuario principal - responsable de liberacion |
| Gerente Garantia Calidad | SI | Puede confirmar liberaciones |
| Administrador | SI | Acceso completo al sistema |
| Gerente Control Calidad | NO | Su rol es aprobar analisis, no liberar |
| Analista Control Calidad | NO | No tiene autoridad para liberar |
| Supervisor de Planta | NO | No tiene acceso a liberaciones |
| Analista de Planta | NO | No tiene acceso a liberaciones |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar que el proceso externo de liberacion fue completado
- Confirmar que toda la documentacion esta firmada
- Verificar las trazas del lote a liberar
- Documentar el motivo de la confirmacion (mínimo 20 caracteres)

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para confirmar liberacion:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Dictamen APROBADO** | El lote debe estar aprobado por Control de Calidad |
| **Tipo Unidad de Venta** | Solo aplica a productos terminados |
| **Con stock** | Debe tener al menos un bulto con cantidad actual > 0 |
| **Con analisis con vencimiento** | Debe tener exactamente un analisis activo con fecha de vencimiento |

### 3.2 Tipos de Producto

| Tipo de Producto | Requiere Liberacion | Motivo |
|------------------|---------------------|--------|
| Unidad de Venta | SI | Producto terminado para venta |
| Semielaborado | NO | Producto intermedio |
| Granel | NO | Producto intermedio |
| API | NO | Materia prima |
| Excipiente | NO | Materia prima |
| Acondicionamiento | NO | Material de empaque |

### 3.3 Estados del Lote

| Estado Actual | Puede Liberarse | Motivo |
|---------------|-----------------|--------|
| RECIBIDO | NO | Requiere pasar por CU5 primero |
| CUARENTENA | NO | Requiere pasar por CU5 primero |
| APROBADO | SI | Estado correcto para liberar |
| RECHAZADO | NO | Lote rechazado no puede venderse |
| LIBERADO | NO | Ya fue liberado |
| DEVUELTO | NO | Fue devuelto |

---

## 4. Formulario de Confirmacion

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote a Confirmar** | SI | Selector | Lote a confirmar como liberado | - |
| **Fecha de Confirmacion** | SI | Fecha | Fecha de la confirmacion | Hoy |
| **Motivo del Cambio** | SI | Texto | Descripcion de la confirmacion | - |

### 4.2 Informacion del Lote en el Selector

El selector muestra para cada lote elegible:

| Campo | Ejemplo |
|-------|---------|
| Producto | Ibuprofeno 400mg Blister x 20 |
| Codigo | UDV-001/25120001 |
| Fecha Ingreso | 01/12/2025 |
| Trazas | 50001/51000 |

### 4.3 Informacion Importante en Pantalla

El formulario incluye un aviso destacado:

> **Importante:** Este sistema NO libera lotes. Esta pantalla permite confirmar que el proceso externo de liberacion fue completado satisfactoriamente y el lote esta habilitado para la venta.

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado" |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado" |
| 3 | Fecha de Confirmacion | Debe ingresar fecha | "La fecha es obligatoria" |
| 4 | Motivo del Cambio | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 5 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 6 | Motivo del Cambio | Maximo 300 caracteres | "El motivo no puede exceder 300 caracteres" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de confirmacion debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de movimiento no puede ser anterior a la fecha de ingreso del lote" |
| La fecha de analisis debe ser valida | "La fecha de analisis no puede ser anterior a la fecha de ingreso del lote" |

### 5.3 Validaciones de Analisis

| Validacion | Mensaje de Error |
|------------|------------------|
| Debe existir exactamente un analisis con fecha de vencimiento | "NO hay Analisis con fecha de vencimiento para el lote" |
| No puede haber multiples analisis con fecha de vencimiento | "Hay mas de un analisis activo con fecha de vencimiento" |

---

## 6. Resultado de la Operacion

### 6.1 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Dictamen | APROBADO | LIBERADO |
| Fecha Vencimiento Proveedor | null | Fecha vencimiento del analisis |

### 6.2 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION |
| Motivo | LOTE_LIBERADO |
| Dictamen Inicial | APROBADO |
| Dictamen Final | LIBERADO |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de confirmacion indicada |
| Motivo del cambio | "[CU21] " + Motivo del cambio ingresado |
| Activo | SI |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Confirmar Liberacion] ──────────────┐
      |                                          |
      | (Seleccionar lote, completar datos)      | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Exito] ◄────────────────────── [Menu Principal]
      |
      | (Nueva Confirmacion) o (Ir a Inicio)
      v
[Formulario Confirmar] o [Menu Principal]
```

### 7.2 Pantalla de Exito

Muestra:
- Mensaje: "Confirmacion de Lote Liberado exitosa"
- Codigo del lote liberado
- Nuevo dictamen: LIBERADO
- Opciones: "Confirmar otro lote" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU21

| CU | Nombre | Descripcion |
|----|--------|-------------|
| CU22 | Venta Producto | Registrar venta del lote liberado |
| CU27 | Trazado | Consultar/gestionar trazas del lote |
| CU26 | Reverso | Si la confirmacion fue incorrecta |

### 8.2 Flujo Completo de Unidad de Venta

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
CU21: Confirmar Lote LIBERADO  ◄── [Este CU]
         |
         v
CU22: Venta del Producto
         |
         v
    [FIN - Producto vendido]
```

### 8.3 Operaciones NO Posibles Antes de CU21

| CU | Nombre | Por Que No |
|----|--------|------------|
| CU22 | Venta Producto | Requiere dictamen LIBERADO |

---

## 9. Casos de Uso Ejemplos

### 9.1 Caso Tipico: Liberacion de Lote de Comprimidos

**Situacion:** El DT ha completado la revision y firma de la documentacion de liberacion de un lote de Ibuprofeno.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-001/25120001 |
| Producto | Ibuprofeno 400mg Blister x 20 |
| Fecha Ingreso | 01/12/2025 |
| Trazas | 50001/51000 |
| Dictamen Actual | APROBADO |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote a Confirmar | UDV-001/25120001 |
| Fecha de Confirmacion | 06/12/2025 |
| Motivo del Cambio | Liberacion completada segun protocolo LIB-2025-0150. Documentacion firmada por DT. |

**Resultado:**
- Lote: Dictamen cambia de APROBADO a LIBERADO
- Fecha vencimiento del lote: Tomada del analisis aprobado
- Movimiento de MODIFICACION con "[CU21] Liberacion completada..."
- Lote disponible para CU22 (Venta)

### 9.2 Caso con Jarabe

**Situacion:** Se confirma la liberacion de un lote de jarabe pediatrico.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-002/25110050 |
| Producto | Jarabe Pediatrico 100ml |
| Fecha Ingreso | 15/11/2025 |
| Trazas | 10001/10500 |
| Dictamen Actual | APROBADO |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote a Confirmar | UDV-002/25110050 |
| Fecha de Confirmacion | 06/12/2025 |
| Motivo del Cambio | Lote liberado para comercializacion - Expediente completo revisado y aprobado |

**Resultado:**
- Dictamen: APROBADO → LIBERADO
- 500 unidades listas para venta

### 9.3 Caso Error: Lote Sin Analisis con Vencimiento

**Situacion:** El usuario intenta confirmar un lote que no tiene fecha de vencimiento en su analisis.

**Resultado:**
- El sistema rechaza la operacion
- Mensaje: "NO hay Analisis con fecha de vencimiento para el lote"
- El usuario debe primero asegurar que el analisis tenga fecha de vencimiento

### 9.4 Caso Error: Lote No Aprobado

**Situacion:** El usuario busca confirmar un lote que aun esta en cuarentena.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes con dictamen APROBADO de tipo Unidad de Venta

### 9.5 Caso Error: Multiples Analisis con Vencimiento

**Situacion:** El lote tiene mas de un analisis activo con fecha de vencimiento.

**Resultado:**
- El sistema rechaza la operacion
- Mensaje: "Hay mas de un analisis activo con fecha de vencimiento"
- Debe resolverse la inconsistencia antes de liberar

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿El sistema libera el lote?**
R: No. El sistema solo CONFIRMA que el proceso externo de liberacion fue completado. La liberacion real es un proceso documental/regulatorio.

**P: ¿Quien puede confirmar la liberacion?**
R: El DT (Director Tecnico), Gerente de Garantia de Calidad o Administrador.

**P: ¿Por que solo aplica a Unidades de Venta?**
R: Porque solo los productos terminados (UV) requieren liberacion formal para poder venderse. Las materias primas y semielaborados se aprueban pero no se "liberan".

**P: ¿Que pasa con la fecha de vencimiento?**
R: Al confirmar la liberacion, el sistema toma la fecha de vencimiento del analisis y la asigna al lote.

### 10.2 Sobre los Lotes

**P: ¿Por que no veo un lote que deberia estar en la lista?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Tenga dictamen APROBADO
- Sea de tipo Unidad de Venta
- Tenga stock disponible
- Tenga un analisis con fecha de vencimiento

**P: ¿Puedo liberar un lote que ya fue liberado?**
R: No. Los lotes con dictamen LIBERADO no aparecen en la lista.

**P: ¿Que significa el rango de trazas que muestra?**
R: Es el rango de numeros de traza (identificadores individuales) de las unidades del lote. Ejemplo: 50001/51000 significa que el lote tiene 1000 unidades numeradas del 50001 al 51000.

### 10.3 Sobre Errores

**P: ¿Que hago si confirme por error?**
R: Use CU26 (Reverso) para anular la confirmacion. El lote volvera a estado APROBADO.

**P: ¿Que hago si el lote no tiene fecha de vencimiento en el analisis?**
R: Debe contactar a Control de Calidad para que completen los datos del analisis (CU5) con la fecha de vencimiento.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** DT, Gerente Garantia Calidad o Administrador
2. **Para que:** Confirmar que un lote fue liberado externamente
3. **El sistema NO libera:** Solo confirma el proceso externo
4. **Tipo de producto:** Solo Unidades de Venta
5. **Pre-requisito:** Lote APROBADO con analisis que tenga fecha de vencimiento
6. **Dictamen resultante:** LIBERADO
7. **Tipo de movimiento:** MODIFICACION
8. **Motivo del movimiento:** LOTE_LIBERADO
9. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)
10. **Habilita:** CU22 (Venta del Producto)

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU21)

| CU | Nombre | Resultado que Habilita |
|----|--------|------------------------|
| CU5 | Resultado Aprobado | Lote con dictamen APROBADO |

### Operaciones Siguientes (despues de CU21)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU22 | Venta Producto | Lote con dictamen LIBERADO |
| CU27 | Trazado | Lote con trazas activas |
| CU26 | Reverso | Si se necesita anular la confirmacion |

---

**Fin del Documento - CU21_CONFIRMAR_LOTE_LIBERADO v1.0 - Especificacion Funcional**
