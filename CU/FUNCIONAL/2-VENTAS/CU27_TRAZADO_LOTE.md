# CU27 - TRAZADO LOTE: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** MODIFICACION
**Motivo:** TRAZADO

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Trazado](#4-formulario-de-trazado)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite **asignar numeros de traza unicos** a las unidades de un lote de Unidad de Venta. El trazado es el proceso mediante el cual cada unidad individual del lote recibe un identificador unico (numero de traza) que permite su seguimiento completo a lo largo de toda la cadena de distribucion.

### 1.2 Concepto de Trazabilidad

La trazabilidad farmaceutica (ANMAT) requiere que cada unidad de venta pueda ser identificada individualmente desde su produccion hasta el paciente. El numero de traza:
- Es unico por unidad
- Permite rastrear el origen del producto
- Facilita recall de productos especificos
- Cumple con regulaciones ANMAT de trazabilidad

### 1.3 Cuando Usar Este CU

Usar CU27 cuando:
- Un lote de Unidad de Venta fue aprobado pero aun no tiene trazas asignadas
- Se necesita asignar numeracion de trazas antes de vender

**NO usar CU27 cuando:**
- El lote ya tiene trazas asignadas → ya esta trazado
- El lote no es de tipo Unidad de Venta → no requiere trazas individuales
- El lote no esta aprobado → usar primero **CU5 (Resultado Aprobado)**
- Se quiere vender → usar **CU22 (Venta Producto)** despues de trazar

### 1.4 Resultado del CU

Al completar exitosamente:
- Se crean **trazas individuales** para cada unidad del lote
- Las trazas se **distribuyen entre los bultos** segun sus cantidades
- El lote se marca como **trazado = true**
- Se registra un **movimiento de MODIFICACION** con motivo TRAZADO
- El lote queda listo para venta (CU22) si tiene dictamen LIBERADO

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Director Tecnico (DT) | SI | Responsable de trazabilidad |
| Gerente Garantia Calidad | SI | Control de trazabilidad |
| Administrador | SI | Acceso completo al sistema |
| Gerente Control Calidad | NO | No participa en trazado |
| Analista Control Calidad | NO | No tiene acceso a trazado |
| Supervisor de Planta | NO | No tiene acceso a trazado |
| Analista de Planta | NO | No tiene acceso a trazado |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar que el lote esta aprobado y sin trazas previas
- Determinar el numero de traza inicial correcto
- Asegurar que la numeracion no se superponga con lotes anteriores
- Documentar el motivo del trazado (mínimo 20 caracteres)

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para trazado:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Dictamen APROBADO** | El lote debe estar aprobado por Control de Calidad |
| **Tipo Unidad de Venta** | Solo aplica a productos terminados |
| **Con stock** | Debe tener al menos un bulto con cantidad actual > 0 |
| **Sin trazas** | NO debe tener trazas activas previamente asignadas |

### 3.2 Estados del Lote

| Dictamen | Puede Trazarse | Motivo |
|----------|----------------|--------|
| RECIBIDO | NO | Requiere aprobacion (CU5) |
| CUARENTENA | NO | Requiere aprobacion (CU5) |
| APROBADO | SI | Estado correcto para trazar |
| RECHAZADO | NO | Lote rechazado |
| LIBERADO | NO* | Ya deberia estar trazado |

*Los lotes LIBERADOS normalmente ya tienen trazas. Si no las tienen, hay un problema en el flujo.

### 3.3 Tipos de Producto

| Tipo de Producto | Puede Trazarse | Motivo |
|------------------|----------------|--------|
| Unidad de Venta | SI | Producto terminado para venta |
| Semielaborado | NO | Producto intermedio |
| Granel | NO | Producto intermedio |
| API | NO | Materia prima |
| Excipiente | NO | Materia prima |
| Acondicionamiento | NO | Material de empaque |

---

## 4. Formulario de Trazado

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote a Trazar** | SI | Selector | Lote al que se asignaran trazas | - |
| **Fecha de Trazado** | SI | Fecha | Fecha de la operacion | Hoy |
| **Numero de Traza Inicial** | SI | Numero | Primer numero de traza a asignar | - |
| **Motivo del Cambio** | SI | Texto | Descripcion del trazado | - |

### 4.2 Informacion del Lote en el Selector

El selector muestra para cada lote elegible:

| Campo | Ejemplo |
|-------|---------|
| Producto | Ibuprofeno 400mg Blister x 20 |
| Codigo interno | UDV-001/25120001 |
| Fecha ingreso | 01/12/2025 |

### 4.3 Calculo Automatico de Trazas

El sistema calcula automaticamente:
- **Cantidad total de trazas** = Suma de cantidades actuales de todos los bultos
- **Traza final** = Traza inicial + Cantidad total - 1
- **Distribucion por bulto** = Cada bulto recibe tantas trazas como unidades tiene

**Ejemplo:** Lote con 100 unidades en 2 bultos (50+50), traza inicial 10001:
- Bulto 1: Trazas 10001 a 10050
- Bulto 2: Trazas 10051 a 10100
- Rango total: 10001 / 10100

### 4.4 Informacion Detallada del Lote (Solo Lectura)

Al seleccionar un lote, se muestra:

| Campo | Descripcion |
|-------|-------------|
| Codigo interno | Codigo del lote |
| Producto | Nombre del producto |
| Cantidad actual | Stock disponible |
| Numero de bultos | Cantidad de bultos |
| Dictamen | Estado del lote |

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado" |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado" |
| 3 | Lote | Debe ser tipo UNIDAD_VENTA | "El lote debe ser UNIDAD_VENTA para poder trazarse" |
| 4 | Fecha de Trazado | Debe ingresar fecha | "La fecha es obligatoria" |
| 5 | Traza Inicial | Debe ser mayor a 0 | "Ingrese un valor valido para la traza inicial del lote" |
| 6 | Motivo del Cambio | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 7 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 8 | Motivo del Cambio | Maximo 300 caracteres | "El motivo no puede exceder 300 caracteres" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de trazado debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de movimiento no puede ser anterior a la fecha de ingreso del lote" |

---

## 6. Resultado de la Operacion

### 6.1 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Trazado | false | true |
| Trazas | (vacio) | Lista de trazas creadas |

### 6.2 Trazas Creadas

Para cada unidad del lote se crea una traza:

| Atributo | Valor |
|----------|-------|
| Numero de Traza | Secuencial desde traza inicial |
| Lote | Referencia al lote |
| Bulto | Referencia al bulto correspondiente |
| Producto | Producto del lote |
| Estado | DISPONIBLE |
| Activo | SI |

### 6.3 Distribucion de Trazas por Bulto

Las trazas se asignan secuencialmente a cada bulto segun su cantidad:

| Bulto | Cantidad | Trazas Asignadas |
|-------|----------|------------------|
| Bulto 1 | N1 unidades | Traza inicial hasta Traza inicial + N1 - 1 |
| Bulto 2 | N2 unidades | Continuacion secuencial |
| ... | ... | ... |

**Ejemplo concreto:**
- Traza inicial: 50001
- Bulto 1: 500 unidades → Trazas 50001 a 50500
- Bulto 2: 500 unidades → Trazas 50501 a 51000
- Total: 1000 trazas

### 6.4 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION |
| Motivo | TRAZADO |
| Dictamen Inicial | (dictamen actual del lote) |
| Dictamen Final | (dictamen actual del lote, sin cambio) |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de trazado indicada |
| Motivo del cambio | "[CU27] " + Motivo del cambio |
| Activo | SI |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Trazado Lote] ─────────────────────┐
      |                                          |
      | (Seleccionar lote, indicar traza)        | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Exito] ◄────────────────────── [Menu Principal]
      |
      | (Trazar Otro Lote) o (Ir a Inicio)
      v
