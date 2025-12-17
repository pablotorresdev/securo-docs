# CU5/CU6 - RESULTADO ANALISIS: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-09
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** MODIFICACION
**Motivo:** RESULTADO_ANALISIS

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Resultado](#4-formulario-de-resultado)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite registrar el **resultado del analisis de calidad** de un lote. El resultado puede ser:
- **CU5 - APROBADO**: El lote cumple con las especificaciones y queda disponible para uso
- **CU6 - RECHAZADO**: El lote no cumple con las especificaciones y debe gestionarse su disposicion

### 1.2 Cuando Usar Este CU

Usar CU5/CU6 cuando:
- El laboratorio de Control de Calidad ha completado los ensayos del lote
- Se tiene el resultado definitivo del analisis (aprobado o rechazado)
- El lote esta en estado CUARENTENA con un analisis en curso

**NO usar CU5/CU6 cuando:**
- El lote aun no tiene numero de analisis → usar primero **CU2 (Cuarentena)**
- No se ha realizado muestreo del lote → usar primero **CU3 (Muestreo)**
- El lote ya tiene un dictamen (APROBADO/RECHAZADO) → si requiere nuevo analisis usar **CU8 (Reanalisis)**
- Se quiere cancelar el analisis sin dictaminar → usar **CU11 (Anulacion Analisis)**

### 1.3 Diferencia entre CU5 y CU6

| Aspecto | CU5 - APROBADO | CU6 - RECHAZADO |
|---------|----------------|-----------------|
| Resultado | Conforme | No Conforme |
| Dictamen Final | APROBADO | RECHAZADO |
| Campos adicionales | Fecha Reanalisis, Fecha Vencimiento, Titulo (%) | Solo Motivo del cambio |
| Accion siguiente tipica | Produccion, Venta, Liberacion | Devolucion, Descarte |

### 1.4 Resultado del CU

Al completar exitosamente:
- El **analisis** recibe fecha de realizacion y dictamen (APROBADO o RECHAZADO)
- El **lote** cambia su dictamen de CUARENTENA a APROBADO o RECHAZADO
- Se registra un **movimiento de MODIFICACION** con motivo RESULTADO_ANALISIS
- Si es APROBADO: se registran fechas de reanalisis, vencimiento y titulo

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Gerente Control Calidad | SI | Usuario principal para esta operacion |
| Administrador | SI | Acceso completo al sistema |
| Analista Control Calidad | NO | Solo puede muestrear, no dictaminar |
| Analista de Planta | NO | No tiene acceso a operaciones de Calidad |
| Supervisor de Planta | NO | No tiene acceso a operaciones de Calidad |
| Gerente Garantia Calidad | NO | No participa en dictamen de analisis |
| DT | NO | Participa en liberacion, no en analisis |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Revisar los resultados de laboratorio antes de dictaminar
- Verificar que el muestreo fue realizado correctamente
- Determinar si el lote cumple con las especificaciones del producto
- Completar todos los campos requeridos segun el dictamen
- Documentar el motivo del resultado (mínimo 20 caracteres)

---

## 3. Pre-condiciones

### 3.1 Requisitos del Lote

Para que un lote este disponible para resultado de analisis:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Dictamen CUARENTENA** | El lote debe estar en cuarentena |
| **Analisis en curso** | Debe existir un analisis sin dictamen y sin fecha de realizacion |
| **Con stock** | Debe tener al menos un bulto con cantidad actual > 0 |

### 3.2 Requisito de Muestreo Previo

**IMPORTANTE:** El sistema valida que exista un **movimiento de MUESTREO** asociado al numero de analisis. Si no hay muestreo registrado, el sistema rechaza la operacion.

Esto garantiza que:
- Se tomo muestra fisica del lote
- El numero de analisis es valido y corresponde al lote

### 3.3 Estados Posibles del Lote

| Estado Actual | Puede Recibir Resultado | Motivo |
|---------------|-------------------------|--------|
| RECIBIDO | NO | Requiere CU2 primero |
| CUARENTENA | SI | Estado correcto para dictaminar |
| APROBADO | NO | Ya tiene dictamen |
| RECHAZADO | NO | Ya tiene dictamen |
| LIBERADO | NO | Ya esta en uso |
| DEVUELTO | NO | Fue devuelto al proveedor |

---

## 4. Formulario de Resultado

### 4.1 Seleccion del Lote/Analisis

El usuario puede seleccionar el lote de dos formas:

**Opcion 1 - Por Lote:**
| Campo | Muestra |
|-------|---------|
| Nombre Producto | Ibuprofeno 400mg |
| Codigo Lote | 1-05701_25110001 |
| Nro Analisis | ANA-2025-0150 |

**Opcion 2 - Por Analisis:**
| Campo | Muestra |
|-------|---------|
| Nro Analisis | ANA-2025-0150 |
| Codigo Lote | 1-05701_25110001 |

### 4.2 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Lote o Analisis** | SI | Selector | Lote/Analisis a dictaminar | - |
| **Resultado (Dictamen)** | SI | Selector | APROBADO o RECHAZADO | - |
| **Fecha Realizado Analisis** | SI | Fecha | Fecha de finalizacion del analisis | Hoy |
| **Fecha Reanalisis** | NO* | Fecha | Fecha para proximo reanalisis | - |
| **Fecha Vencimiento** | SI** | Fecha | Fecha de vencimiento del lote | - |
| **Titulo (%)** | NO* | Numero | Porcentaje de pureza/potencia | 100 |
| **Motivo del Cambio/Observaciones** | SI | Texto | Descripcion del resultado | - |

*Solo aplica y se muestra para resultado APROBADO de productos que no son Unidad de Venta
**Obligatorio para productos UNIDAD_VENTA cuando se aprueba. La fecha se asigna al campo fechaVencimientoProveedor del lote.

### 4.3 Comportamiento Condicional

**Si el resultado es APROBADO:**
- Se habilitan: Fecha Reanalisis, Fecha Vencimiento, Titulo
- El campo Titulo tiene valor por defecto 100%
- Para Unidades de Venta: Fecha Reanalisis se oculta (no aplica)

**Si el resultado es RECHAZADO:**
- Se deshabilitan/ocultan: Fecha Reanalisis, Fecha Vencimiento, Titulo
- Solo se requiere el Motivo del Cambio

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Lote | Debe seleccionar uno | "Lote no encontrado" |
| 2 | Lote | Debe existir y estar activo | "Lote no encontrado" |
| 3 | Resultado | Debe seleccionar APROBADO o RECHAZADO | "Debe seleccionar un resultado" |
| 4 | Fecha Realizado | Debe ingresar fecha | "La fecha es obligatoria" |
| 5 | Motivo del Cambio/Observaciones | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 6 | Motivo del Cambio/Observaciones | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 7 | Motivo del Cambio/Observaciones | Maximo 300 caracteres | "El motivo no puede exceder 300 caracteres" |

### 5.2 Validaciones de Muestreo

| Validacion | Mensaje de Error |
|------------|------------------|
| Debe existir un movimiento de MUESTREO para el Nro de Analisis | "No se encontro un MUESTREO realizado para ese Nro de Analisis {nro}" |

### 5.3 Validaciones de Fecha

| Validacion | Mensaje de Error |
|------------|------------------|
| Fecha de movimiento debe ser igual o posterior al ingreso del lote | "La fecha de movimiento no puede ser anterior a la fecha de ingreso del lote" |
| Fecha de analisis debe ser igual o posterior al ingreso del lote | "La fecha de analisis no puede ser anterior a la fecha de ingreso del lote" |
| Fecha de reanalisis debe ser posterior a fecha de realizacion (si se indica) | "La fecha de reanalisis debe ser posterior a la fecha de realizacion" |
| Fecha de vencimiento debe ser posterior a fecha de realizacion (si se indica) | "La fecha de vencimiento debe ser posterior a la fecha de realizacion" |

### 5.4 Validaciones Especiales para Unidades de Venta

| Validacion | Mensaje de Error |
|------------|------------------|
| Fecha de vencimiento obligatoria para productos UNIDAD_VENTA al aprobar | "La fecha de vencimiento es obligatoria para productos de tipo Unidad de Venta" |

**Nota:** Para productos de tipo UNIDAD_VENTA (producidos internamente por Conifarma), al aprobar el analisis se requiere ingresar la fecha de vencimiento. Esta fecha se asigna automaticamente al campo `fechaVencimientoProveedor` del lote, ya que los productos propios no tienen proveedor externo que defina el vencimiento.

---

## 6. Resultado de la Operacion

### 6.1 Cambios en el Analisis

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Fecha Realizado | null | Fecha indicada |
| Dictamen | null | APROBADO o RECHAZADO |
| Fecha Reanalisis | null | Fecha indicada (si APROBADO) |
| Fecha Vencimiento | null | Fecha indicada (si aplica) |
| Titulo | null | Valor indicado (si APROBADO) |
| Motivo del cambio | null | Motivo del cambio |

### 6.2 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Dictamen | CUARENTENA | APROBADO o RECHAZADO |
| Fecha Vencimiento Proveedor | (anterior) | Fecha Vencimiento del analisis (solo para UV aprobados) |

**Nota:** El estado del lote NO cambia en este CU. El estado cambia en operaciones posteriores (consumo, liberacion, devolucion).

**Nota UV:** Para productos UNIDAD_VENTA, al aprobar se asigna la fecha de vencimiento del analisis al campo `fechaVencimientoProveedor` del lote.

### 6.3 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION |
| Motivo | RESULTADO_ANALISIS |
| Nro Analisis | Numero del analisis dictaminado |
| Dictamen Inicial | CUARENTENA |
| Dictamen Final | APROBADO o RECHAZADO |
| Fecha | Fecha de realizacion del analisis |
| Usuario | Usuario que realizo la operacion |
| Motivo del cambio | "[CU5]" o "[CU6]" + Motivo del cambio |
| Activo | SI |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Resultado Analisis] ────────────────┐
      |                                          |
      | (Seleccionar lote, completar datos)      | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Exito] ◄────────────────────── [Menu Principal]
      |
      | (Nuevo Resultado) o (Ir a Inicio)
      v
