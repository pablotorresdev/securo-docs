# GUIA DE CICLO DE VIDA - CONITRACK

**Version:** 1.1
**Fecha:** 2025-12-10
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico

---

## Tabla de Contenidos

1. [Introduccion](#introduccion)
2. [Permisos por Caso de Uso](#permisos-por-caso-de-uso)
3. [Ciclo de Compras/Produccion (CU1-CU11)](#ciclo-de-comprasproduccion)
   - [CU1 - Alta Ingreso Compra](#cu1---alta-ingreso-compra)
   - [CU2 - Dictamen Cuarentena](#cu2---dictamen-cuarentena)
   - [CU3 - Muestreo](#cu3---muestreo)
   - [CU4 - Devolucion Compra](#cu4---devolucion-compra)
   - [CU5 - Resultado Analisis: APROBADO](#cu5---resultado-analisis-aprobado)
   - [CU6 - Resultado Analisis: RECHAZADO](#cu6---resultado-analisis-rechazado)
   - [CU7 - Consumo Produccion](#cu7---consumo-produccion)
   - [CU8 - Reanalisis Lote](#cu8---reanalisis-lote)
   - [CU9 - Analisis Expirado (Automatico)](#cu9---analisis-expirado-automatico)
   - [CU10 - Vencimiento Lote (Automatico)](#cu10---vencimiento-lote-automatico)
   - [CU11 - Anulacion Analisis](#cu11---anulacion-analisis)
4. [Ciclo de Ventas (CU20-CU25, CU21)](#ciclo-de-ventas)
   - [CU20 - Ingreso Produccion](#cu20---ingreso-produccion)
   - [CU22 - Confirmar Lote Liberado](#cu22---confirmar-lote-liberado)
   - [CU23 - Venta Producto](#cu23---venta-producto)
   - [CU24 - Devolucion Venta](#cu24---devolucion-venta)
   - [CU25 - Retiro de Mercado (Recall)](#cu25---retiro-de-mercado-recall)
   - [CU21 - Trazado de Lote](#cu21---trazado-de-lote)
5. [Operaciones Transversales (CU30-CU31)](#operaciones-transversales)
   - [CU30 - Ajuste Stock](#cu30---ajuste-stock)
   - [CU31 - Reverso de Movimiento](#cu31---reverso-de-movimiento)
6. [Diagrama General del Ciclo](#diagrama-general-del-ciclo)

---

## Introduccion

Este documento describe el ciclo de vida completo de los lotes en el sistema CONITRACK, detallando para cada Caso de Uso (CU):

- **Contexto:** Diagrama ASCII mostrando donde se ubica en el flujo
- **Caso Tipico:** Descripcion de uso normal
- **Caminos Posibles:** Operaciones que pueden seguir despues

### Tipos de Movimiento

| Tipo | Descripcion | Efecto en Stock |
|------|-------------|-----------------|
| ALTA | Ingreso de mercaderia | Aumenta |
| BAJA | Egreso de mercaderia | Disminuye |
| MODIFICACION | Cambio de estado/dictamen | Sin cambio |

### Dictamenes Principales

| Dictamen | Descripcion | Utilizable |
|----------|-------------|------------|
| RECIBIDO | Ingresado, pendiente analisis | NO |
| CUARENTENA | En proceso de analisis | NO |
| APROBADO | Analisis aprobado (MP) | SI (produccion) |
| RECHAZADO | Analisis rechazado | NO |
| LIBERADO | Listo para venta (UV) | SI (venta) |
| ANALISIS_EXPIRADO | Requiere reanalisis | NO |
| VENCIDO | Producto vencido (terminal) | NO |
| DEVOLUCION_CLIENTES | Devuelto por cliente | NO |
| RETIRO_MERCADO | Recall (terminal) | NO |

---

## Permisos por Caso de Uso

La siguiente tabla resume los perfiles de usuario autorizados para ejecutar cada caso de uso:

### Jerarquia de Roles

| Nivel | Rol | Descripcion |
|-------|-----|-------------|
| 6 | ADMIN | Administrador del sistema |
| 5 | DT | Director Tecnico |
| 4 | GERENTE_GARANTIA_CALIDAD | Gerente de Garantia de Calidad |
| 3 | GERENTE_CONTROL_CALIDAD | Gerente de Control de Calidad |
| 3 | SUPERVISOR_PLANTA | Supervisor de Planta |
| 2 | ANALISTA_CONTROL_CALIDAD | Analista de Control de Calidad |
| 2 | ANALISTA_PLANTA | Analista de Planta |
| 1 | AUDITOR | Auditor (solo lectura) |

### Matriz de Permisos

| CU | Nombre | Perfiles Autorizados |
|----|--------|---------------------|
| CU1 | Ingreso Compra | ADMIN, ANALISTA_PLANTA |
| CU2 | Dictamen Cuarentena | ADMIN, ANALISTA_CONTROL_CALIDAD |
| CU3 | Muestreo | ADMIN, ANALISTA_CONTROL_CALIDAD |
| CU4 | Devolucion Compra | ADMIN, ANALISTA_PLANTA |
| CU5 | Resultado Aprobado | ADMIN, GERENTE_CONTROL_CALIDAD |
| CU6 | Resultado Rechazado | ADMIN, GERENTE_CONTROL_CALIDAD |
| CU7 | Consumo Produccion | ADMIN, SUPERVISOR_PLANTA |
| CU8 | Reanalisis Lote | ADMIN, GERENTE_CONTROL_CALIDAD |
| CU9 | Analisis Expirado | SISTEMA (automatico) |
| CU10 | Vencimiento Lote | SISTEMA (automatico) |
| CU11 | Anulacion Analisis | ADMIN, GERENTE_CONTROL_CALIDAD |
| CU20 | Ingreso Produccion | ADMIN, SUPERVISOR_PLANTA |
| CU22 | Confirmar Liberado | ADMIN, GERENTE_GARANTIA_CALIDAD, DT |
| CU23 | Venta Producto | ADMIN, ANALISTA_PLANTA |
| CU24 | Devolucion Venta | ADMIN, GERENTE_GARANTIA_CALIDAD |
| CU25 | Retiro Mercado | ADMIN, GERENTE_GARANTIA_CALIDAD |
| CU30 | Ajuste Stock | ADMIN, SUPERVISOR_PLANTA, ANALISTA_PLANTA |
| CU31 | Reverso Movimiento | Todos excepto AUDITOR (*) |
| CU21 | Trazado Lote | ADMIN, GERENTE_GARANTIA_CALIDAD, DT |

(*) CU31 tiene validacion adicional: el creador del movimiento siempre puede revertirlo; usuarios de jerarquia superior pueden revertir movimientos de usuarios inferiores.

---

## Ciclo de Compras/Produccion

### CU1 - Alta Ingreso Compra

**Perfiles Autorizados:** `ADMIN`, `ANALISTA_PLANTA`

```
[Proveedor Externo]
        |
        v
   [CU1 Ingreso]
        |
        v
  [Lote RECIBIDO]
```

**Caso Tipico:** Llega mercaderia de un proveedor externo (API, excipiente, material de empaque). Se registra el ingreso con remito, lote proveedor, cantidad y fechas.

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Enviar a analisis | CU2 Cuarentena |
| Devolver al proveedor | CU4 Devolucion |
| Ajuste de cantidad | CU30 Ajuste |
| Error en el ingreso | CU31 Reverso |

---

### CU2 - Dictamen Cuarentena

**Perfiles Autorizados:** `ADMIN`, `ANALISTA_CONTROL_CALIDAD`

```
[RECIBIDO / APROBADO / ANALISIS_EXPIRADO / LIBERADO / DEVOLUCION_CLIENTES]
        |
        v
   [CU2 Cuarentena]
        |
        v
  [Lote CUARENTENA]
   (Nro Analisis asignado)
```

**Caso Tipico:** Se asigna numero de analisis al lote y pasa a estado CUARENTENA para ser analizado por Control de Calidad.

**Entradas Posibles:**
- RECIBIDO: Lote recien ingresado
- APROBADO: Lote que requiere reanalisis
- ANALISIS_EXPIRADO: Lote con analisis vencido
- LIBERADO: Producto terminado que requiere reanalisis
- DEVOLUCION_CLIENTES: Lote devuelto que debe analizarse

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Tomar muestra | CU3 Muestreo |
| Error en asignacion | CU31 Reverso |
| Devolver al proveedor | CU4 Devolucion |

---

### CU3 - Muestreo

**Perfiles Autorizados:** `ADMIN`, `ANALISTA_CONTROL_CALIDAD`

```
[CUARENTENA]
        |
        v
   [CU3 Muestreo]
        |
        v
[Lote con muestra tomada]
  (cantidad reducida)
```

**Caso Tipico:** Se extrae una muestra del lote para analisis de laboratorio. La cantidad del lote se reduce por la muestra tomada.

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Resultado aprobado | CU5 Aprobado |
| Resultado rechazado | CU6 Rechazado |
| Error en muestreo | CU31 Reverso |
| Devolver al proveedor | CU4 Devolucion |

---

### CU4 - Devolucion Compra

**Perfiles Autorizados:** `ADMIN`, `ANALISTA_PLANTA`

```
[RECIBIDO / RECHAZADO]
        |
        v
   [CU4 Devolucion]
        |
        v
[Lote con stock reducido]
  o [Lote DEVUELTO si total]
```

**Caso Tipico:** Se devuelve mercaderia al proveedor por rechazo de calidad, exceso, o acuerdo comercial.

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Error en devolucion | CU31 Reverso |

> **Nota:** Si se devuelve la totalidad, el lote queda en estado DEVUELTO (terminal).

---

### CU5 - Resultado Analisis: APROBADO

**Perfiles Autorizados:** `ADMIN`, `GERENTE_CONTROL_CALIDAD`

```
[CUARENTENA]
        |
        v
   [CU5 Aprobado]
        |
        v
  [Lote APROBADO]
   (fechas actualizadas)
```

**Caso Tipico:** Control de Calidad dictamina que el lote cumple especificaciones. Se asignan fechas de reanalisis y vencimiento.

**Caminos Posibles (segun tipo producto):**

**Para Materias Primas (API/EXC):**
| Situacion | CU Aplicable |
|-----------|--------------|
| Devolver al proveedor | CU4 Devolucion |
| Usar en produccion | CU7 Consumo |
| Requiere reanalisis futuro | CU8 Reanalisis |
| Ajuste de cantidad | CU30 Ajuste |
| Error en dictamen | CU31 Reverso |

**Para Unidades de Venta (UV):**
| Situacion | CU Aplicable |
|-----------|--------------|
| Confirmar para venta | CU22 Liberar |
| Trazado de unidades | CU21 Trazado |
| Error en dictamen | CU31 Reverso |

---

### CU6 - Resultado Analisis: RECHAZADO

**Perfiles Autorizados:** `ADMIN`, `GERENTE_CONTROL_CALIDAD`

```
[CUARENTENA]
        |
        v
   [CU6 Rechazado]
        |
        v
  [Lote RECHAZADO]
```

**Caso Tipico:** Control de Calidad dictamina que el lote NO cumple especificaciones.

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Devolver al proveedor | CU4 Devolucion |
| Ajuste/descarte | CU30 Ajuste |
| Error en dictamen | CU31 Reverso |

---

### CU7 - Consumo Produccion

**Perfiles Autorizados:** `ADMIN`, `SUPERVISOR_PLANTA`

```
[APROBADO (MP)]
        |
        v
   [CU7 Consumo]
        |
        v
[Lote con stock reducido]
  o [Lote CONSUMIDO si total]
```

**Caso Tipico:** Se consume materia prima aprobada para fabricar producto terminado. El stock del lote origen se reduce.

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Error en consumo | CU31 Reverso |
| Devolver al proveedor | CU4 Devolucion | 

> **Nota:** CU7 es una operacion de BAJA. Si se consume la totalidad, el lote queda CONSUMIDO (terminal).

---

### CU8 - Reanalisis Lote

**Perfiles Autorizados:** `ADMIN`, `GERENTE_CONTROL_CALIDAD`

```
[APROBADO]
        |
        v
   [CU8 Reanalisis]
        |
        v
  [Lote CUARENTENA]
   (Nuevo Nro Analisis)
```

**Caso Tipico:** Un lote aprobado requiere nuevo analisis antes de que expire su fecha de reanalisis (preventivo).

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Tomar muestra | CU3 Muestreo |
| Error en reanalisis | CU31 Reverso |

---

### CU9 - Analisis Expirado (Automatico)

**Ejecutado por:** `SISTEMA` (usuario automatico: system_auto)

```
[APROBADO / LIBERADO]
  (fechaReanalisis <= hoy)
        |
        v
   [CU9 Automatico]
   (5:00 AM diario)
        |
        v
  [Lote ANALISIS_EXPIRADO]
```

**Caso Tipico:** Proceso batch automatico que detecta lotes cuya fecha de reanalisis ha vencido y los bloquea hasta nuevo analisis.

**Caracteristicas:**
- Ejecucion: Diaria a las 5:00 AM
- Usuario: system_auto (ADMIN)
- Recuperable: SI (via CU2 + CU3 + CU5)

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Recuperar lote | CU2 Cuarentena (nuevo analisis) |
| Ajuste/descarte | CU30 Ajuste |

> **Nota:** No existe reverso para CU9. La unica forma de recuperar es realizar nuevo analisis.

---

### CU10 - Vencimiento Lote (Automatico)

**Ejecutado por:** `SISTEMA` (usuario automatico: system_auto)

```
[CUALQUIER DICTAMEN]
  (fechaVencimiento <= hoy)
        |
        v
   [CU10 Automatico]
   (5:00 AM, despues de CU9)
        |
        v
  [Lote VENCIDO]
   (TERMINAL)
```

**Caso Tipico:** Proceso batch automatico que detecta lotes cuya fecha de vencimiento ha pasado. El producto queda permanentemente inutilizable.

**Caracteristicas:**
- Ejecucion: Diaria a las 5:00 AM (despues de CU9)
- Usuario: system_auto (ADMIN)
- Recuperable: NO (estado terminal)
- Cancela analisis en curso automaticamente

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Destruccion documentada | CU30 Ajuste Stock |

> **Nota:** VENCIDO es un estado terminal. No existe reverso ni recuperacion. Solo se permite CU30 para registrar la destruccion.

---

### CU11 - Anulacion Analisis

**Perfiles Autorizados:** `ADMIN`, `GERENTE_CONTROL_CALIDAD`

```
[CUARENTENA / APROBADO / RECHAZADO]
        |
        v
   [CU11 Anulacion]
        |
        v
[Lote en estado anterior]
  (RECIBIDO, APROBADO, ANALISIS_EXPIRADO, DEVOLUCION_CLIENTES)
```

**Caso Tipico:** Se detecta un error en el proceso de analisis (nro incorrecto, muestra equivocada) y se anula, restaurando el estado previo del lote.

**Estados Restaurables:**
| Estado Antes de Cuarentena | Se Restaura A |
|---------------------------|---------------|
| RECIBIDO | RECIBIDO |
| APROBADO | APROBADO |
| ANALISIS_EXPIRADO | ANALISIS_EXPIRADO |
| DEVOLUCION_CLIENTES | DEVOLUCION_CLIENTES |

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Reiniciar analisis correcto | CU2 Cuarentena |
| Devolver si corresponde | CU4 Devolucion |

---

## Ciclo de Ventas

### CU20 - Ingreso Produccion

**Perfiles Autorizados:** `ADMIN`, `SUPERVISOR_PLANTA`

```
[Produccion Interna]
   (con consumo CU7)
        |
        v
   [CU20 Ingreso]
        |
        v
  [Lote UV RECIBIDO]
```

**Caso Tipico:** Se registra el ingreso de producto terminado (UV) fabricado internamente.

**Formato Codigo de Lote:** `AAMM####(L)` donde:
- AA: Año (2 digitos)
- MM: Mes (01-12)
- ####: Secuencial (4 digitos)
- L: Letra identificadora (A-Z) - **OPCIONAL**

Ejemplos validos: `25010001`, `25010001A`, `25120015B`

**Relacion con Consumo:**
- El ingreso de produccion esta vinculado al consumo de materias primas (CU7)
- Se registra la genealogia (lote origen)

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Enviar a analisis | CU2 Cuarentena |
| Ajuste de cantidad | CU30 Ajuste |
| Error en el ingreso | CU31 Reverso |

> **Nota:** El trazado NO se realiza en CU20. Una vez que el lote este LIBERADO (post CU22), se puede ejecutar CU21 para asignar trazas.

---

### CU22 - Confirmar Lote Liberado

**Perfiles Autorizados:** `ADMIN`, `GERENTE_GARANTIA_CALIDAD`, `DT`

```
[APROBADO (UV)]
        |
        v
   [CU22 Liberar]
        |
        v
  [Lote LIBERADO]
```

**Caso Tipico:** Director Tecnico confirma que el lote de producto terminado esta listo para comercializacion.

**Pre-condiciones:**
- Lote debe ser tipo UV (Unidad de Venta)
- Dictamen debe ser APROBADO

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Trazado (si aplica) | CU21 Trazado |
| Venta del producto | CU23 Venta |
| Reanalisis preventivo | CU8 Reanalisis |
| Error en liberacion | CU31 Reverso |

> **Nota:** El trazado es condicional segun destino. Exportaciones pueden no requerir trazabilidad ANMAT.

---

### CU23 - Venta Producto

**Perfiles Autorizados:** `ADMIN`, `ANALISTA_PLANTA`

```
[LIBERADO (UV)]
        |
        v
   [CU23 Venta]
        |
        v
[Lote con stock reducido]
  o [Lote VENDIDO si total]
   (trazas asignadas a cliente)
```

**Caso Tipico:** Se registra la venta de unidades de un lote liberado a un cliente.

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Cliente devuelve | CU24 Devolucion Venta |
| Problema de calidad | CU25 Recall |
| Error en venta | CU31 Reverso |

---

### CU24 - Devolucion Venta

**Perfiles Autorizados:** `ADMIN`, `GERENTE_GARANTIA_CALIDAD`

```
[VENDIDO/PARCIAL]
        |
        v
   [CU24 Devolucion]
        |
        v
[Lote derivado _D_#]
  DEVOLUCION_CLIENTES
```

**Caso Tipico:** Un cliente devuelve producto. Se crea un lote derivado con sufijo `_D_N` para gestionar la devolucion.

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Analizar devolucion | CU2 Cuarentena |
| Ajuste/descarte | CU30 Ajuste |
| Error en devolucion | CU31 Reverso |

> **Nota:** El lote devuelto (_D_#) puede ser enviado a analisis para determinar si es reutilizable.

---

### CU25 - Retiro de Mercado (Recall)

**Perfiles Autorizados:** `ADMIN`, `GERENTE_GARANTIA_CALIDAD`

```
[VENDIDO/RECALL_CLIENTE]
        |
        v
   [CU25 Retiro]
        |
        v
[Lote derivado _R_#]
  RETIRO_MERCADO
```

**Caso Tipico:** Se detecta un problema de calidad post-venta y se retira producto del mercado.

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Error en el recall | CU31 Reverso |
| Ajuste de cantidad | CU30 Ajuste Stock |
| Destruccion del lote | CU30 Ajuste Stock (descarte) |

> **Nota:** Los lotes con dictamen RETIRO_MERCADO **no pueden** enviarse a analisis (CU2). El recall es un estado terminal que solo permite ajustes, reverso o destruccion documentada mediante CU30.

---

### CU21 - Trazado de Lote

**Perfiles Autorizados:** `ADMIN`, `GERENTE_GARANTIA_CALIDAD`, `DT`

```
[LOTE LIBERADO UV]
        |
        v
   [CU21 Trazado]
        |
        v
[Trazas asignadas]
  (mismo dictamen)
```

**Caso Tipico:** Se asignan numeros de traza individuales a las unidades de venta (UV) de un lote liberado, habilitandolas para venta con trazabilidad completa.

**Pre-condiciones:**
- Lote debe ser de tipo UV (Unidad de Venta)
- Dictamen debe ser LIBERADO
- Puede ejecutarse durante CU20 (traza inicial opcional) o posteriormente

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Venta del producto trazado | CU23 Venta |
| Error en trazado | CU31 Reverso |
| Devolucion post-venta | CU24 Devolucion Venta |

> **Nota:** El trazado es condicional segun el destino del producto. Exportaciones pueden no requerir trazabilidad ANMAT. El sistema asigna rangos de trazas (ej: 50001-50100 para 100 unidades).

---

## Operaciones Transversales

### CU30 - Ajuste Stock

**Perfiles Autorizados:** `ADMIN`, `SUPERVISOR_PLANTA`, `ANALISTA_PLANTA`

```
[CUALQUIER LOTE CON STOCK]
        |
        v
   [CU30 Ajuste]
        |
        v
[Cantidad modificada]
  (mismo dictamen)
```

**Caso Tipico:** Correccion de stock por inventario fisico, merma, rotura, destruccion de producto vencido/retirado, o diferencias detectadas.

**Entradas Posibles:**
- Lotes en cualquier estado que tengan stock > 0
- Caso especial: lotes VENCIDO y RETIRO_MERCADO solo permiten CU30 para registrar destruccion

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Error en el ajuste | CU31 Reverso |
| Continua ciclo normal | Segun dictamen actual del lote |

> **Nota:** CU30 es una operacion de BAJA que reduce cantidad sin cambiar dictamen. Es la unica operacion permitida para lotes VENCIDO y RETIRO_MERCADO (destruccion documentada).

---

### CU31 - Reverso de Movimiento

**Perfiles Autorizados:** `ADMIN`, `DT`, `GERENTE_GARANTIA_CALIDAD`, `GERENTE_CONTROL_CALIDAD`, `SUPERVISOR_PLANTA`, `ANALISTA_CONTROL_CALIDAD`, `ANALISTA_PLANTA`

> **Nota:** AUDITOR no tiene acceso a esta operacion.

```
[MOVIMIENTO EXISTENTE]
        |
        v
   [CU31 Reverso]
        |
        v
[Estado anterior restaurado]
```

**Caso Tipico:** Se detecta un error en una operacion previa y se necesita revertir al estado anterior.

**Tipos de Reverso:**
| Tipo Movimiento Original | Efecto del Reverso |
|--------------------------|-------------------|
| ALTA (CU1, CU20, CU24, CU25) | Elimina el lote/devuelve stock |
| BAJA (CU4, CU7, CU23, CU30) | Restaura el stock consumido |
| MODIFICACION (CU2, CU5, CU6, CU8, CU11, CU22) | Restaura dictamen anterior |

**Autorizacion Adicional (ReversoAuthorizationService):**
- El creador del movimiento siempre puede revertirlo
- Usuarios de jerarquia superior pueden revertir movimientos de usuarios inferiores
- AUDITOR nunca puede ejecutar reversos

**Restricciones:**
- No existe reverso para CU9 (Analisis Expirado) ni CU10 (Vencimiento) - son procesos automaticos irreversibles
- No se puede revertir un movimiento ya revertido

**Caminos Posibles:**
| Situacion | CU Aplicable |
|-----------|--------------|
| Despues del reverso | Retoma el flujo segun el estado restaurado |

> **Nota:** CU31 es una operacion de contingencia. Cada reverso genera su propio movimiento de auditoria referenciando el movimiento original.

---

## Diagrama General del Ciclo

```
                              CICLO DE VIDA COMPLETO
================================================================================

COMPRAS/MATERIAS PRIMAS                      PRODUCCION/VENTAS
-----------------------                      -----------------

[Proveedor Externo]                          [Produccion Interna]
        |                                            |
        v                                            v
   [CU1 Ingreso]                                [CU20 Ingreso]
        |                                            |
        v                                            v
   [RECIBIDO] ----+                             [RECIBIDO]
        |        |                                   |
        |   [CU4 Devolucion]                        |
        v        |                                   v
   [CU2 Cuarentena] <----+              [CU2 Cuarentena] <----+
        |               |                       |            |
        v               |                       v            |
   [CUARENTENA]         |                  [CUARENTENA]      |
        |               |                       |            |
        v               |                       v            |
   [CU3 Muestreo]       |                  [CU3 Muestreo]    |
        |               |                       |            |
   +----+----+          |                  +----+----+       |
   |         |          |                  |         |       |
   v         v          |                  v         v       |
[CU5]      [CU6]        |               [CU5]      [CU6]     |
APROBADO   RECHAZADO    |               APROBADO   RECHAZADO |
   |         |          |                  |                 |
   |    [CU4 Dev]       |                  v                 |
   |         |          |             [CU22 Liberar]         |
   v         v          |                  |                 |
[CU7 Consumo] --------->|                  v                 |
   |                    |             [LIBERADO]             |
   |                    |                  |                 |
   |                    |            [CU21 Trazado]          |
   |                    |                  |                 |
   |                    |                  v                 |
   |                    |             [CU23 Venta]           |
   |                    |                  |                 |
   +--------------------+             +----+----+            |
                                      |         |            |
                                      v         v            |
                                 [CU24 Dev] [CU25 Recall]    |
                                      |         |            |
                                      v         v            |
                                 [_D_# lote] [_R_# lote]     |
                                      |         |            |
                                      +---------+------------+
                                            |
                                       (si analizable)


PROCESOS AUTOMATICOS (5:00 AM)
------------------------------
[CU9] Analisis Expirado: fechaReanalisis <= hoy --> ANALISIS_EXPIRADO (recuperable)
[CU10] Vencimiento:      fechaVencimiento <= hoy --> VENCIDO (terminal)


OPERACIONES TRANSVERSALES
-------------------------
[CU30] Ajuste Stock: Cualquier lote con stock (unica opcion para VENCIDO/RETIRO_MERCADO)
[CU31] Reverso: Revertir cualquier movimiento (excepto CU9/CU10)

================================================================================
```

---

## Estados Terminales

Los siguientes estados son **terminales** - el lote no puede recuperarse para uso:

| Estado | CU que lo genera | Unica operacion permitida |
|--------|------------------|---------------------------|
| VENCIDO | CU10 (automatico) | CU30 (destruccion) |
| RETIRO_MERCADO | CU25 | CU30 (destruccion) |
| DEVUELTO | CU4 (total) | Ninguna |
| CONSUMIDO | CU7 (total) | Ninguna |
| VENDIDO | CU23 (total) | CU24, CU25 |

---

## Resumen de CUs por Tipo de Movimiento

### ALTA (Ingreso de stock)
| CU | Nombre | Origen |
|----|--------|--------|
| CU1 | Ingreso Compra | Proveedor externo |
| CU20 | Ingreso Produccion | Fabricacion interna |
| CU24 | Devolucion Venta | Cliente |
| CU25 | Retiro Mercado | Mercado (recall) |

### BAJA (Egreso de stock)
| CU | Nombre | Destino |
|----|--------|---------|
| CU4 | Devolucion Compra | Proveedor |
| CU7 | Consumo Produccion | Produccion interna |
| CU23 | Venta Producto | Cliente |
| CU30 | Ajuste Stock | Ajuste/Destruccion |

### MODIFICACION (Cambio de estado)
| CU | Nombre | Cambio |
|----|--------|--------|
| CU2 | Dictamen Cuarentena | -> CUARENTENA |
| CU3 | Muestreo | Reduce cantidad |
| CU5 | Resultado Aprobado | -> APROBADO |
| CU6 | Resultado Rechazado | -> RECHAZADO |
| CU8 | Reanalisis | -> CUARENTENA |
| CU9 | Analisis Expirado | -> ANALISIS_EXPIRADO |
| CU10 | Vencimiento | -> VENCIDO |
| CU11 | Anulacion Analisis | -> estado anterior |
| CU22 | Confirmar Liberado | -> LIBERADO |
| CU31 | Reverso | -> estado anterior |
| CU21 | Trazado Lote | Asigna trazas |

---


## Estados de Cantidad

Estos estan relacionados con la existencia fisica de los productos, aplican a lote/bulto/traza según su cantidad disponible.

### Valores

| Estado | Descripción | Condición |
|--------|-------------|-----------|
| `NUEVO` | Lote recién ingresado | Cantidad inicial = cantidad actual |
| `DISPONIBLE` | Unidad trazada disponible | Aplica a cada traza |
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
| CU3 | Muestreo | NUEVO | EN_USO |
| CU4 | Devolución Compra | NUEVO/EN_USO | DEVUELTO |
| CU7 | Consumo Producción | NUEVO/EN_USO | EN_USO/CONSUMIDO |
| CU20 | Ingreso Producción | - | NUEVO |
| CU23 | Venta Producto | NUEVO/EN_USO | EN_USO/VENDIDO |
| CU24 | Devolución Venta | VENDIDO | EN_USO/NUEVO |
| CU25 | Retiro Mercado | VENDIDO/EN_USO | RECALL |
| CU30 | Ajuste Stock | * | * |
| CU31 | Reverso Movimiento | * | (restaura estado anterior) |

---

## Estados de Calidad

Define el estado de aprobación del lote según análisis de calidad.

### Valores

| Dictamen | Descripción | Tipo |
|----------|-------------|------|
| `RECIBIDO` | Lote recién ingresado, pendiente análisis | Inicial |
| `CUARENTENA` | En espera de resultado de análisis | Transitorio |
| `APROBADO` | Aprobado por control de calidad | Final positivo |
| `RECHAZADO` | Rechazado por control de calidad | Final negativo |
| `ANULADO` | Análisis anulado | Excepcional |
| `CANCELADO` | Operación cancelada | Excepcional |
| `ANALISIS_EXPIRADO` | La fecha de re-análisis venció | Automático |
| `VENCIDO` | Lote vencido por fecha | Automático |
| `LIBERADO` | Liberado para venta | Final comercial |
| `DEVOLUCION_CLIENTES` | Devuelto por clientes | Comercial |
| `RETIRO_MERCADO` | Retirado del mercado (recall) | Comercial |

### Transiciones por Caso de Uso

| CU | Operación | Dictamen Inicial | Dictamen Final |
|----|-----------|------------------|----------------|
| CU1 | Alta Ingreso Compra | - | RECIBIDO |
| CU2 | Dictamen Cuarentena | RECIBIDO | CUARENTENA |
| CU5/6 | Resultado Análisis | CUARENTENA | APROBADO/RECHAZADO |
| CU8 | Reanálisis Lote | APROBADO | APROBADO(Analisis asignado) |
| CU9 | Análisis Expirado | RECIBIDO/CUARENTENA/APROBADO | ANALISIS_EXPIRADO |
| CU10 | Vencimiento Lote | APROBADO/LIBERADO | VENCIDO |
| CU20 | Ingreso Producción | - | RECIBIDO |
| CU22 | Confirmar Lote Liberado | APROBADO | LIBERADO |
| CU24 | Devolución Venta | LIBERADO | DEVOLUCION_CLIENTES |
| CU25 | Retiro Mercado | LIBERADO | RETIRO_MERCADO |

---

**Fin del Documento - GUIA-CICLO-VIDA v1.0**
