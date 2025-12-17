# CU10 - VENCIMIENTO DE LOTE: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** MODIFICACION (Automatica)
**Motivo:** VENCIMIENTO

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Ejecucion Automatica](#2-ejecucion-automatica)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Resultado de la Operacion](#4-resultado-de-la-operacion)
5. [Flujo del Proceso](#5-flujo-del-proceso)
6. [Gestion de Productos Vencidos](#6-gestion-de-productos-vencidos)
7. [Casos de Uso Ejemplos](#7-casos-de-uso-ejemplos)
8. [Preguntas Frecuentes](#8-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso es un **proceso automatico** que detecta lotes cuya **fecha de vencimiento ha sido alcanzada o superada** y cambia su dictamen a **VENCIDO**. Este es un estado **terminal** - el producto no puede usarse bajo ninguna circunstancia.

### 1.2 Concepto de Vencimiento

El vencimiento de lote:
- Es un **proceso batch** que se ejecuta automaticamente
- Detecta lotes con `fechaVencimientoVigente <= hoy`
- Cambia el dictamen del lote a **VENCIDO** (estado terminal)
- **Cancela** cualquier analisis en curso
- Registra un **movimiento de MODIFICACION** con motivo VENCIMIENTO
- El producto **NO es recuperable** - debe destruirse

### 1.3 Diferencia con Analisis Expirado (CU9)

| Aspecto | CU9 - Analisis Expirado | CU10 - Vencimiento |
|---------|-------------------------|---------------------|
| Fecha evaluada | fechaReanalisisVigente | fechaVencimientoVigente |
| Dictamen resultante | ANALISIS_EXPIRADO | VENCIDO |
| Recuperable | SI (nuevo analisis via CU2) | **NO (terminal)** |
| Producto utilizable | NO (hasta nuevo analisis) | **NO (nunca mas)** |
| Cancela analisis en curso | NO | **SI** |
| Accion requerida | Nuevo analisis | Destruccion (CU25) |

### 1.4 Resultado del CU

Al detectar lotes vencidos:
- El **dictamen** cambia a VENCIDO (terminal)
- Si hay **analisis en curso**, se cancela (dictamen = CANCELADO)
- Se registra un **movimiento de MODIFICACION** con motivo VENCIMIENTO
- El lote queda **permanentemente bloqueado**
- Solo se puede ejecutar **CU25 (Ajuste Stock)** para registrar destruccion

---

## 2. Ejecucion Automatica

### 2.1 Programacion

| Aspecto | Valor |
|---------|-------|
| Frecuencia | Diaria |
| Hora de ejecucion | 05:00 AM (hora del servidor) |
| Configuracion | `@Scheduled(cron = "0 0 5 * * *")` |
| Servicio | FechaValidatorService |
| Orden | Se ejecuta DESPUES de CU9 |

### 2.2 Usuario del Sistema

Las operaciones automaticas se ejecutan con un usuario especial:

| Atributo | Valor |
|----------|-------|
| Username | system_auto |
| Rol | ADMIN |
| Tipo | Usuario del sistema (no interactivo) |

### 2.3 Observaciones Automaticas

El movimiento se registra con motivo del cambio automatico:

```
(CU10) VENCIMIENTO AUTOMATICO POR FECHA: {fecha_hoy}
```

---

## 3. Pre-condiciones

### 3.1 Lotes Afectados

Un lote sera procesado si cumple **TODAS** estas condiciones:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Con stock** | Debe tener cantidad actual > 0 |
| **Fecha vencimiento alcanzada** | `fechaVencimientoVigente <= hoy` |

### 3.2 Fecha de Vencimiento Vigente

La fecha de vencimiento vigente proviene de:
1. El **ultimo analisis aprobado** del lote, o
2. La **fecha de vencimiento del proveedor** si no hay analisis propio

### 3.3 Dictamenes de Entrada

Este proceso puede afectar lotes con cualquier dictamen que tenga fecha de vencimiento definida:

| Dictamen Actual | Procesable | Caso Tipico |
|-----------------|------------|-------------|
| LIBERADO | SI | Producto listo para venta que vencio |
| APROBADO | SI | Materia prima aprobada que vencio |
| CUARENTENA | SI | Producto en analisis que vencio |
| ANALISIS_EXPIRADO | SI | Ya tenia reanalisis vencido, ahora vence |

---

## 4. Resultado de la Operacion

### 4.1 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Dictamen | (anterior) | VENCIDO |

**Nota:** El estado del lote NO cambia. Solo cambia el dictamen.

### 4.2 Cancelacion de Analisis en Curso

Si el lote tiene un analisis en curso (sin dictamen), este se cancela:

| Atributo del Analisis | Valor Anterior | Valor Nuevo |
|-----------------------|----------------|-------------|
| Dictamen | null (en curso) | CANCELADO |

### 4.3 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION |
| Motivo | VENCIMIENTO |
| Lote | Lote afectado |
| Dictamen Inicial | Dictamen anterior del lote |
| Dictamen Final | VENCIDO |
| Usuario | system_auto |
| Fecha | Fecha de ejecucion del proceso |
| Motivo del cambio | "(CU10) VENCIMIENTO AUTOMATICO POR FECHA: {fecha}" |
| Activo | SI |

### 4.4 Efectos Permanentes

| Efecto | Descripcion |
|--------|-------------|
| Bloqueo total | El lote no puede usarse para NINGUNA operacion |
| No recuperable | No existe forma de "desvenzer" un producto |
| Requiere destruccion | Solo CU25 (Ajuste Stock) para registrar destruccion |
| Analisis cancelado | Cualquier analisis en curso queda CANCELADO |

---

## 5. Flujo del Proceso

### 5.1 Secuencia de Ejecucion

```
[Scheduler 5:00 AM]
       |
       v
[Ejecutar CU9 primero]
       |
       v
[Buscar lotes con stock]
       |
       v
[Filtrar: fechaVencimientoVigente <= hoy]
       |
       +---> (Sin lotes) ---> FIN
       |
       v
[Para cada lote afectado]
       |
       v
[Crear MovimientoDTO]
       |
       v
[Crear Movimiento MODIFICACION]
       |
       v
[Actualizar dictamen lote a VENCIDO]
       |
       v
[¿Tiene analisis en curso?]
       |
       +---> SI ---> [Marcar analisis como CANCELADO]
       |
       v
[Guardar lote, movimiento y analisis]
       |
       v
[Log: "Vencido: {lote}"]
       |
       v
[Siguiente lote o FIN]
```

### 5.2 Orden de Ejecucion

El proceso batch ejecuta primero CU9 (Analisis Expirado) y luego CU10 (Vencimiento). Esto significa que si un lote tiene ambas fechas vencidas, primero seria marcado como ANALISIS_EXPIRADO y luego como VENCIDO (el estado final sera VENCIDO).

### 5.3 Transaccionalidad

- Cada lote se procesa en una **transaccion independiente**
- Si falla un lote, los demas siguen procesandose
- Los errores se registran en el log del sistema

---

## 6. Gestion de Productos Vencidos

### 6.1 Acciones Permitidas

Para un lote VENCIDO, **solo una operacion** esta permitida:

| CU | Nombre | Proposito |
|----|--------|-----------|
| CU25 | Ajuste Stock | Registrar destruccion/descarte del producto |

### 6.2 Procedimiento de Destruccion

```
[Lote VENCIDO]
       |
       v
[Coordinar destruccion fisica]
       |
       v
[CU25: Ajuste Stock]
  - Cantidad a ajustar: toda la existencia
  - Motivo: "Destruccion por vencimiento - Lote {codigo}"
       |
       v
[Documentar acta de destruccion (fuera del sistema)]
       |
       v
[Lote con stock = 0, estado DESCARTADO]
```

### 6.3 Requisitos ANMAT

Segun normativa ANMAT, los productos vencidos deben:
1. Ser identificados y segregados
2. Destruirse de forma documentada
3. Mantener registro de la destruccion
4. No mezclarse con productos vigentes

El sistema cumple con esto mediante:
- Dictamen VENCIDO visible en todas las pantallas
- Bloqueo de todas las operaciones excepto ajuste
- Registro del movimiento de ajuste con motivo documentado

---

## 7. Casos de Uso Ejemplos

### 7.1 Producto Liberado que Vence

**Situacion:** Un lote de producto terminado liberado para venta alcanza su fecha de vencimiento.

**Estado inicial:**
| Campo | Valor |
|-------|-------|
| Lote | UDV-001/25030001 |
| Dictamen | LIBERADO |
| Fecha Vencimiento | 06/12/2025 |
| Fecha Hoy | 06/12/2025 |
| Trazas | 50001-50100 (100 unidades) |

**Proceso automatico (5:00 AM):**
1. Sistema detecta `fechaVencimiento <= hoy`
2. Crea movimiento MODIFICACION con motivo VENCIMIENTO
3. Cambia dictamen a VENCIDO

**Estado final:**
| Campo | Valor |
|-------|-------|
| Dictamen | VENCIDO |
| Movimiento | "CU10 - VENCIMIENTO AUTOMATICO POR FECHA: 06/12/2025" |
| Operaciones permitidas | Solo CU25 (Ajuste) |

**Accion requerida:**
- Supervisor ejecuta CU25 con motivo "Destruccion por vencimiento"
- Se registra acta de destruccion

### 7.2 Lote en Cuarentena con Analisis en Curso que Vence

**Situacion:** Un lote en cuarentena con analisis pendiente alcanza fecha de vencimiento.

**Estado inicial:**
| Campo | Valor |
|-------|-------|
| Lote | 1-05701_25060001 |
| Dictamen | CUARENTENA |
| Analisis en curso | Nro 2025-001234 (sin dictamen) |
| Fecha Vencimiento | 06/12/2025 |

**Proceso automatico:**
1. Sistema detecta vencimiento
2. Cancela el analisis en curso (dictamen = CANCELADO)
3. Cambia dictamen del lote a VENCIDO

**Estado final:**
| Campo | Valor |
|-------|-------|
| Dictamen Lote | VENCIDO |
| Dictamen Analisis | CANCELADO |
| Observacion | El analisis ya no tiene sentido - producto inutilizable |

### 7.3 Lote con Analisis Expirado y Vencimiento el Mismo Dia

**Situacion:** Un lote tiene tanto fecha de reanalisis como vencimiento en la misma fecha.

**Estado inicial:**
| Campo | Valor |
|-------|-------|
| Lote | 1-05702_25040001 |
| Fecha Reanalisis | 06/12/2025 |
| Fecha Vencimiento | 06/12/2025 |
| Fecha Hoy | 06/12/2025 |

**Proceso automatico:**
1. **CU9 primero:** Marca como ANALISIS_EXPIRADO
2. **CU10 despues:** Marca como VENCIDO

**Estado final:**
| Campo | Valor |
|-------|-------|
| Dictamen | VENCIDO (el ultimo en ejecutarse) |
| Movimientos | 2 movimientos registrados (CU9 y CU10) |

### 7.4 Multiples Lotes Vencidos

**Situacion:** El proceso batch encuentra 10 lotes vencidos.

**Resultado:**
- Los 10 lotes se procesan secuencialmente
- Cada uno recibe su propio movimiento de MODIFICACION
- Analisis en curso de cada lote se cancelan
- Se registra log para cada lote procesado

---

## 8. Preguntas Frecuentes

### 8.1 Sobre el Proceso

**P: ¿A que hora se ejecuta el proceso?**
R: A las 5:00 AM hora del servidor, todos los dias, despues de CU9.

**P: ¿Se puede revertir el vencimiento?**
R: **NO.** El vencimiento es irreversible. No existe reverso para CU10. El producto debe destruirse.

**P: ¿Por que se cancela el analisis en curso?**
R: Porque no tiene sentido analizar un producto que ya no puede usarse. El analisis queda con dictamen CANCELADO para registro.

### 8.2 Sobre los Lotes

**P: ¿Que pasa con el stock?**
R: El stock permanece registrado, pero el lote esta bloqueado. Debe ejecutarse CU25 para dar de baja el stock.

**P: ¿Puedo vender un producto vencido?**
R: **NO.** El sistema bloquea absolutamente todas las operaciones excepto CU25 (Ajuste).

**P: ¿Las trazas se pierden?**
R: No. Las trazas permanecen asociadas al lote para trazabilidad historica.

### 8.3 Sobre Destruccion

**P: ¿Como registro la destruccion?**
R: Usando CU25 (Ajuste Stock). Documente el motivo como "Destruccion por vencimiento" y adjunte referencia al acta de destruccion.

**P: ¿Es obligatorio destruir el producto?**
R: Segun normativa ANMAT, los productos vencidos deben destruirse de forma documentada. El sistema no permite ninguna otra operacion.

### 8.4 Diferencias con CU9

**P: ¿Puede un lote tener ambos vencimientos?**
R: Si. Pueden vencer tanto el analisis (CU9) como el producto (CU10). El estado final siempre sera VENCIDO porque CU10 se ejecuta despues.

**P: ¿Cual es peor, analisis expirado o vencido?**
R: VENCIDO es terminal y definitivo. ANALISIS_EXPIRADO se puede recuperar con nuevo analisis.

---

## Resumen de Puntos Clave

1. **Tipo:** Proceso automatico (batch)
2. **Ejecucion:** Diaria a las 5:00 AM (despues de CU9)
3. **Usuario:** system_auto (ADMIN)
4. **Condicion:** fechaVencimientoVigente <= hoy
5. **Dictamen resultante:** VENCIDO (terminal)
6. **Motivo del movimiento:** VENCIMIENTO
7. **Efecto:** Bloquea permanentemente el lote
8. **Recuperable:** **NO** (estado terminal)
9. **Reversible:** **NO** (no existe reverso para CU10)
10. **Cancela analisis en curso:** SI (dictamen = CANCELADO)
11. **Unica operacion permitida:** CU25 (Ajuste Stock para destruccion)

---

## Relacion con Otros CUs

### Operaciones que Pueden Preceder a CU10

| CU | Nombre | Situacion |
|----|--------|-----------|
| CU5 | Resultado Aprobado | Lote aprobado con fecha vencimiento definida |
| CU21 | Confirmar Liberado | Lote liberado con fecha vencimiento |
| CU9 | Analisis Expirado | Lote ya tenia reanalisis vencido |

### Unica Operacion Permitida despues de CU10

| CU | Nombre | Proposito |
|----|--------|-----------|
| CU25 | Ajuste Stock | Registrar destruccion del producto vencido |

### Operaciones BLOQUEADAS por CU10

| CU | Nombre | Motivo |
|----|--------|--------|
| CU2 | Dictamen Cuarentena | No se puede analizar producto vencido |
| CU3 | Muestreo | No tiene sentido muestrear vencido |
| CU5 | Resultado Analisis | No aplica |
| CU7 | Consumo Produccion | Prohibido usar en produccion |
| CU21 | Confirmar Liberado | No aplica |
| CU22 | Venta Producto | Prohibido vender producto vencido |
| CU26 | Reverso | No existe reverso para vencimiento |

---

**Fin del Documento - CU10_VENCIMIENTO_LOTE v1.0 - Especificacion Funcional**
