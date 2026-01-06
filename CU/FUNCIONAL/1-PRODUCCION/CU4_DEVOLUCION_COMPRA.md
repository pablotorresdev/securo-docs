# CU4 - DEVOLUCION COMPRA: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-09
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** BAJA
**Motivo:** DEVOLUCION_COMPRA

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

Este caso de uso permite registrar la **devolucion de un lote completo al proveedor**. Se utiliza cuando un material comprado debe ser retornado por problemas de calidad, incumplimiento de especificaciones, o cualquier otra razon que justifique su devolucion.

### 1.2 Cuando Usar Este CU

Usar CU4 cuando:
- Un lote fue **rechazado** en el analisis de calidad y debe devolverse
- El material presenta **defectos** detectados antes o durante el analisis
- El proveedor solicita la devolucion del material
- Hay **errores en el despacho** del proveedor (producto equivocado, cantidades incorrectas)
- El material no cumple con las **especificaciones** acordadas

**NO usar CU4 cuando:**
- El lote es de produccion interna (Conifarma) → no aplica devolucion a proveedor
- Se quiere ajustar el stock por otro motivo → usar **CU30 (Ajuste Stock)**
- Se quiere anular el ingreso por error → usar **CU31 (Reverso)**
- El producto es Semielaborado o Unidad de Venta → estos son de produccion interna

### 1.3 Resultado del CU

Al completar exitosamente:
- El lote cambia a estado **DEVUELTO** y dictamen **RECHAZADO**
- Todos los bultos quedan con cantidad actual **cero** y estado **DEVUELTO**
- Se registra un **movimiento de BAJA** con motivo DEVOLUCION_COMPRA
- Si habia un analisis en curso, se marca como **CANCELADO**
- El lote ya no esta disponible para ninguna operacion posterior

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Analista de Planta | SI | Usuario principal para esta operacion |
| Administrador | SI | Acceso completo al sistema |
| Supervisor de Planta | NO | Debe solicitar a Analista de Planta |
| Analista Control Calidad | NO | Su rol es analizar, no gestionar devoluciones |
| Gerentes | NO | Roles de supervision, no operativos |
| DT | NO | Rol de aprobacion, no operativo |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar que el lote fisico esta preparado para devolucion
- Coordinar con el proveedor la logistica de devolucion
- Documentar el motivo de la devolucion (mínimo 20 caracteres)
- Confirmar los datos antes de ejecutar la operacion

---

## 3. Pre-condiciones

### 3.1 Lotes Elegibles para Devolucion

Para que un lote aparezca en la lista de seleccion, debe cumplir:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Tipo de producto** | API, Excipiente, Acond. Primario o Acond. Secundario |
| **Dictamen valido** | RECIBIDO, CUARENTENA, APROBADO o RECHAZADO |
| **Estado valido** | NO debe estar en estado DEVUELTO |
| **Con stock** | Debe tener al menos un bulto con cantidad actual > 0 |

### 3.2 Tipos de Producto Permitidos

| Tipo de Producto | Permitido | Motivo |
|------------------|-----------|--------|
| API | SI | Material comprado a proveedor |
| Excipiente | SI | Material comprado a proveedor |
| Acondicionamiento Primario | SI | Material comprado a proveedor |
| Acondicionamiento Secundario | SI | Material comprado a proveedor |
| Semielaborado | NO | Produccion interna |
| Unidad de Venta | NO | Produccion interna |

### 3.3 Dictamenes Permitidos para Devolucion

| Dictamen | Puede Devolverse | Situacion Tipica |
|----------|------------------|------------------|
| RECIBIDO | SI | Problemas detectados antes de analizar |
| CUARENTENA | SI | Problemas durante el analisis |
| APROBADO | SI | Problemas detectados post-aprobacion |
| RECHAZADO | SI | Caso mas comun - analisis no conforme |
| LIBERADO | NO | No aplica para materias primas |
| DEVOLUCION_CLIENTES | NO | Es devolucion de clientes, no de compra |

---

