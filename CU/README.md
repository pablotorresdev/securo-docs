# Casos de Uso - CONITRACK

**Ultima actualizacion:** 2025-12-07
**Total CUs:** 22 documentados

---

## Indice por Categoria

### PRODUCCION (`/PRODUCCION/`)

| CU | Nombre | Tipo | Archivo |
|----|--------|------|---------|
| CU1 | Alta Ingreso Compra | ALTA | `CU1_ALTA_INGRESO_COMPRA_ANALISIS_COMPLETO.md` |
| CU2 | Dictamen Cuarentena | MODIF | `CU2_DICTAMEN_CUARENTENA.md` |
| CU3 | Muestreo | BAJA | `CU3_MUESTREO.md` |
| CU4 | Devolucion Compra | BAJA | `CU4_DEVOLUCION_COMPRA.md` |
| CU5/CU6 | Resultado Analisis | MODIF | `CU5_CU6_RESULTADO_ANALISIS.md` |
| CU7 | Consumo Produccion | BAJA | `CU7_CONSUMO_PRODUCCION.md` |
| CU8 | Reanalisis Lote | MODIF | `CU8_REANALISIS_LOTE.md` |
| CU9 | Analisis Expirado | AUTO | `CU9_ANALISIS_EXPIRADO.md` |
| CU10 | Vencimiento Lote | AUTO | `CU10_VENCIMIENTO_LOTE.md` |
| CU11 | Anulacion Analisis | MODIF | `CU11_ANULACION_ANALISIS.md` |

### VENTAS (`/VENTAS/`)

| CU | Nombre | Tipo | Archivo |
|----|--------|------|---------|
| CU20 | Ingreso Produccion | ALTA | `CU20_INGRESO_PRODUCCION.md` |
| CU21 | Confirmar Lote Liberado | MODIF | `CU21_CONFIRMAR_LOTE_LIBERADO.md` |
| CU22 | Venta Producto | BAJA | `CU22_VENTA_PRODUCTO.md` |
| CU23 | Devolucion Venta | ALTA | `CU23_DEVOLUCION_VENTA.md` |
| CU24 | Retiro Mercado | ALTA+MODIF | `CU24_RETIRO_MERCADO.md` |
| CU27 | Trazado Lote | MODIF | `CU27_TRAZADO_LOTE.md` |

### CONTINGENCIAS (raiz `/`)

| CU | Nombre | Tipo | Archivo |
|----|--------|------|---------|
| CU25 | Ajuste Stock | BAJA | `CU25_AJUSTE_STOCK.md` |
| CU26 | Reverso Movimiento | MODIF | `CU26_REVERSO_MOVIMIENTO.md` |

### ABM MAESTROS (raiz `/`)

| CU | Nombre | Tipo | Archivo |
|----|--------|------|---------|
| CU40 | ABM Proveedores | ABM | `CU40_ABM_PROVEEDORES.md` |
| CU41 | ABM Productos | ABM | `CU41_ABM_PRODUCTOS.md` |
| CU42 | Configuracion Sistema | ADMIN | `CU42_ABM_CONFIGURACION.md` |
| CU43 | ABM Usuarios | ABM | `CU43_ABM_USUARIOS.md` |

---

## Clasificacion por Tipo de Operacion

### ALTA (Ingreso de Stock) - 4 CUs
Operaciones que incrementan stock o crean nuevos lotes.
- CU1: Ingreso Compra
- CU20: Ingreso Produccion
- CU23: Devolucion Venta (crea nuevo lote)
- CU24: Retiro Mercado (crea nuevo lote + modifica original)

### BAJA (Egreso de Stock) - 5 CUs
Operaciones que reducen stock existente.
- CU3: Muestreo
- CU4: Devolucion Compra
- CU7: Consumo Produccion
- CU22: Venta Producto
- CU25: Ajuste Stock

### MODIFICACION (Cambio de Estado/Dictamen) - 7 CUs
Operaciones que cambian estado o dictamen sin afectar cantidades.
- CU2: Dictamen Cuarentena
- CU5/CU6: Resultado Analisis
- CU8: Reanalisis Lote
- CU11: Anulacion Analisis
- CU21: Confirmar Lote Liberado
- CU26: Reverso Movimiento
- CU27: Trazado Lote

### AUTOMATICOS (Scheduler) - 2 CUs
Procesos batch ejecutados por el sistema.
- CU9: Analisis Expirado (5:00 AM diario)
- CU10: Vencimiento Lote (5:00 AM diario, despues de CU9)

### ABM MAESTROS - 4 CUs
Alta/Baja/Modificacion de datos maestros.
- CU40: Proveedores
- CU41: Productos
- CU42: Configuracion
- CU43: Usuarios
