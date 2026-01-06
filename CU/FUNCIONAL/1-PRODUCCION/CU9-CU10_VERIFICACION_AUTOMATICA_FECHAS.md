# CU9/CU10 - PROCESOS AUTOMATICOS: Especificacion Tecnica

**Version:** 1.0
**Fecha:** 2025-12-09
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** MODIFICACION (Automatica)
**Servicio:** FechaValidatorService

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [CU9 - Analisis Expirado](#2-cu9---analisis-expirado)
3. [CU10 - Vencimiento de Lote](#3-cu10---vencimiento-de-lote)
4. [Configuracion Tecnica](#4-configuracion-tecnica)
5. [Manejo de Errores](#5-manejo-de-errores)
6. [Auditoria y Logs](#6-auditoria-y-logs)

---

## 1. Descripcion General

### 1.1 Proposito

El servicio `FechaValidatorService` implementa los procesos batch automaticos que detectan y procesan:
- **CU9**: Lotes con analisis expirado (fecha de reanalisis vencida)
- **CU10**: Lotes vencidos (fecha de vencimiento superada)

### 1.2 Ejecucion

| Aspecto | Valor |
|---------|-------|
| **Frecuencia** | Diaria |
| **Horario** | 05:00 AM |
| **Usuario Sistema** | `system_auto` (rol ADMIN) |
| **Orden de Ejecucion** | Primero CU10, luego CU9 |

### 1.3 Cron Expression

```java
@Scheduled(cron = "0 0 5 * * *")
```

---

## 2. CU9 - Analisis Expirado

### 2.1 Condicion de Activacion

Un lote es procesado por CU9 cuando:
- `fechaReanalisisVigente <= hoy`
- Lote tiene stock > 0
- Lote esta activo
- Dictamen es uno de: CUARENTENA, APROBADO, RECIBIDO, LIBERADO, DEVOLUCION_CLIENTES

### 2.2 Postcondiciones

| Atributo | Cambio |
|----------|--------|
| Dictamen | → `ANALISIS_EXPIRADO` |
| Movimiento | MODIFICACION con motivo `EXPIRACION_ANALISIS` |

### 2.3 Recuperabilidad

**SI es recuperable** mediante:
1. CU2 (Dictamen Cuarentena) - Asignar nuevo numero de analisis
2. CU3 (Muestreo) - Tomar nueva muestra
3. CU5 (Resultado Analisis) - Obtener nuevo dictamen

### 2.4 Metodo Principal

```java
List<Lote> findAllLotesAnalisisExpirado() {
    LocalDate hoy = LocalDate.now();
    return loteRepository.findLotesConStockOrder().stream()
        .filter(l -> {
            LocalDate f = l.getFechaReanalisisVigente();
            return f != null && !f.isAfter(hoy); // <= hoy
        })
        .toList();
}
```

---

## 3. CU10 - Vencimiento de Lote

### 3.1 Condicion de Activacion

Un lote es procesado por CU10 cuando:
- `fechaVencimientoVigente <= hoy`
- Lote tiene stock > 0
- Lote esta activo
- Dictamen es uno de: CUARENTENA, APROBADO, RECIBIDO, LIBERADO, DEVOLUCION_CLIENTES, ANALISIS_EXPIRADO

### 3.2 Postcondiciones

| Atributo | Cambio |
|----------|--------|
| Dictamen | → `VENCIDO` (TERMINAL) |
| Movimiento | MODIFICACION con motivo `VENCIMIENTO` |
| Analisis en curso | → `CANCELADO` (si existe) |

### 3.3 Recuperabilidad

**NO es recuperable**. El estado VENCIDO es terminal.

Unica operacion permitida: **CU30 (Ajuste Stock)** para registrar destruccion.

### 3.4 Metodo Principal

```java
List<Lote> findAllLotesVencidos() {
    LocalDate hoy = LocalDate.now();
    return loteRepository.findLotesConStockOrder().stream()
        .filter(l -> {
            LocalDate f = l.getFechaVencimientoVigente();
            return f != null && !f.isAfter(hoy); // <= hoy
        })
        .toList();
}
```

---

## 4. Configuracion Tecnica

### 4.1 Usuario del Sistema

El servicio crea automaticamente el usuario `system_auto` si no existe:

```java
User getSystemUser() {
    return userRepository.findByUsernameWithRole("system_auto")
        .orElseGet(() -> {
            Role adminRole = roleRepository.findByName(RoleEnum.ADMIN.name())
                .orElseGet(() -> roleRepository.save(Role.fromEnum(RoleEnum.ADMIN)));
            User systemUser = new User("system_auto", "SYSTEM_NO_LOGIN", adminRole);
            return userRepository.save(systemUser);
        });
}
```

### 4.2 Observaciones Automaticas

| CU | Formato Motivo del Cambio                          |
|----|----------------------------------------------------|
| CU9 | `[CU9] (CU9) ANALISIS EXPIRADO POR FECHA: {fecha}`       |
| CU10 | `[CU10] (CU10) VENCIMIENTO AUTOMATICO POR FECHA: {fecha}` |

**Nota:** El prefijo `[CUx]` es agregado automaticamente por `formatMotivoDelCambioWithCU()`. El mensaje base es `(CU9) ANALISIS EXPIRADO...` o `(CU10) VENCIMIENTO AUTOMATICO...`.

---

## 5. Manejo de Errores

### 5.1 Estrategia

Si un lote falla durante el procesamiento, el error se registra en logs y se continua con el siguiente lote.

```java
try {
    // Procesar lote
} catch (Exception e) {
    log.error("[CU9] Error procesando expiracion de analisis para lote: {} - {}",
        lote.getCodigoLote(), e.getMessage(), e);
    // Continuar con el siguiente lote
}
```

### 5.2 Logs de Error

| Nivel | Mensaje |
|-------|---------|
| ERROR | `[CU9] Error procesando expiracion de analisis para lote: {codigo} - {mensaje}` |
| ERROR | `[CU10] Error procesando vencimiento para lote: {codigo} - {mensaje}` |

---

## 6. Auditoria y Logs

### 6.1 Logs de Ejecucion

| Evento | Nivel | Mensaje |
|--------|-------|---------|
| Sin lotes CU9 | DEBUG | `[CU9] No hay lotes con analisis expirado para procesar` |
| Inicio CU9 | INFO | `[CU9] Procesando {n} lotes con analisis expirado` |
| Lote procesado CU9 | INFO | `[CU9] Analisis expirado procesado - Lote: {lote}, FechaReanalisis: {fecha}` |
| Sin lotes CU10 | DEBUG | `[CU10] No hay lotes vencidos para procesar` |
| Inicio CU10 | INFO | `[CU10] Procesando {n} lotes vencidos` |
| Lote procesado CU10 | INFO | `[CU10] Lote vencido procesado - Lote: {lote}, FechaVencimiento: {fecha}` |

### 6.2 Movimientos Registrados

Cada lote procesado genera un movimiento con:

| Campo | Valor CU9 | Valor CU10 |
|-------|-----------|------------|
| Tipo | MODIFICACION | MODIFICACION |
| Motivo | EXPIRACION_ANALISIS | VENCIMIENTO |
| Usuario | system_auto | system_auto |
| DictamenInicial | (anterior) | (anterior) |
| DictamenFinal | ANALISIS_EXPIRADO | VENCIDO |

---

## Restricciones de Reverso (CU31)

| CU | Reversible | Motivo |
|----|------------|--------|
| CU9 | **NO** | Proceso automatico irreversible |
| CU10 | **NO** | Estado terminal irreversible |

El servicio `ModifReversoMovimientoService` rechaza explicitamente reversos de estos motivos:
- `VENCIMIENTO`: "No se puede reversar un vencimiento de lote (CU10). El vencimiento es un proceso automático irreversible."
- `EXPIRACION_ANALISIS`: "No se puede reversar una expiración de análisis (CU9). La expiración es un proceso automático."

---

**Fin del Documento - CU9_CU10_PROCESOS_AUTOMATICOS v1.0 - Especificacion Tecnica**