## 4. Formulario de Devolucion

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote a Devolver** | SI | Selector | Lote que se devolvera al proveedor | - |
| **Fecha de Devolucion** | SI | Fecha | Fecha en que se realiza la devolucion | Hoy |
| **Motivo del Cambio/Observaciones** | SI | Texto | Descripcion del motivo de la devolucion (min 20 chars) | - |

### 4.2 Informacion del Lote en el Selector

El selector muestra para cada lote elegible:

| Campo | Ejemplo |
|-------|---------|
| Codigo | 1-05701_25120001 |
| Lote Proveedor | LP-2025-001 |
| Nro Remito | REM-123456 (si existe) |
| Proveedor | Sigma-Aldrich |
| Producto | Paracetamol |
| Dictamen | RECHAZADO |

### 4.3 Caracteristica Importante: Devolucion Completa

**La devolucion siempre es del lote completo.** No es posible devolver parcialmente:
- Se devuelven **todos los bultos** del lote
- Se devuelve **toda la cantidad actual** del lote
- El lote queda con cantidad cero y no puede usarse mas

---

## 5. Validaciones

### 5.1 Validaciones al Enviar el Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado" |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado" |
| 3 | Fecha de Devolucion | Debe ingresar fecha | "La fecha es obligatoria" |
| 4 | Motivo del Cambio | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 5 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 6 | Motivo del Cambio | Maximo 300 caracteres | "El motivo no puede exceder 300 caracteres" |

### 5.2 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de devolucion debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de movimiento no puede ser anterior a la fecha de ingreso del lote" |

---

## 6. Resultado de la Operacion

### 6.1 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Estado | (variable) | DEVUELTO |
| Dictamen | (variable) | RECHAZADO |
| Cantidad Actual | (cantidad existente) | 0 |

### 6.2 Cambios en los Bultos

Para **cada bulto** del lote:

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Estado | (variable) | DEVUELTO |
| Cantidad Actual | (cantidad existente) | 0 |

### 6.3 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | BAJA |
| Motivo | DEVOLUCION_COMPRA |
| Cantidad | Cantidad total del lote (antes de devolver) |
| Unidad de Medida | Unidad del lote |
| Dictamen Inicial | Dictamen que tenia el lote antes |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de devolucion indicada |
| Motivo del cambio | Motivo del cambio ingresado |
| Activo | SI |

### 6.4 Detalles del Movimiento

Para **cada bulto** se registra un detalle:

| Atributo | Valor |
|----------|-------|
| Bulto | Referencia al bulto |
| Cantidad | Cantidad que tenia el bulto (antes de devolver) |
| Unidad de Medida | Unidad del bulto |
| Activo | SI |

### 6.5 Cancelacion de Analisis en Curso

Si el lote tenia un **analisis sin dictaminar** (en curso):

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
[Formulario de Devolucion] ─────────────────────┐
      |                                          |
      | (Seleccionar lote y completar datos)     | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Confirmacion] ─────────────┐   [Menu Principal]
      |                                  |
      | (Confirmar)                      | (Volver a editar)
      v                                  v
[Pantalla de Exito] ◄───────────── [Formulario de Devolucion]
      |
      | (Nueva Devolucion) o (Ir a Inicio)
      v
