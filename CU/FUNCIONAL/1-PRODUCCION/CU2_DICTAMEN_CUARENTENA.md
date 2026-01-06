# CU2 - DICTAMEN CUARENTENA: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-09
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** MODIFICACION
**Motivo:** ANALISIS

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Cuarentena](#4-formulario-de-cuarentena)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite a Control de Calidad **confirmar la asignacion de un Numero de Analisis** a un lote y ponerlo en **cuarentena** para iniciar el proceso de analisis. Es el paso previo obligatorio antes de tomar muestras (CU3) y dictaminar resultados (CU5/CU6).

### 1.2 Cuando Usar Este CU

Usar CU2 cuando:
- Se recibio un lote por compra (CU1) y debe enviarse a Control de Calidad
- Se produjo un lote internamente (CU20) y debe analizarse
- Un lote aprobado requiere re-analisis por acercarse su fecha de vencimiento
- Un lote con analisis expirado debe volver a analizarse
- Un lote devuelto por cliente requiere analisis

### 1.3 Resultado del CU

Al completar exitosamente:
- El lote cambia su dictamen a **CUARENTENA**
- Se crea un nuevo registro de **Analisis** con el numero asignado
- Se registra un **movimiento de MODIFICACION** con motivo ANALISIS
- Las trazas del lote (si existen) cambian a estado DISPONIBLE
- El lote queda disponible para muestreo (CU3)

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Analista Control Calidad | SI | Usuario principal para esta operacion |
| Administrador | SI | Acceso completo al sistema |
| Analista de Planta | NO | No tiene acceso a operaciones de Calidad |
| Supervisor de Planta | NO | No tiene acceso a operaciones de Calidad |
| Gerentes | NO | Roles de supervision, no operativos |
| DT | NO | Rol de aprobacion final |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar que el lote fisico esta en condiciones de ser analizado
- Asignar un numero de analisis unico y valido
- Indicar la fecha de analisis (cuando se inicia el analisis)
- Documentar el motivo del cambio a cuarentena

---

## 3. Pre-condiciones

### 3.1 Lotes Elegibles para Cuarentena

Para que un lote aparezca en la lista de seleccion, debe cumplir:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Dictamen valido** | RECIBIDO, APROBADO, ANALISIS_EXPIRADO, LIBERADO o DEVOLUCION_CLIENTES |
| **Estado valido** | NUEVO, DISPONIBLE, DEVUELTO o EN_USO |
| **Con stock** | Debe tener al menos un bulto con cantidad actual > 0 |

### 3.2 Dictamenes Permitidos para Entrada

| Dictamen Actual | Caso de Uso Origen | Situacion |
|-----------------|-------------------|-----------|
| RECIBIDO | CU1 (Compra) o CU20 (Produccion) | Lote nuevo sin analizar |
| APROBADO | CU5 (Resultado) | Re-analisis de lote aprobado |
| ANALISIS_EXPIRADO | CU9 (Automatico) | Lote cuyo analisis expiro |
| LIBERADO | CU22 (Liberacion) | Lote liberado que requiere nuevo analisis |
| DEVOLUCION_CLIENTES | CU24 (Devolucion) | Lote devuelto a analizar |

### 3.3 Estados Permitidos para Entrada

| Estado Actual | Descripcion |
|---------------|-------------|
| NUEVO | Lote recien ingresado |
| DISPONIBLE | Lote disponible para uso |
| DEVUELTO | Lote devuelto por cliente |
| EN_USO | Lote parcialmente consumido |

---

## 4. Formulario de Cuarentena

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote** | SI | Selector | Lote a enviar a cuarentena | - |
| **Numero de Analisis** | SI | Texto | Codigo unico del analisis a realizar | - |
| **Fecha de Analisis** | SI | Fecha | Fecha en que se inicia el analisis | Hoy |
| **Fecha de Movimiento** | SI | Fecha | Fecha del registro del movimiento | Hoy |
| **Motivo del Cambio** | SI | Texto | Descripcion del motivo de la cuarentena | - |

### 4.2 Informacion del Lote Seleccionado (Solo Lectura)

Al seleccionar un lote, el sistema muestra automaticamente:

| Campo | Descripcion |
|-------|-------------|
| Codigo de Lote | Identificador unico del lote |
| Producto | Nombre y codigo del producto |
| Proveedor | Razon social del proveedor |
| Dictamen Actual | Dictamen antes de la cuarentena |
| Cantidad Actual | Stock disponible del lote |
| Fecha de Ingreso | Cuando ingreso el lote al sistema |
| Lote Proveedor | Codigo del lote asignado por el proveedor |

### 4.3 Numero de Analisis

El numero de analisis es un identificador **unico** asignado por Control de Calidad para rastrear el proceso de analisis del lote.

**Formato obligatorio:** `PREFIJO-N-NNNN`

| Prefijo | Tipo de Producto | Ejemplo |
|---------|------------------|---------|
| MP | Materia Prima (API, Excipiente) | MP-1-0001 |
| ME | Material de Empaque | ME-1-0001 |
| SE | Semielaborado | SE-1-0001 |
| PT | Producto Terminado (UV) | PT-1-0001 |
| MPD | Materia Prima para Desarrollo | MPD-1-0001 |
| PTD | Producto Terminado para Desarrollo | PTD-1-0001 |

**Caracteristicas:**
- Maximo 20 caracteres
- Formato: PREFIJO-N-NNNN (ej: MP-1-0001)
- Debe ser unico en todo el sistema (no puede repetirse)
- El sistema valida el formato automaticamente

---

## 5. Validaciones

### 5.1 Validaciones al Enviar el Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Debe seleccionar un lote" |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado" |
| 3 | Numero de Analisis | Debe ingresar valor | "El numero de analisis es obligatorio" |
| 4 | Numero de Analisis | Debe ser unico | "El numero de analisis ya existe" |
| 5 | Numero de Analisis | Maximo 20 caracteres | "El numero de analisis no puede exceder 20 caracteres" |
| 6 | Fecha de Analisis | Debe ingresar fecha | "La fecha de analisis es obligatoria" |
| 7 | Fecha de Movimiento | Debe ingresar fecha | "La fecha de movimiento es obligatoria" |
| 8 | Motivo del Cambio | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 9 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 10 | Motivo del Cambio | Maximo 300 caracteres | "El motivo no puede exceder 300 caracteres" |

### 5.2 Validaciones de Fechas

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de movimiento debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de movimiento no puede ser anterior a la fecha de ingreso del lote" |
| La fecha de analisis debe ser igual o posterior a la fecha de ingreso del lote | "La fecha de analisis no puede ser anterior a la fecha de ingreso del lote" |

### 5.3 Validacion de Numero de Analisis Unico

El sistema verifica que no exista otro analisis con el mismo numero:

- Si ya existe un analisis activo con ese numero → rechaza
- Si el numero fue usado por un analisis inactivo → tambien rechaza (el numero es historico)

---

## 6. Resultado de la Operacion

### 6.1 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Dictamen | (variable) | CUARENTENA |

**Nota:** El estado del lote NO cambia. Solo cambia el dictamen.

### 6.2 Registro de Analisis Creado

| Atributo | Valor |
|----------|-------|
| Numero de Analisis | El ingresado por el usuario |
| Fecha de Analisis | La fecha indicada |
| Dictamen | null (pendiente de resultado) |
| Lote | Referencia al lote |
| Activo | SI |

### 6.3 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION |
| Motivo | ANALISIS |
| Dictamen Inicial | Dictamen anterior del lote |
| Dictamen Final | CUARENTENA |
| Numero de Analisis | El ingresado |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de movimiento indicada |
| Motivo del cambio | Motivo del cambio ingresado |
| Activo | SI |

### 6.4 Cambios en Trazas (si aplica)

Si el lote tiene trazas activas:
- Todas las trazas cambian a estado **DISPONIBLE**

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario de Cuarentena] ────────────────────┐
      |                                         |
      | (Completar datos y enviar)              | (Cancelar)
      v                                         v
