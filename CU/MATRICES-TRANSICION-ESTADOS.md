# Matrices de Transición de Estados - CONITRACK

**Fecha:** 9 de Diciembre de 2025
**Versión:** 1.0
**Propósito:** Documentación formal de transiciones de estados válidas según reglas de negocio y ANMAT

---

## 1. EstadoEnum - Estados de Cantidad

Define el estado del lote/bulto/traza según su cantidad disponible.

### Valores

| Estado | Descripción | Condición |
|--------|-------------|-----------|
| `NUEVO` | Lote recién ingresado | Cantidad inicial = cantidad actual |
| `DISPONIBLE` | Disponible para operaciones | Cantidad inicial = cantidad actual (trazas) |
| `EN_USO` | Parcialmente consumido | Cantidad inicial > cantidad actual > 0 |
| `CONSUMIDO` | Agotado por producción | Cantidad actual = 0 (por producción) |
| `VENDIDO` | Agotado por venta | Cantidad actual = 0 (por venta) |
| `DEVUELTO` | Agotado por devolución | Cantidad actual = 0 (por devolución compra) |
| `RECALL` | Retirado del mercado | Cantidad actual = 0 (por retiro mercado) |
| `DESCARTADO` | Destruido | Cantidad actual = 0 (por destrucción) |

### Transiciones por Caso de Uso

| CU | Operación | Estado Inicial | Estado Final |
|----|-----------|----------------|--------------|
| CU1 | Alta Ingreso Compra | - | NUEVO |
| CU3 | Muestreo | NUEVO/DISPONIBLE | EN_USO |
| CU4 | Devolución Compra | NUEVO/EN_USO | DEVUELTO |
| CU7 | Consumo Producción | NUEVO/EN_USO | EN_USO/CONSUMIDO |
| CU20 | Ingreso Producción | - | NUEVO |
| CU22 | Venta Producto | NUEVO/EN_USO | EN_USO/VENDIDO |
| CU23 | Devolución Venta | VENDIDO | EN_USO/NUEVO |
| CU24 | Retiro Mercado | VENDIDO/EN_USO | RECALL |
| CU25 | Ajuste Stock | * | * |
| CU26 | Reverso Movimiento | * | (restaura estado anterior) |

---

## 2. DictamenEnum - Estados de Calidad

Define el estado de aprobación del lote según análisis de calidad.

### Valores

| Dictamen | Descripción | Tipo |
|----------|-------------|------|
| `RECIBIDO` | Lote recién ingresado, pendiente análisis | Inicial |
| `CUARENTENA` | En espera de análisis o decisión | Transitorio |
| `APROBADO` | Aprobado por control de calidad | Final positivo |
| `RECHAZADO` | Rechazado por control de calidad | Final negativo |
| `ANULADO` | Análisis anulado | Excepcional |
| `CANCELADO` | Operación cancelada | Excepcional |
| `ANALISIS_EXPIRADO` | Análisis venció sin resultado | Automático |
| `VENCIDO` | Lote vencido por fecha | Automático |
| `LIBERADO` | Liberado para venta | Final comercial |
| `DEVOLUCION_CLIENTES` | Devuelto por clientes | Comercial |
| `RETIRO_MERCADO` | Retirado del mercado (recall) | Comercial |

### Matriz de Transiciones Válidas

```
                           DESTINO
         ┌─────────────────────────────────────────────────────────────────────────┐
         │ RECIB │ CUARE │ APROB │ RECHA │ ANULA │ CANCE │ A_EXP │ VENCI │ LIBER │
   ┌─────┼───────┼───────┼───────┼───────┼───────┼───────┼───────┼───────┼───────┤
 O │RECIB│   -   │   ✓   │   -   │   -   │   -   │   -   │   -   │   -   │   -   │
 R │CUARE│   -   │   -   │   ✓   │   ✓   │   -   │   -   │   ✓†  │   -   │   -   │
 I │APROB│   -   │   ✓‡  │   -   │   -   │   -   │   -   │   -   │   ✓†  │   ✓   │
 G │RECHA│   -   │   -   │   -   │   -   │   -   │   -   │   -   │   -   │   -   │
 E │ANULA│   -   │   ✓*  │   ✓*  │   -   │   -   │   -   │   -   │   -   │   -   │
 N │CANCE│   -   │   -   │   -   │   -   │   -   │   -   │   -   │   -   │   -   │
   │A_EXP│   -   │   ✓*  │   -   │   -   │   -   │   -   │   -   │   -   │   -   │
   │VENCI│   -   │   -   │   -   │   -   │   -   │   -   │   -   │   -   │   -   │
   │LIBER│   -   │   -   │   -   │   -   │   -   │   -   │   -   │   -   │   -   │
   └─────┴───────┴───────┴───────┴───────┴───────┴───────┴───────┴───────┴───────┘

    ✓  = Transición válida (operación normal)
    ✓* = Transición válida solo por REVERSO (CU26)
    ✓† = Transición automática por sistema (CU9/CU10)
    ✓‡ = Transición válida por REANALISIS (CU8)
    -  = Transición no válida
```

### Transiciones por Caso de Uso