[Formulario de Devolucion] o [Menu Principal]
```

### 7.2 Pantalla de Confirmacion

Antes de ejecutar la devolucion, el sistema muestra un resumen con:

- Codigo del lote a devolver
- Nombre del producto
- Nombre del proveedor
- Lote del proveedor
- Numero de remito (si existe)
- Cantidad total a devolver
- Cantidad de bultos
- Dictamen actual
- Fecha de devolucion
- Motivo del cambio

**Advertencia importante:** "Esta operacion devolvera el lote completo al proveedor. Esta accion no puede deshacerse."

### 7.3 Pantalla de Exito

Muestra:
- Mensaje: "Devolucion realizada correctamente"
- Codigo del lote devuelto
- Nuevo estado: DEVUELTO / RECHAZADO
- Opciones: "Realizar otra devolucion" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Operaciones NO Posibles Despues de CU4

Una vez devuelto, el lote queda **inactivo para operaciones**:

| CU | Nombre | Por Que No |
|----|--------|------------|
| CU2 | Cuarentena | Lote sin stock, estado DEVUELTO |
| CU3 | Muestreo | Lote sin stock |
| CU5/CU6 | Resultado Analisis | Lote con dictamen RECHAZADO |
| CU7 | Consumo Produccion | Lote sin stock y RECHAZADO |
| CU31 | Reverso | Solo el creador del movimiento puede revertir |

### 8.2 Unica Operacion Posible

| CU | Nombre | Condicion |
|----|--------|-----------|
| CU31 | Reverso | Solo si el usuario que ejecuto CU4 quiere revertir su propia operacion |

### 8.3 Flujo Tipico que Termina en CU4

```
CU1: Ingreso Compra (RECIBIDO)
         |
         v
CU2: Cuarentena (CUARENTENA)
         |
         v
CU3: Muestreo
         |
         v
CU6: Resultado RECHAZADO
         |
         v
CU4: Devolucion Compra (DEVUELTO/RECHAZADO)
         |
         v
    [FIN del lote]