[Pantalla de Confirmacion] ──────────┐    [Menu Principal]
      |                               |
      | (Confirmar)                   | (Volver a editar)
      v                               v
[Pantalla de Exito] ◄────────── [Formulario de Cuarentena]
      |
      | (Nueva Cuarentena) o (Ir a Inicio)
      v
[Formulario de Cuarentena] o [Menu Principal]
```

### 7.2 Pantalla de Confirmacion

Antes de guardar, el sistema muestra un resumen con:

- Codigo del lote seleccionado
- Nombre del producto
- Nombre del proveedor
- Dictamen actual → CUARENTENA (nuevo)
- Numero de analisis asignado
- Fechas ingresadas
- Motivo del cambio

El usuario debe confirmar o puede volver a editar.

### 7.3 Pantalla de Exito

Muestra:
- Mensaje de exito: "Cambio de calidad a Cuarentena exitoso"
- Codigo del lote
- Numero de analisis asignado
- Opciones: "Realizar otra cuarentena" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Flujo Normal Despues de CU2

El flujo tipico despues de poner un lote en cuarentena es:

```
CU2: Cuarentena (CUARENTENA)
         |
         v
CU3: Muestreo
    - Se toma muestra fisica para analisis de laboratorio
         |
         v
CU5 o CU6: Resultado de Analisis
    - CU5: APROBADO → lote disponible para uso/produccion
    - CU6: RECHAZADO → lote debe devolverse o descartarse
         |
    +----+----+
    |         |
    v         v