| CU | Operación | Dictamen Inicial | Dictamen Final |
|----|-----------|------------------|----------------|
| CU1 | Alta Ingreso Compra | - | RECIBIDO |
| CU2 | Dictamen Cuarentena | RECIBIDO | CUARENTENA |
| CU5/6 | Resultado Análisis | CUARENTENA | APROBADO/RECHAZADO |
| CU8 | Reanálisis Lote | APROBADO | CUARENTENA |
| CU9 | Análisis Expirado | CUARENTENA | ANALISIS_EXPIRADO |
| CU10 | Vencimiento Lote | APROBADO | VENCIDO |
| CU11 | Anulación Análisis | APROBADO/RECHAZADO | ANULADO |
| CU20 | Ingreso Producción | - | RECIBIDO |
| CU21 | Confirmar Lote Liberado | APROBADO | LIBERADO |
| CU23 | Devolución Venta | LIBERADO | DEVOLUCION_CLIENTES |
| CU24 | Retiro Mercado | LIBERADO | RETIRO_MERCADO |

---

## 3. Reglas de Negocio

### 3.1 Estados Terminales (sin salida)
- `EstadoEnum.DESCARTADO` - No permite ninguna operación posterior
- `DictamenEnum.RECHAZADO` - Lote no puede ser aprobado (requiere nueva muestra)
- `DictamenEnum.VENCIDO` - Lote no puede recuperarse
- `DictamenEnum.LIBERADO` - Solo admite operaciones comerciales

### 3.2 Restricciones de Operación

| Operación | Requiere Estado | Requiere Dictamen |
|-----------|-----------------|-------------------|
| Muestreo (CU3) | NUEVO | CUARENTENA |
| Consumo (CU7) | NUEVO/EN_USO | APROBADO |
| Venta (CU22) | NUEVO/EN_USO | LIBERADO |
| Devolución Venta (CU23) | VENDIDO | LIBERADO |
| Retiro Mercado (CU24) | VENDIDO/EN_USO | LIBERADO |

### 3.3 Transiciones Automáticas

El sistema ejecuta transiciones automáticas mediante jobs schedulados:

1. **CU9 - Análisis Expirado**: `CUARENTENA → ANALISIS_EXPIRADO`
   - Condición: Fecha análisis > fecha límite sin resultado
   - Frecuencia: Diaria

2. **CU10 - Vencimiento Lote**: `APROBADO → VENCIDO`
   - Condición: Fecha actual > fecha vencimiento lote
   - Frecuencia: Diaria

### 3.4 Reversibilidad (CU26)

El reverso de movimiento permite deshacer transiciones de estado:
- Autorizado solo para: creador del movimiento o usuarios de nivel jerárquico superior
- Restaura el estado previo exacto
- Genera audit trail completo
- No permitido para: CU9 (Análisis Expirado), CU10 (Vencimiento), CU24 (Retiro Mercado)

---

## 4. Diagrama de Flujo de Estados

### EstadoEnum (Cantidad)

```
                    ┌──────────────────┐
                    │     INGRESO      │
                    │   (CU1, CU20)    │
                    └────────┬─────────┘
                             │
                             v
                    ┌────────────────┐
                    │     NUEVO      │
                    └───────┬────────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
              v             v             v
        ┌──────────┐  ┌──────────┐  ┌──────────┐
        │ EN_USO   │  │ CONSUMIDO│  │ VENDIDO  │
        │(Muestreo)│  │(Produc.) │  │ (Venta)  │
        └─────┬────┘  └──────────┘  └────┬─────┘
              │                          │
              v                          v
        ┌──────────┐              ┌──────────┐
        │CONSUMIDO │              │ RECALL   │
        │/VENDIDO  │              │(Retiro)  │
        └──────────┘              └──────────┘
```

### DictamenEnum (Calidad)

```
                    ┌──────────────────┐
                    │     INGRESO      │
                    │   (CU1, CU20)    │
                    └────────┬─────────┘
                             │
                             v
                    ┌────────────────┐
                    │    RECIBIDO    │
                    └───────┬────────┘
                            │ CU2
                            v
                    ┌────────────────┐
           ┌───────>│   CUARENTENA   │<──────┐
           │        └───────┬────────┘       │
           │ CU8            │ CU5/6    CU9   │
           │        ┌───────┴───────┐        │
           │        v               v        │
      ┌────┴─────┐           ┌──────────┐    │
      │ APROBADO │           │RECHAZADO │    │
      └────┬─────┘           └──────────┘    │
           │                                 │
     ┌─────┴─────┐                           │
     │ CU21      │ CU10                      │
     v           v                           │
┌─────────┐  ┌─────────┐                     │
│LIBERADO │  │ VENCIDO │                     │
└────┬────┘  └─────────┘                     │
     │                                       │
  CU23/24                                    │
     v                                       │
┌────────────────┐                           │
│DEVOLUCION/     │                           │
│RETIRO_MERCADO  │                           │
└────────────────┘                           │
                                             │
              ┌──────────────────┐           │
              │ ANALISIS_EXPIRADO│───────────┘
              └──────────────────┘  (reverso)
```

---

## 5. Referencias ANMAT

- **Disposición 4159**: Requisitos para sistemas computarizados
- **Anexo 6**: Requisitos específicos de trazabilidad
- **Requisito 9**: Documentación de cambios de estado

Todas las transiciones de estado generan registros de auditoría según ANMAT Disp. 4159, Anexo 6, Req. 9.

---

*Documento generado para cumplimiento ANMAT - Diciembre 2025*