```

---

## 9. Casos de Uso Ejemplos

### 9.1 Caso Tipico: Lote Rechazado por Analisis

**Situacion:** Un lote de API fue rechazado en el analisis de calidad y debe devolverse al proveedor.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05701_25110010 |
| Producto | Paracetamol (1-05701) |
| Proveedor | Sigma-Aldrich |
| Dictamen Actual | RECHAZADO |
| Cantidad | 25 KILOGRAMO en 1 bulto |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote a Devolver | 1-05701_25110010 |
| Fecha de Devolucion | 06/12/2025 |
| Motivo del Cambio | Devolucion por rechazo en analisis fisicoquimico - fuera de especificacion de pureza segun certificado ANA-2025-0180 |

**Resultado:**
- Lote: Estado DEVUELTO, Dictamen RECHAZADO, Cantidad 0
- Bulto 1: Estado DEVUELTO, Cantidad 0
- Movimiento de BAJA registrado con 25 KG

### 9.2 Caso con Analisis en Curso: Problema Detectado Durante Analisis

**Situacion:** Durante el analisis se detecta contaminacion y se decide devolver sin esperar el dictamen final.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05710_25110015 |
| Producto | Lactosa (1-05710) |
| Proveedor | ProvQuim SA |
| Dictamen Actual | CUARENTENA |
| Nro Analisis en Curso | ANA-2025-0195 |
| Cantidad | 60 KILOGRAMO en 3 bultos |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote a Devolver | 1-05710_25110015 |
| Fecha de Devolucion | 06/12/2025 |
| Motivo del Cambio | Devolucion por contaminacion detectada durante muestreo - material con particulas extranas visibles |

**Resultado:**
- Lote: Estado DEVUELTO, Dictamen RECHAZADO
- 3 Bultos: Todos DEVUELTO con Cantidad 0
- Analisis ANA-2025-0195: Dictamen cambia a CANCELADO
- Movimiento de BAJA registrado con 60 KG

### 9.3 Caso Preventivo: Devolucion Antes de Analizar

**Situacion:** Al recibir el material se detecta que el envase esta danado y se decide devolver sin iniciar analisis.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | ACOND-001/25120005 |
| Producto | Frasco Ambar 100ml (ACOND-001) |
| Proveedor | Envases SRL |
| Dictamen Actual | RECIBIDO |
| Cantidad | 500 UNIDAD en 5 bultos |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote a Devolver | ACOND-001/25120005 |
| Fecha de Devolucion | 06/12/2025 |
| Motivo del Cambio | Devolucion por dano en embalaje durante transporte - cajas aplastadas, frascos rotos |

**Resultado:**
- Lote: Estado DEVUELTO, Dictamen RECHAZADO
- 5 Bultos: Todos DEVUELTO con Cantidad 0
- No habia analisis, no se cancela nada
- Movimiento de BAJA registrado con 500 UNIDAD

### 9.4 Caso Error: Lote Ya Devuelto

**Situacion:** El usuario intenta devolver un lote que ya fue devuelto anteriormente.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Los lotes con estado DEVUELTO estan excluidos del selector

### 9.5 Caso Error: Producto de Produccion Interna

**Situacion:** El usuario busca devolver un lote de Semielaborado o Unidad de Venta.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes de tipo API, Excipiente, Acond. Primario o Secundario

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿Puedo devolver solo una parte del lote?**
R: No. La devolucion es siempre del lote completo. Si necesita ajustar parcialmente, use CU30 (Ajuste Stock).

**P: ¿Puedo devolver un lote aprobado?**
R: Si, aunque es poco comun. Puede ocurrir si se detectan problemas post-aprobacion o el proveedor solicita la devolucion.

**P: ¿Que pasa con el analisis en curso cuando devuelvo?**
R: Si hay un analisis sin dictaminar, se marca automaticamente como CANCELADO.

**P: ¿Puedo devolver un lote de produccion interna?**
R: No. Los Semielaborados y Unidades de Venta son producidos internamente y no tienen proveedor externo al cual devolver.

### 10.2 Sobre el Lote Devuelto

**P: ¿Puedo volver a usar el lote despues de devolverlo?**
R: No. El lote queda con cantidad cero y estado DEVUELTO. No puede usarse para ninguna operacion.

**P: ¿El lote sigue apareciendo en las consultas?**
R: Si, el lote permanece en el sistema para trazabilidad, pero con estado DEVUELTO y cantidad cero.

**P: ¿Puedo revertir una devolucion?**
R: Solo mediante CU31 (Reverso), y unicamente si usted fue quien realizo la devolucion.

### 10.3 Sobre la Lista de Lotes

**P: ¿Por que no veo un lote que deberia estar en la lista?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Sea de tipo API, Excipiente, Acond. Primario o Secundario
- Tenga dictamen RECIBIDO, CUARENTENA, APROBADO o RECHAZADO
- NO tenga estado DEVUELTO
- Tenga stock disponible (al menos un bulto con cantidad > 0)

**P: ¿Por que aparecen lotes aprobados en la lista?**
R: Porque tecnicamente es posible devolver un lote aprobado si se detectan problemas posteriores.

### 10.4 Sobre Errores

**P: ¿Que hago si devolvi el lote incorrecto?**
R: Puede usar CU31 (Reverso) si usted realizo la devolucion. Esto restaurara el lote a su estado anterior.

**P: ¿Que hago si la fecha de devolucion es incorrecta?**
R: Debera usar CU31 (Reverso) para anular la devolucion y repetirla con la fecha correcta.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Analista de Planta o Administrador
2. **Para que:** Devolver un lote completo al proveedor
3. **Productos permitidos:** API, Excipiente, Acond. Primario, Acond. Secundario
4. **Dictamen resultante:** RECHAZADO
5. **Estado resultante:** DEVUELTO
6. **Tipo de movimiento:** BAJA
7. **Motivo del movimiento:** DEVOLUCION_COMPRA
8. **Campo critico:** Motivo del Cambio/Observaciones (mínimo 20 caracteres)
9. **Restriccion importante:** La devolucion es siempre del lote COMPLETO

---

## Relacion con Otros CUs

### Operaciones Previas (que pueden llevar a CU4)

| CU | Nombre | Situacion |
|----|--------|-----------|
| CU1 | Alta Ingreso Compra | Lote recibido con problemas inmediatos |
| CU2 | Dictamen Cuarentena | Lote en analisis con problemas detectados |
| CU6 | Resultado Rechazado | Caso mas comun - lote no paso analisis |

### Operaciones Siguientes (despues de CU4)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU31 | Reverso | Solo si el mismo usuario quiere revertir su operacion |

---

**Fin del Documento - CU4_DEVOLUCION_COMPRA v1.1 - Especificacion Funcional**