[Formulario Resultado] o [Menu Principal]
```

### 7.2 Pantalla de Exito

Muestra:
- Mensaje: "Cambio de dictamen a APROBADO/RECHAZADO exitoso"
- Codigo del lote
- Numero de analisis
- Nuevo dictamen del lote
- Opciones: "Registrar otro resultado" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Despues de CU5 (APROBADO)

| CU | Nombre | Descripcion |
|----|--------|-------------|
| CU7 | Consumo Produccion | Usar el material aprobado en produccion |
| CU21 | Confirmar Lote Liberado | Liberar lote de Unidad de Venta para venta |
| CU8 | Reanalisis | Programar nuevo analisis del lote aprobado |
| CU25 | Ajuste Stock | Ajustar cantidades si es necesario |

### 8.2 Despues de CU6 (RECHAZADO)

| CU | Nombre | Descripcion |
|----|--------|-------------|
| CU4 | Devolucion Compra | Devolver el lote rechazado al proveedor |
| CU25 | Ajuste Stock | Registrar destruccion/descarte del material |

### 8.3 Flujo Tipico Completo

```
CU1: Ingreso Compra (RECIBIDO)
         |
         v
CU2: Cuarentena (CUARENTENA + Nro Analisis)
         |
         v
CU3: Muestreo (muestra para analisis)
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
 CU7/21      CU4
