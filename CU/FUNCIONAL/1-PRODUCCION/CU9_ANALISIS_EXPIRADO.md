# CU9 - ANALISIS EXPIRADO: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** MODIFICACION (Automatica)
**Motivo:** EXPIRACION_ANALISIS

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Ejecucion Automatica](#2-ejecucion-automatica)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Resultado de la Operacion](#4-resultado-de-la-operacion)
5. [Flujo del Proceso](#5-flujo-del-proceso)
6. [Operaciones de Recuperacion](#6-operaciones-de-recuperacion)
7. [Casos de Uso Ejemplos](#7-casos-de-uso-ejemplos)
8. [Preguntas Frecuentes](#8-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso es un **proceso automatico** que detecta lotes cuya **fecha de reanalisis ha sido alcanzada o superada** y cambia su dictamen a **ANALISIS_EXPIRADO**. Esto bloquea el uso del lote hasta que se realice un nuevo analisis.

### 1.2 Concepto de Expiracion de Analisis

La expiracion de analisis:
- Es un **proceso batch** que se ejecuta automaticamente
- Detecta lotes con `fechaReanalisisVigente <= hoy`
- Cambia el dictamen del lote a **ANALISIS_EXPIRADO**
- Registra un **movimiento de MODIFICACION** con motivo EXPIRACION_ANALISIS
- **Bloquea** el uso del lote hasta nuevo analisis

### 1.3 Diferencia con Vencimiento (CU10)

| Aspecto | CU9 - Analisis Expirado | CU10 - Vencimiento |
|---------|-------------------------|---------------------|
| Fecha evaluada | fechaReanalisisVigente | fechaVencimientoVigente |
| Dictamen resultante | ANALISIS_EXPIRADO | VENCIDO |
| Recuperable | SI (nuevo analisis via CU2) | NO (terminal) |
| Producto utilizable | NO (hasta nuevo analisis) | NO (nunca mas) |

### 1.4 Resultado del CU

Al detectar lotes con analisis expirado:
- El **dictamen** cambia a ANALISIS_EXPIRADO
- Se registra un **movimiento de MODIFICACION** con motivo EXPIRACION_ANALISIS
- El lote queda **bloqueado** para consumo, venta o cualquier operacion
- Se puede **recuperar** mediante CU2 (Cuarentena) + CU3 (Muestreo) + CU5 (Resultado)

---

## 2. Ejecucion Automatica

### 2.1 Programacion

| Aspecto | Valor |
|---------|-------|
| Frecuencia | Diaria |
| Hora de ejecucion | 05:00 AM (hora del servidor) |
| Configuracion | `@Scheduled(cron = "0 0 5 * * *")` |
| Servicio | FechaValidatorService |

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
(CU9) ANALISIS EXPIRADO POR FECHA: {fecha_hoy}
```

---

## 3. Pre-condiciones

### 3.1 Lotes Afectados

Un lote sera procesado si cumple **TODAS** estas condiciones:

| Condicion | Descripcion |
|-----------|-------------|
| **Activo** | El lote debe estar activo (no eliminado) |
| **Con stock** | Debe tener cantidad actual > 0 |
| **Fecha reanalisis alcanzada** | `fechaReanalisisVigente <= hoy` |

### 3.2 Fecha de Reanalisis Vigente

La fecha de reanalisis vigente proviene de:
1. El **ultimo analisis aprobado** del lote, o
2. La **fecha de reanalisis del proveedor** si no hay analisis propio

### 3.3 Dictamenes de Entrada

Este proceso puede afectar lotes con cualquier dictamen que tenga fecha de reanalisis definida:

| Dictamen Actual | Procesable |
|-----------------|------------|
| APROBADO | SI (caso mas comun) |
| LIBERADO | SI |
| Otros con fecha reanalisis | SI |

---

## 4. Resultado de la Operacion

### 4.1 Cambios en el Lote

| Atributo | Valor Anterior | Valor Nuevo |
|----------|----------------|-------------|
| Dictamen | (anterior) | ANALISIS_EXPIRADO |

**Nota:** El estado del lote NO cambia. Solo cambia el dictamen.

### 4.2 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | MODIFICACION |
| Motivo | EXPIRACION_ANALISIS |
| Lote | Lote afectado |
| Dictamen Inicial | Dictamen anterior del lote |
| Dictamen Final | ANALISIS_EXPIRADO |
| Usuario | system_auto |
| Fecha | Fecha de ejecucion del proceso |
| Motivo del cambio | "(CU9) ANALISIS EXPIRADO POR FECHA: {fecha}" |
| Activo | SI |

### 4.3 Efectos Colaterales

| Efecto | Descripcion |
|--------|-------------|
| Bloqueo de operaciones | El lote no puede usarse para CU7, CU22, etc. |
| Visible en alertas | El lote aparece en reportes de lotes bloqueados |
| Requiere accion | Usuario debe iniciar nuevo analisis (CU2) |

---

## 5. Flujo del Proceso

### 5.1 Secuencia de Ejecucion

```
[Scheduler 5:00 AM]
       |
       v
[Buscar lotes con stock]
       |
       v
[Filtrar: fechaReanalisisVigente <= hoy]
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
[Actualizar dictamen lote a ANALISIS_EXPIRADO]
       |
       v
[Guardar lote y movimiento]
       |
       v
[Log: "Reanalisis expirado: {lote}"]
       |
       v
[Siguiente lote o FIN]
```

### 5.2 Transaccionalidad

- Cada lote se procesa en una **transaccion independiente**
- Si falla un lote, los demas siguen procesandose
- Los errores se registran en el log del sistema

---

## 6. Operaciones de Recuperacion

### 6.1 Flujo de Recuperacion

Para recuperar un lote con analisis expirado:

```
[Lote ANALISIS_EXPIRADO]
         |
         v
[CU2: Dictamen Cuarentena]
  - Asignar nuevo nro de analisis
  - Dictamen cambia a CUARENTENA
         |
         v
[CU3: Muestreo]
  - Tomar nueva muestra
         |
         v
[CU5: Resultado Aprobado]
  - Asignar nuevas fechas
  - Dictamen cambia a APROBADO
         |
         v
[Lote recuperado y utilizable]
```

### 6.2 Usuarios Responsables

| Paso | CU | Usuario Responsable |
|------|-----|---------------------|
| Iniciar cuarentena | CU2 | Analista Control Calidad |
| Tomar muestra | CU3 | Analista Control Calidad |
| Dictaminar resultado | CU5 | Gerente Control Calidad |

### 6.3 Tiempo de Bloqueo

El lote permanece bloqueado hasta completar el flujo de recuperacion. No hay forma de "saltear" el proceso de analisis.

---

## 7. Casos de Uso Ejemplos

### 7.1 Lote Aprobado con Reanalisis Vencido

**Situacion:** Un lote de materia prima fue aprobado hace 6 meses con fecha de reanalisis hoy.

**Estado inicial:**
| Campo | Valor |
|-------|-------|
| Lote | 1-05701_25060001 |
| Dictamen | APROBADO |
| Fecha Reanalisis | 06/12/2025 |
| Fecha Hoy | 06/12/2025 |

**Proceso automatico (5:00 AM):**
1. Sistema detecta `fechaReanalisis <= hoy`
2. Crea movimiento MODIFICACION con motivo EXPIRACION_ANALISIS
3. Cambia dictamen a ANALISIS_EXPIRADO

**Estado final:**
| Campo | Valor |
|-------|-------|
| Dictamen | ANALISIS_EXPIRADO |
| Movimiento | "CU9 - ANALISIS EXPIRADO POR FECHA: 06/12/2025" |

**Recuperacion:**
- Analista ejecuta CU2 → asigna nuevo nro de analisis
- Analista ejecuta CU3 → toma muestra
- Gerente ejecuta CU5 → aprueba con nuevas fechas

### 7.2 Lote Liberado con Reanalisis Vencido

**Situacion:** Un lote de producto terminado liberado para venta tiene reanalisis vencido.

**Estado inicial:**
| Campo | Valor |
|-------|-------|
| Lote | UDV-001/25030001 |
| Dictamen | LIBERADO |
| Fecha Reanalisis | 05/12/2025 |
| Fecha Hoy | 06/12/2025 |

**Resultado:**
- Dictamen cambia a ANALISIS_EXPIRADO
- El producto **NO puede venderse** hasta nuevo analisis
- Las trazas permanecen pero el lote esta bloqueado

### 7.3 Multiples Lotes Afectados

**Situacion:** El proceso batch encuentra 5 lotes con reanalisis vencido.

**Resultado:**
- Los 5 lotes se procesan secuencialmente
- Cada uno recibe su propio movimiento de MODIFICACION
- Si uno falla, los demas continuan
- Se registra log para cada lote procesado

---

## 8. Preguntas Frecuentes

### 8.1 Sobre el Proceso

**P: ¿A que hora se ejecuta el proceso?**
R: A las 5:00 AM hora del servidor, todos los dias.

**P: ¿Se puede ejecutar manualmente?**
R: No esta diseñado para ejecucion manual. Es un proceso automatico.

**P: ¿Que pasa si el servidor esta caido a las 5 AM?**
R: El proceso no se ejecuta. Se ejecutara al dia siguiente. Los lotes que deberian haberse procesado se procesaran entonces.

**P: ¿Se puede revertir este proceso?**
R: No directamente. No existe reverso para CU9. La recuperacion es via nuevo analisis (CU2 → CU3 → CU5).

### 8.2 Sobre los Lotes

**P: ¿Que lotes se ven afectados?**
R: Lotes activos con stock > 0 y fechaReanalisisVigente <= fecha de hoy.

**P: ¿Puedo usar el lote mientras esta en ANALISIS_EXPIRADO?**
R: No. El lote esta bloqueado para todas las operaciones hasta completar nuevo analisis.

**P: ¿El stock se pierde?**
R: No. El stock permanece intacto. Solo cambia el dictamen que bloquea las operaciones.

### 8.3 Diferencias con CU10

**P: ¿Cual es la diferencia con vencimiento?**
R: CU9 (Analisis Expirado) es recuperable con nuevo analisis. CU10 (Vencimiento) es terminal - el producto no puede usarse mas.

**P: ¿Pueden ejecutarse ambos el mismo dia?**
R: Si. Primero se procesan los de analisis expirado, luego los vencidos. Un lote podria tener ambas fechas alcanzadas.

---

## Resumen de Puntos Clave

1. **Tipo:** Proceso automatico (batch)
2. **Ejecucion:** Diaria a las 5:00 AM
3. **Usuario:** system_auto (ADMIN)
4. **Condicion:** fechaReanalisisVigente <= hoy
5. **Dictamen resultante:** ANALISIS_EXPIRADO
6. **Motivo del movimiento:** EXPIRACION_ANALISIS
7. **Efecto:** Bloquea el lote para cualquier operacion
8. **Recuperable:** SI (via CU2 → CU3 → CU5)
9. **Reversible:** NO (no existe reverso para CU9)
10. **Afecta stock:** NO (solo cambia dictamen)

---

## Relacion con Otros CUs

### Operaciones que Pueden Preceder a CU9

| CU | Nombre | Situacion |
|----|--------|-----------|
| CU5 | Resultado Aprobado | Lote aprobado con fecha reanalisis definida |
| CU21 | Confirmar Liberado | Lote liberado con fecha reanalisis del analisis |

### Operaciones de Recuperacion (despues de CU9)

| CU | Nombre | Paso |
|----|--------|------|
| CU2 | Dictamen Cuarentena | 1. Asignar nuevo analisis |
| CU3 | Muestreo | 2. Tomar muestra |
| CU5 | Resultado Aprobado | 3. Aprobar con nuevas fechas |

### Operaciones Bloqueadas por CU9

| CU | Nombre | Motivo |
|----|--------|--------|
| CU7 | Consumo Produccion | Requiere dictamen APROBADO |
| CU22 | Venta Producto | Requiere dictamen LIBERADO |
| CU21 | Confirmar Liberado | Requiere dictamen APROBADO |

---

**Fin del Documento - CU9_ANALISIS_EXPIRADO v1.0 - Especificacion Funcional**