[Formulario Trazado] o [Menu Principal]
```

### 7.2 Pantalla de Exito

Muestra:
- Mensaje: "Trazado de Lote exitoso"
- Codigo del lote trazado
- Producto
- Rango de trazas asignadas (ej: 50001 / 51000)
- Cantidad de trazas creadas

Opciones: "Trazar otro lote" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU27

| CU | Nombre | Cuando Aplicar |
|----|--------|----------------|
| CU21 | Confirmar Lote Liberado | Para habilitar la venta (si aun no esta LIBERADO) |
| CU22 | Venta Producto | Para vender el lote trazado (si esta LIBERADO) |
| CU26 | Reverso | Si el trazado fue incorrecto |

### 8.2 Flujo Tipico con Trazado

```
CU20: Ingreso Produccion (sin trazas)
         |
         v
CU2: Cuarentena (CUARENTENA)
         |
         v
CU3: Muestreo
         |
         v
CU5: Resultado APROBADO
         |
         v
CU27: Trazado Lote  ◄── [Este CU]
         |
         v
CU21: Confirmar LIBERADO
         |
         v
CU22: Venta del Producto
```

### 8.3 Alternativa: Trazado en Ingreso

Si las trazas se asignan durante el ingreso (CU20), no es necesario CU27:

```
CU20: Ingreso Produccion (con traza inicial)
         |
         v (trazas ya asignadas)