Produccion  Devolucion
o Venta
```

---

## 9. Casos de Uso Ejemplos

### 9.1 CU5: Aprobacion de API

**Situacion:** El analisis de un lote de Paracetamol dio conforme en todos los ensayos.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05701_25110005 |
| Producto | Paracetamol (1-05701) |
| Nro Analisis | ANA-2025-0175 |
| Dictamen Actual | CUARENTENA |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Resultado | APROBADO |
| Fecha Realizado | 06/12/2025 |
| Fecha Reanalisis | 06/06/2026 |
| Fecha Vencimiento | 06/12/2027 |
| Titulo | 99.8 |
| Motivo del Cambio | Aprobado segun protocolo ANA-PCMOL-001. Ensayos fisicoquimicos conformes. Pureza 99.8%. |

**Resultado:**
- Analisis ANA-2025-0175: Dictamen APROBADO, Fecha 06/12/2025
- Lote: Dictamen cambia de CUARENTENA a APROBADO
- Movimiento de MODIFICACION con "[CU5] Aprobado segun protocolo..."

### 9.2 CU6: Rechazo por Contaminacion

**Situacion:** El analisis microbiologico detecto contaminacion en un lote de excipiente.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | 1-05710_25110020 |
| Producto | Lactosa (1-05710) |
| Nro Analisis | ANA-2025-0180 |
| Dictamen Actual | CUARENTENA |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Resultado | RECHAZADO |
| Fecha Realizado | 06/12/2025 |
| Motivo del Cambio | Rechazado por contaminacion microbiologica. Recuento de aerobios mesofilos excede limite (>1000 UFC/g vs limite 100 UFC/g). |

**Resultado:**
- Analisis ANA-2025-0180: Dictamen RECHAZADO
- Lote: Dictamen cambia de CUARENTENA a RECHAZADO
- Movimiento de MODIFICACION con "[CU6] Rechazado por contaminacion..."
- Siguiente paso: CU4 (Devolucion) o CU25 (Ajuste para descarte)

### 9.3 CU5: Aprobacion de Unidad de Venta

**Situacion:** Un lote de comprimidos pasa el analisis de liberacion.

**Lote seleccionado:**
| Campo | Valor |
|-------|-------|
| Codigo Lote | UDV-001/25120001 |
| Producto | Ibuprofeno 400mg Blister x 20 |
| Nro Analisis | ANA-2025-0185 |
| Tipo Producto | UNIDAD_VENTA |

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Resultado | APROBADO |
| Fecha Realizado | 06/12/2025 |
| Fecha Vencimiento | 06/12/2027 |
| Motivo del Cambio | Aprobado para liberacion. Disolucion conforme, uniformidad de contenido OK, aspecto visual correcto. |

**Nota:** El campo Fecha Reanalisis no aparece para Unidades de Venta.

**Resultado:**
- Lote APROBADO, listo para CU21 (Confirmar Liberacion)

### 9.4 Caso Error: Sin Muestreo Previo

**Situacion:** El usuario intenta dictaminar un lote que no tiene muestreo registrado.

**Resultado:**
- El sistema rechaza la operacion
- Mensaje: "No se encontro un MUESTREO realizado para ese Nro de Analisis ANA-2025-0190"
- El usuario debe primero ejecutar CU3 (Muestreo)

### 9.5 Caso Error: Lote No Esta en Cuarentena

**Situacion:** El usuario busca dictaminar un lote que ya fue aprobado.

**Resultado:**
- El lote NO aparece en la lista de seleccion
- Solo aparecen lotes con dictamen CUARENTENA y analisis en curso

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿Puedo dictaminar sin haber hecho muestreo?**
R: No. El sistema valida que exista un movimiento de MUESTREO asociado al numero de analisis.

**P: ¿Puedo cambiar el resultado despues de dictaminar?**
R: No directamente. Deberia usar CU26 (Reverso) si fue un error, o CU8 (Reanalisis) si necesita un nuevo analisis.

**P: ¿Que diferencia hay entre fecha de reanalisis y fecha de vencimiento?**
R: La fecha de reanalisis indica cuando se debe volver a analizar el lote para confirmar que sigue conforme. La fecha de vencimiento indica cuando el material ya no puede usarse.

**P: ¿Por que no aparece el campo Fecha Reanalisis para Unidades de Venta?**
R: Las Unidades de Venta son productos terminados que no se reanalizan; se venden antes de su vencimiento.

### 10.2 Sobre los Resultados

**P: ¿Que pasa si apruebo un lote por error?**
R: Use CU26 (Reverso) si fue inmediatamente despues. Si ya se uso el lote, debera documentar la incidencia segun procedimientos de calidad.

**P: ¿Que pasa con el lote rechazado?**
R: Queda disponible para devolucion (CU4) o ajuste de stock (CU25). No puede usarse en produccion ni venderse.

**P: ¿El titulo es obligatorio para aprobar?**
R: No es obligatorio, pero tiene valor por defecto 100%. Se usa para APIs donde es importante registrar la pureza.

### 10.3 Sobre la Lista de Lotes

**P: ¿Por que no veo un lote que deberia estar en la lista?**
R: Verificar que el lote:
- Este activo (no eliminado)
- Tenga dictamen CUARENTENA
- Tenga un analisis sin dictaminar (en curso)
- Tenga stock disponible

**P: ¿Por que hay dos selectores (por lote y por analisis)?**
R: Para facilitar la busqueda. Si el usuario conoce el numero de analisis, puede buscarlo directamente. Si conoce el lote, puede seleccionarlo y el sistema muestra el analisis asociado.

### 10.4 Sobre Errores

**P: ¿Que hago si la fecha de analisis es incorrecta?**
R: Use CU26 (Reverso) para anular el resultado y vuelva a dictaminar con la fecha correcta.

**P: ¿Puedo dictaminar el mismo lote dos veces?**
R: No en el mismo analisis. Si necesita un nuevo analisis despues de aprobar, use CU8 (Reanalisis).

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Gerente Control Calidad o Administrador
2. **Para que:** Registrar resultado de analisis (APROBADO o RECHAZADO)
3. **Pre-requisito:** Lote en CUARENTENA con muestreo realizado
4. **Tipo de movimiento:** MODIFICACION
5. **Motivo del movimiento:** RESULTADO_ANALISIS
6. **CU5 (APROBADO):** Registra fechas de reanalisis/vencimiento y titulo
7. **CU6 (RECHAZADO):** Solo registra el motivo del rechazo
8. **Campo critico:** Motivo del Cambio/Observaciones (mínimo 20 caracteres)
9. **Restriccion importante:** Debe existir MUESTREO previo para el analisis

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU5/CU6)

| CU | Nombre | Resultado que Habilita |
|----|--------|------------------------|
| CU2 | Dictamen Cuarentena | Crea el analisis en curso |
| CU3 | Muestreo | Registra el muestreo requerido |

### Operaciones Siguientes (despues de CU5 - APROBADO)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU7 | Consumo Produccion | Lote APROBADO tipo API/Excipiente |
| CU21 | Confirmar Liberacion | Lote APROBADO tipo Unidad de Venta |
| CU8 | Reanalisis | Lote APROBADO que requiere nuevo analisis |

### Operaciones Siguientes (despues de CU6 - RECHAZADO)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU4 | Devolucion Compra | Lote RECHAZADO a devolver |
| CU25 | Ajuste Stock | Lote RECHAZADO a descartar |

---

**Fin del Documento - CU5_CU6_RESULTADO_ANALISIS v1.1 - Especificacion Funcional**