Produccion  Devolucion
  (CU7)      (CU4)
```

### 8.2 Operaciones Posibles Despues de CU2

| CU | Nombre | Cuando Aplicar | Usuario |
|----|--------|----------------|---------|
| CU3 | Muestreo | Siempre - tomar muestra para analisis | Analista Control Calidad |
| CU11 | Anulacion Analisis | Si el analisis debe cancelarse | Gerente Control Calidad |
| CU31 | Reverso Movimiento | Si se asigno por error | Usuario que hizo la operacion |

### 8.3 Operaciones NO Posibles Mientras en Cuarentena

| CU | Nombre | Por Que No |
|----|--------|------------|
| CU5/CU6 | Resultado Analisis | Requiere primero CU3 (muestreo) |
| CU7 | Consumo Produccion | Solo lotes APROBADOS |
| CU22 | Liberacion | Solo lotes APROBADOS de Unidad de Venta |
| CU23 | Venta | Solo lotes LIBERADOS |

---

## 9. Casos de Uso Ejemplos

### 9.1 Caso Tipico: Lote Recibido a Cuarentena

**Situacion:** Llego un lote de API por compra (CU1) y Control de Calidad debe analizarlo.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05701_25120001 |
| Producto | Paracetamol (1-05701) |
| Proveedor | Sigma-Aldrich |
| Dictamen Actual | RECIBIDO |
| Cantidad | 25 KILOGRAMO |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote | 1-05701_25120001 |
| Numero de Analisis | ANA-2025-0150 |
| Fecha de Analisis | 16/12/2025 |
| Fecha de Movimiento | 16/12/2025 |
| Motivo del Cambio | Inicio de analisis de materia prima recibida segun protocolo QC-001 |

**Resultado:**
- Dictamen cambia: RECIBIDO → CUARENTENA
- Se crea Analisis ANA-2025-0150
- Lote disponible para muestreo (CU3)

### 9.2 Caso Re-analisis: Lote Aprobado Proximo a Vencer

**Situacion:** Un lote aprobado esta por cumplir su fecha de re-analisis y debe volver a analizarse.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05710_25080003 |
| Producto | Lactosa (1-05710) |
| Dictamen Actual | APROBADO |
| Cantidad | 45 KILOGRAMO |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote | 1-05710_25080003 |
| Numero de Analisis | ANA-2025-0151 |
| Fecha de Analisis | 16/12/2025 |
| Motivo del Cambio | Re-analisis programado por proximidad a fecha de vencimiento del analisis anterior |

**Resultado:**
- Dictamen cambia: APROBADO → CUARENTENA
- Se crea nuevo Analisis ANA-2025-0151 (el lote ya tenia analisis anteriores)
- Lote no puede usarse en produccion hasta nuevo resultado aprobado

### 9.3 Caso Lote Devuelto: Devolucion de Cliente

**Situacion:** Se recibio una devolucion de cliente (CU24) y debe analizarse el lote devuelto.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-001/25100005_D_1 |
| Producto | Ibuprofeno 400mg Caja x 20 |
| Dictamen Actual | DEVOLUCION_CLIENTES |
| Cantidad | 50 UNIDAD |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Lote | UDV-001/25100005_D_1 |
| Numero de Analisis | ANA-2025-0152 |
| Fecha de Analisis | 16/12/2025 |
| Motivo del Cambio | Analisis de lote devuelto por cliente para determinar estado del producto |

**Resultado:**
- Dictamen cambia: DEVOLUCION_CLIENTES → CUARENTENA
- Producto analizado para determinar si puede re-ingresar al circuito comercial

### 9.4 Caso Error: Numero de Analisis Duplicado

**Situacion:** El usuario intenta asignar un numero de analisis que ya existe.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Lote | 1-05702_25120001 |
| Numero de Analisis | ANA-2025-0150 | ← Ya existe |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "El numero de analisis ya existe"
- El usuario debe ingresar un numero diferente

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿Puedo poner en cuarentena un lote que ya esta en cuarentena?**
R: No. Un lote con dictamen CUARENTENA no aparece en la lista de seleccion. Primero debe resolverse su analisis actual (aprobado o rechazado).

**P: ¿Puedo poner en cuarentena un lote rechazado?**
R: No. Los lotes rechazados deben devolverse (CU4) o descartarse. No pueden volver a analizarse.

**P: ¿Que pasa con el lote mientras esta en cuarentena?**
R: El lote no puede usarse en produccion ni venderse. Debe esperar el resultado del analisis (CU5/CU6).

**P: ¿Cuanto tiempo puede estar un lote en cuarentena?**
R: No hay limite de tiempo en el sistema. Es responsabilidad de Control de Calidad completar el analisis en tiempo razonable.

### 10.2 Sobre el Numero de Analisis

**P: ¿Quien define el formato del numero de analisis?**
R: Es definido por la empresa segun sus procedimientos internos. El sistema solo valida que sea unico.

**P: ¿Puedo reutilizar un numero de analisis de un analisis anulado?**
R: No. Los numeros de analisis son historicos y no se reutilizan aunque el analisis se haya anulado.

**P: ¿Un lote puede tener mas de un analisis?**
R: Si. Cada vez que un lote pasa por CU2 se crea un nuevo registro de analisis. Esto ocurre en re-analisis.

### 10.3 Sobre la Lista de Lotes

**P: ¿Por que no veo un lote que deberia estar en la lista?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Tenga dictamen valido (RECIBIDO, APROBADO, ANALISIS_EXPIRADO, LIBERADO o DEVOLUCION_CLIENTES)
- Tenga estado valido (NUEVO, DISPONIBLE, DEVUELTO o EN_USO)
- Tenga stock disponible (al menos un bulto con cantidad > 0)

**P: ¿La lista esta ordenada?**
R: Si. Los lotes se ordenan por fecha de ingreso (mas antiguos primero), luego por codigo de lote.

### 10.4 Sobre Errores

**P: ¿Que hago si asigne el numero de analisis incorrecto?**
R: Puede usar CU31 (Reverso Movimiento) para anular la operacion y luego repetir CU2 con el numero correcto.

**P: ¿Que hago si seleccione el lote incorrecto?**
R: Si aun no confirmo, puede volver a editar. Si ya confirmo, use CU31 (Reverso Movimiento).

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Analista Control Calidad o Administrador
2. **Para que:** Asignar numero de analisis y poner lote en cuarentena
3. **Dictamenes de entrada:** RECIBIDO, APROBADO, ANALISIS_EXPIRADO, LIBERADO, DEVOLUCION_CLIENTES
4. **Dictamen resultante:** CUARENTENA
5. **Siguiente paso tipico:** CU3 (Muestreo)
6. **Motivo del movimiento:** ANALISIS
7. **Campo critico:** Numero de Analisis (debe ser unico)
8. **Restriccion importante:** Motivo del cambio minimo 20 caracteres

---

## Relacion con Otros CUs

### Operaciones Previas (que llevan a CU2)

| CU | Nombre | Dictamen Resultante |
|----|--------|---------------------|
| CU1 | Alta Ingreso Compra | RECIBIDO |
| CU20 | Alta Ingreso Produccion | RECIBIDO |
| CU5 | Resultado Aprobado | APROBADO (para re-analisis) |
| CU9 | Expiracion Analisis (automatico) | ANALISIS_EXPIRADO |
| CU24 | Devolucion de Cliente | DEVOLUCION_CLIENTES |

### Operaciones Siguientes (despues de CU2)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU3 | Muestreo | Lote en CUARENTENA |
| CU11 | Anulacion Analisis | Lote en CUARENTENA con analisis |
| CU31 | Reverso Movimiento | Ultimo movimiento fue CU2 |

---

**Fin del Documento - CU2_DICTAMEN_CUARENTENA v1.1 - Especificacion Funcional**