CU2 → CU3 → CU5 → CU21 → CU22
```

---

## 9. Casos de Uso Ejemplos

### 9.1 Trazado de Lote de Comprimidos

**Situacion:** Un lote de 1000 comprimidos aprobado necesita asignacion de trazas para venta.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-001/25120001 |
| Producto | Ibuprofeno 400mg Blister x 20 |
| Dictamen | APROBADO |
| Cantidad | 1000 UNIDAD en 2 bultos (500+500) |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote a Trazar | UDV-001/25120001 |
| Fecha de Trazado | 06/12/2025 |
| Numero de Traza Inicial | 50001 |
| Motivo del Cambio | Asignacion de trazas para habilitacion de venta lote IBU-2025-012 |

**Resultado:**
- 1000 trazas creadas (50001 a 51000)
- Bulto 1: Trazas 50001 a 50500
- Bulto 2: Trazas 50501 a 51000
- Lote marcado como trazado = true
- Movimiento de MODIFICACION registrado

### 9.2 Trazado de Lote de Jarabe

**Situacion:** Un lote de 500 frascos de jarabe requiere trazas.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-002/25110050 |
| Producto | Jarabe Pediatrico 100ml |
| Dictamen | APROBADO |
| Cantidad | 500 UNIDAD en 1 bulto |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Numero de Traza Inicial | 20001 |
| Fecha de Trazado | 06/12/2025 |
| Motivo del Cambio | Trazado de lote jarabe pediatrico para distribucion comercial |

**Resultado:**
- 500 trazas creadas (20001 a 20500)
- Bulto 1: Todas las 500 trazas
- Rango mostrado: 20001 / 20500

### 9.3 Caso Error: Lote Ya Trazado

**Situacion:** El usuario busca trazar un lote que ya tiene trazas asignadas.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes sin trazas activas existentes

### 9.4 Caso Error: Traza Inicial Invalida

**Situacion:** El usuario indica traza inicial 0 o negativa.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Numero de Traza Inicial | 0 |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "Ingrese un valor valido para la traza inicial del lote"

### 9.5 Caso Error: Lote No Aprobado

**Situacion:** El usuario busca trazar un lote que aun esta en cuarentena.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes con dictamen APROBADO

### 9.6 Caso Error: Lote No es Unidad de Venta

**Situacion:** El usuario intenta trazar un lote de Semielaborado.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes de tipo Unidad de Venta

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿Que es una traza?**
R: Una traza es un identificador unico asignado a cada unidad individual de un producto. Permite rastrear esa unidad especifica desde su produccion hasta el cliente final.

**P: ¿Por que necesito trazar un lote?**
R: La trazabilidad es requerida por ANMAT para productos farmaceuticos. Permite identificar y retirar productos especificos en caso de recall, y garantiza la autenticidad del producto.

**P: ¿Cuando debo trazar?**
R: Despues de que el lote sea aprobado (CU5) y antes de venderlo (CU22). Si el lote no fue trazado durante el ingreso (CU20), debe trazarse con CU27.

**P: ¿Puedo trazar durante el ingreso?**
R: Si. En CU20 (Ingreso Produccion) hay un campo opcional "Traza Inicial" que permite asignar trazas en el momento del ingreso.

### 10.2 Sobre las Trazas

**P: ¿Como se distribuyen las trazas entre bultos?**
R: Cada bulto recibe tantas trazas consecutivas como unidades tiene. Si un bulto tiene 500 unidades, recibe 500 trazas consecutivas.

**P: ¿Puedo elegir cualquier numero de traza inicial?**
R: El numero debe ser mayor a 0. Se recomienda usar numeracion secuencial para evitar confusiones.

**P: ¿Que pasa si uso el mismo numero de traza que otro lote?**
R: Es una mala practica aunque el sistema no lo impida. Cada traza deberia ser unica en todo el sistema para garantizar trazabilidad efectiva.

**P: ¿Cuantas trazas se crean?**
R: Una traza por cada unidad del lote. Si el lote tiene 1000 unidades, se crean 1000 trazas.

### 10.3 Sobre los Lotes

**P: ¿Por que no veo un lote en la lista?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Tenga dictamen APROBADO
- Sea de tipo Unidad de Venta
- Tenga stock disponible
- NO tenga trazas activas ya asignadas

**P: ¿Puedo trazar un lote LIBERADO?**
R: Normalmente no aparece porque los lotes se trazan antes de liberarse. Si un lote LIBERADO no tiene trazas, hay un problema en el flujo.

**P: ¿Puedo trazar un lote parcialmente?**
R: No. El trazado siempre asigna trazas a todas las unidades del lote.

### 10.4 Sobre Errores

**P: ¿Que hago si trace con el numero incorrecto?**
R: Use CU26 (Reverso) para anular el trazado y luego repita con el numero correcto.

**P: ¿Puedo modificar las trazas despues de asignarlas?**
R: No directamente. Debe usar Reverso y volver a trazar.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** DT, Gerente Garantia Calidad o Administrador
2. **Para que:** Asignar numeros de traza unicos a unidades de venta
3. **Tipo de producto:** Solo Unidades de Venta
4. **Pre-requisito:** Lote APROBADO sin trazas existentes
5. **Tipo de movimiento:** MODIFICACION
6. **Motivo del movimiento:** TRAZADO
7. **Campo critico:** Numero de traza inicial (debe ser > 0)
8. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)
9. **Resultado:** Lote marcado como trazado = true, trazas distribuidas por bulto
10. **Siguiente paso:** CU21 (Confirmar Liberado) o CU22 (Venta si ya LIBERADO)

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU27)

| CU | Nombre | Resultado que Habilita |
|----|--------|------------------------|
| CU5 | Resultado Aprobado | Lote con dictamen APROBADO |
| CU20 | Ingreso Produccion | Si no se trazo en el ingreso |

### Operaciones Siguientes (despues de CU27)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU21 | Confirmar Lote Liberado | Lote APROBADO con trazas asignadas |
| CU22 | Venta Producto | Lote LIBERADO con trazas |
| CU26 | Reverso | Si se necesita anular el trazado |

---

**Fin del Documento - CU27_TRAZADO_LOTE v1.1 - Especificacion Funcional**
