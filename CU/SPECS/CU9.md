# CU9 - EXPIRACION DE ANALISIS (PROCESO AUTOMATICO)

**Caso de Uso:** Expiracion Automatica de Analisis
**URL Base:** N/A (Proceso batch programado)
**Consulta Asociada:** `/lotes/list-fechas-lotes`
**Tipo:** MODIFICACION (Automatico)
**Categoria:** Calidad / Sistema (color purpura)
**Identificador Interno:** `CU9_EXPIRACION_ANALISIS`

---

## 1. DESCRIPCION

Proceso batch automatico que se ejecuta diariamente a las **5:00 AM** para detectar y procesar lotes cuya fecha de reanalisis ha expirado (fechaReanalisis <= fecha actual). Cuando un lote cumple esta condicion, el sistema cambia automaticamente su dictamen a `ANALISIS_EXPIRADO` y genera un movimiento de MODIFICACION con motivo `EXPIRACION_ANALISIS`.

**Caracteristicas principales:**

- **Ejecucion automatica:** Spring `@Scheduled(cron = "0 0 5 * * *")` - todos los dias a las 5:00 AM
- **Usuario del sistema:** Las operaciones se ejecutan como `system_auto` con rol ADMIN
- **Sin interaccion humana:** No requiere formularios ni intervencion del usuario
- **Manejo de errores resiliente:** Si falla el procesamiento de un lote, continua con los siguientes
- **Transaccional:** Cada lote se procesa en transaccion independiente

**Relacion con CU10:** Este servicio tambien procesa CU10 (Vencimiento de Producto). Ambos CUs se ejecutan secuencialmente en el mismo proceso batch.

**Consulta de monitoreo:** La pantalla `/lotes/list-fechas-lotes` permite a los usuarios visualizar las fechas de reanalisis y vencimiento de los lotes, identificando aquellos proximos a expirar.

---

## 2. ROLES AUTORIZADOS

### 2.1 Proceso Automatico

| Rol | Nivel | Acceso |
|-----|-------|--------|
| system_auto (ADMIN) | 6 | Proceso batch |

**Nota:** El proceso batch se ejecuta con un usuario especial `system_auto` que se crea automaticamente si no existe.

### 2.2 Consulta de Fechas (/lotes/list-fechas-lotes)

| Rol | Nivel | Acceso |
|-----|-------|--------|
| ADMIN | 6 | Permitido |
| DT | 5 | Permitido |
| GERENTE_GARANTIA_CALIDAD | 4 | Permitido |
| GERENTE_CONTROL_CALIDAD | 3 | Permitido |
| SUPERVISOR_PLANTA | 3 | Permitido |
| ANALISTA_CONTROL_CALIDAD | 2 | Permitido |
| ANALISTA_PLANTA | 2 | Permitido |
| AUDITOR | 1 | No permitido |

**Anotacion Enum:** `CU9_CU10_FECHAS_AUTOMATICO` en `PermisosCasoUsoEnum.java`

---

## 3. FLUJO DE OPERACION

### 3.1 Proceso Automatico (Batch)

```
+---------------------------------------------------------------------+
|  SCHEDULER (5:00 AM diario)                                          |
|  @Scheduled(cron = "0 0 5 * * *")                                   |
+---------------------------------------------------------------------+
                              |
                              v
+---------------------------------------------------------------------+
|  1. BUSCAR LOTES CON ANALISIS EXPIRADO                              |
|     findAllLotesAnalisisExpirado()                                   |
|  +---------------------------------------------------------------+  |
|  |  Query: Lotes con stock donde                                 |  |
|  |         fechaReanalisisVigente <= fecha actual                |  |
|  +---------------------------------------------------------------+  |
+---------------------------------------------------------------------+
                              |
                              v
+---------------------------------------------------------------------+
|  2. PROCESAR CADA LOTE (CU9)                                        |
|     procesarLotesAnalisisExpirado()                                  |
|  +---------------------------------------------------------------+  |
|  |  Para cada lote:                                              |  |
|  |    - Crear MovimientoDTO con motivo "(CU9) ANALISIS EXPIRADO" |  |
|  |    - Crear Movimiento MODIFICACION/EXPIRACION_ANALISIS        |  |
|  |    - Cambiar dictamen lote a ANALISIS_EXPIRADO                |  |
|  |    - Guardar lote                                             |  |
|  |  Si error: log y continuar con siguiente lote                 |  |
|  +---------------------------------------------------------------+  |
+---------------------------------------------------------------------+
                              |
                              v
+---------------------------------------------------------------------+
|  3. BUSCAR LOTES VENCIDOS (CU10 - ver documentacion separada)       |
|     findAllLotesVencidos()                                           |
+---------------------------------------------------------------------+
                              |
                              v
+---------------------------------------------------------------------+
|  4. PROCESAR LOTES VENCIDOS (CU10)                                  |
|     procesarLotesVencidos()                                          |
+---------------------------------------------------------------------+
```

### 3.2 Consulta de Monitoreo

```
+---------------------------------------------------------------------+
|  CONSULTA FECHAS                                                     |
|  GET /lotes/list-fechas-lotes                                       |
|  Template: lotes/list-fechas-lotes.html                             |
|  +---------------------------------------------------------------+  |
|  |  Tabla DataTables con:                                        |  |
|  |  - Producto (nombre y codigo)                                 |  |
|  |  - Codigo Lote (link a detalle)                               |  |
|  |  - Cantidad Actual y Unidad                                   |  |
|  |  - Dias hasta Reanalisis (coloreado segun urgencia)          |  |
|  |  - Dias hasta Vencimiento (coloreado segun urgencia)         |  |
|  |  - Fechas Vigentes (reanalisis/vencimiento)                  |  |
|  |  - Fechas Proveedor (originales)                             |  |
|  |  - Acciones (Ver Analisis, Ver Lote, Ver Movimientos)        |  |
|  +---------------------------------------------------------------+  |
|  [Exportar Excel]                                                    |
+---------------------------------------------------------------------+
```

---

## 4. ARCHIVOS INVOLUCRADOS

### Backend (Proceso Batch)

| Archivo | Descripcion | Lineas |
|---------|-------------|--------|
| `service/cu/FechaValidatorService.java` | Servicio batch CU9/CU10 | 239 |
| `service/cu/AbstractCuService.java` | Clase base con repositorios | - |

### Test

| Archivo | Descripcion | Lineas |
|---------|-------------|--------|
| `service/cu/FechaValidatorServiceTest.java` | Tests unitarios completos | 1026 |

### Frontend (Consulta de Monitoreo)

| Archivo | Descripcion | Lineas |
|---------|-------------|--------|
| `templates/lotes/list-fechas-lotes.html` | Consulta de fechas | 360 |
| `controller/LotesController.java` | Controller consulta (parcial) | 112 |

### Enums Relacionados

| Archivo | Uso en CU9 |
|---------|------------|
| `enums/UseCaseTag.java` | Tag `CU9("[CU9]")` |
| `enums/MotivoEnum.java` | `EXPIRACION_ANALISIS("Expiracion analisis")` |
| `enums/DictamenEnum.java` | `ANALISIS_EXPIRADO("Analisis expirado")` |
| `enums/PermisosCasoUsoEnum.java` | `CU9_CU10_FECHAS_AUTOMATICO` |

### Utilidades

| Archivo | Uso en CU9 |
|---------|------------|
| `utils/MovimientoModificacionUtils.java` | Factory para Movimiento MODIFICACION |
| `utils/MovimientoCommonUtils.java` | `formatMotivoDelCambioWithCU()` |

---

## 5. CONDICIONES DE ACTIVACION

### 5.1 Criterios para Detectar Analisis Expirado

Un lote se procesa como "analisis expirado" cuando:

| Condicion | Descripcion |
|-----------|-------------|
| Stock > 0 | El lote tiene bultos con cantidad disponible |
| Fecha Reanalisis Vigente | La `fechaReanalisisVigente` del lote no es null |
| Fecha <= Hoy | La fecha de reanalisis es igual o anterior a la fecha actual |

### 5.2 Query de Busqueda

**Metodo:** `findAllLotesAnalisisExpirado()`

```java
loteRepository.findLotesConStockOrder().stream()
    .filter(l -> {
        LocalDate f = l.getFechaReanalisisVigente();
        return f != null && !f.isAfter(hoy); // <= hoy
    })
    .toList();
```

**Query base `findLotesConStockOrder`:**
```sql
SELECT l FROM Lote l
WHERE EXISTS (
    SELECT 1 FROM Bulto b
    WHERE b.lote = l AND b.cantidadActual > 0
)
ORDER BY l.fechaIngreso ASC, l.codigoLote ASC
```

---

## 6. LOGICA DE NEGOCIO

### 6.1 Metodo Principal: validarFecha()

Este metodo es el punto de entrada del proceso batch:

```java
@Scheduled(cron = "0 0 5 * * *")
@Transactional
public void validarFecha() {
    procesarLotesAnalisisExpirado(findAllLotesAnalisisExpirado());
    procesarLotesVencidos(findAllLotesVencidos());  // CU10
}
```

### 6.2 Flujo de Procesamiento CU9

**Metodo:** `procesarLotesAnalisisExpirado(List<Lote> lotesReanalisis)`

1. **Validar lista:** Si esta vacia, log debug y retornar.

2. **Crear DTO base:**
   - `fechaMovimiento`: Fecha actual
   - `fechaYHoraCreacion`: OffsetDateTime.now()
   - `motivoDelCambio`: "(CU9) ANALISIS EXPIRADO POR FECHA: {fecha}"

3. **Para cada lote:**
   - Crear movimiento MODIFICACION/EXPIRACION_ANALISIS
   - Cambiar dictamen del lote a ANALISIS_EXPIRADO
   - Guardar lote
   - Log info con detalles

4. **Manejo de errores:** Si falla un lote, log error y continuar con el siguiente.

### 6.3 Creacion del Movimiento

**Metodo:** `persistirMovimientoExpiracionAnalisis(MovimientoDTO dto, Lote lote)`

- **Tipo:** MODIFICACION
- **Motivo:** EXPIRACION_ANALISIS
- **Usuario:** system_auto (ADMIN)
- **Dictamen Inicial:** Dictamen actual del lote
- **Dictamen Final:** ANALISIS_EXPIRADO
- **MotivoDelCambio:** Formateado con prefijo `[CU9]`

---

## 7. TEMPLATE (Consulta de Monitoreo)

### 7.1 Estilo Visual

La consulta de fechas utiliza la paleta de colores **purpura** que identifica las operaciones de calidad:

- **Fondo de pagina:** Blanco con header purpura gradiente
- **Tarjeta de tabla:** Blanca con sombra suave
- **Indicadores de dias:**
  - **Rojo:** Dias < 0 (expirado) o <= 30
  - **Amarillo:** Dias entre 31 y 60
  - **Verde:** Dias > 60

### 7.2 Librerias Utilizadas

| Libreria | Proposito |
|----------|-----------|
| Bootstrap 5.3 | Framework CSS base |
| Bootstrap Icons 1.11 | Iconografia |
| jQuery 3.7.1 | Manipulacion DOM |
| DataTables 1.13.8 | Tabla interactiva con filtros |
| JSZip 3.10.1 | Exportacion a Excel |

### 7.3 Funcionalidades de la Tabla

| Funcionalidad | Descripcion |
|---------------|-------------|
| Filtro por columna | Inputs en cada cabecera |
| Ordenamiento | Por dias hasta reanalisis (ascendente por defecto) |
| Paginacion | 10, 25, 50, 100 o todos |
| Exportacion | Boton Excel (excluye columna acciones) |
| Idioma | Espanol (es-ES) |

### 7.4 Columnas de la Tabla

| Columna | Descripcion |
|---------|-------------|
| Producto | Nombre generico + codigo |
| Codigo Interno | Link al detalle del lote |
| Cantidad Actual | Cantidad disponible |
| Unidad | Unidad de medida |
| Dias Reanalisis | Dias hasta fecha reanalisis (coloreado) |
| Dias Vencimiento | Dias hasta fecha vencimiento (coloreado) |
| Fecha Reanalisis Vigente | Fecha del ultimo analisis |
| Fecha Vencimiento Vigente | Fecha del ultimo analisis |
| Fecha Rean. Prov. | Fecha original del proveedor |
| Fecha Vto. Prov. | Fecha original del proveedor |
| Acciones | Menu desplegable con links |

---

## 8. CONTROLLER (Consulta)

### 8.1 Clase: LotesController

El controller maneja la consulta de fechas junto con otras consultas de lotes.

### 8.2 Endpoint

| Metodo | URL | Descripcion |
|--------|-----|-------------|
| GET | /lotes/list-fechas-lotes | Muestra consulta de fechas |

### 8.3 Modelo en GET

- `loteDTOs`: Lista de lotes con fechas (dictaminados con stock)
- `recordsPerPage`: Configuracion de paginacion
- `dateFormat`: Formato de fecha del sistema

### 8.4 Query de Datos

**Metodo:** `loteService.findLotesDictaminadosConStock()`

```sql
SELECT DISTINCT l FROM Lote l
LEFT JOIN FETCH l.analisisList aFetch
WHERE EXISTS (
    SELECT 1 FROM Bulto b
    WHERE b.lote = l AND b.cantidadActual > 0
)
AND (
    l.fechaVencimientoProveedor IS NOT NULL
    OR l.fechaReanalisisProveedor IS NOT NULL
    OR EXISTS (
        SELECT 1 FROM Analisis a
        WHERE a.lote = l
          AND a.activo = TRUE
          AND a.dictamen IS NOT NULL
          AND (a.fechaVencimiento IS NOT NULL OR a.fechaReanalisis IS NOT NULL)
    )
)
ORDER BY l.fechaIngreso ASC, l.codigoLote ASC
```

---

## 9. SERVICE

### 9.1 Clase: FechaValidatorService

El servicio implementa la logica de los procesos automaticos CU9 y CU10. Extiende `AbstractCuService` para acceder a repositorios.

### 9.2 Metodos Principales

| Metodo | Responsabilidad |
|--------|-----------------|
| `validarFecha()` | Punto de entrada @Scheduled |
| `findAllLotesAnalisisExpirado()` | Buscar lotes con analisis expirado |
| `procesarLotesAnalisisExpirado()` | Procesar lista de lotes |
| `persistirMovimientoExpiracionAnalisis()` | Crear movimiento individual |
| `getSystemUser()` | Obtener/crear usuario system_auto |

### 9.3 Usuario del Sistema

El proceso batch usa un usuario especial:

```java
User getSystemUser() {
    return userRepository.findByUsername("system_auto")
        .orElseGet(() -> {
            Role adminRole = roleRepository.findByName(RoleEnum.ADMIN.name())
                .orElseGet(() -> roleRepository.save(Role.fromEnum(RoleEnum.ADMIN)));
            User systemUser = new User("system_auto", "N/A", adminRole);
            return userRepository.save(systemUser);
        });
}
```

---

## 10. PERSISTENCIA

### 10.1 Entidades Afectadas

| Entidad | Accion | Estado Final |
|---------|--------|--------------|
| Lote | UPDATE | dictamen=ANALISIS_EXPIRADO |
| Movimiento | INSERT | tipo=MODIFICACION, motivo=EXPIRACION_ANALISIS |
| User (system_auto) | INSERT (si no existe) | rol=ADMIN |
| Role (ADMIN) | INSERT (si no existe) | nivel=6 |

### 10.2 Movimiento Generado

| Campo | Valor |
|-------|-------|
| tipo | MODIFICACION |
| motivo | EXPIRACION_ANALISIS |
| fecha | Fecha actual |
| dictamenInicial | Dictamen anterior del lote |
| dictamenFinal | ANALISIS_EXPIRADO |
| motivoDelCambio | `[CU9](CU9) ANALISIS EXPIRADO POR FECHA: {fecha}` |
| usuario | system_auto |

---

## 11. AUDITORIA (ANMAT)

El sistema cumple con los requerimientos de la Disposicion ANMAT 4159:

- **Hibernate Envers:** Las entidades Lote y Movimiento estan auditadas. Cada cambio genera registro en tablas `_aud`.

- **Campo motivoDelCambio:** Incluye mensaje automatico con fecha del proceso.

- **Prefijo de CU:** El motivoDelCambio incluye `[CU9]` para trazabilidad.

- **Registro de procesamiento:** Todos los lotes procesados quedan registrados con movimiento y log.

---

## 12. SEGURIDAD

### 12.1 Proceso Automatico

- **Sin sesion HTTP:** El proceso batch no depende de sesion de usuario.
- **Usuario interno:** Ejecuta como `system_auto` con permisos ADMIN.
- **Aislamiento:** Cada lote se procesa en try-catch individual.

### 12.2 Consulta de Fechas

- **CSRF:** Token incluido automaticamente.
- **Autorizacion:** Solo roles definidos en PermisosCasoUsoEnum.
- **XSS:** Datos escapados con th:text.

---

## 13. MANEJO DE ERRORES

### 13.1 Proceso Batch

| Situacion | Comportamiento |
|-----------|----------------|
| Lista vacia | Log debug, continuar |
| Error en lote individual | Log error con codigoLote y mensaje, continuar con siguiente |
| Error al obtener usuario | Se crea automaticamente |

### 13.2 Logging de Errores

```java
log.error(
    "[CU9] Error procesando expiración de análisis para lote: {} - {}",
    lote.getCodigoLote(), e.getMessage(), e);
```

---

## 14. LOGGING

El service registra eventos en los siguientes puntos:

| Nivel | Mensaje | Condicion |
|-------|---------|-----------|
| DEBUG | `[CU9] No hay lotes con análisis expirado para procesar` | Lista vacia |
| INFO | `[CU9] Procesando {} lotes con análisis expirado` | Inicio proceso |
| INFO | `[CU9] Análisis expirado procesado - Lote: {loteProveedor}, FechaReanalisis: {fechaReanalisisVigente}` | Lote procesado |
| ERROR | `[CU9] Error procesando expiración de análisis para lote: {codigoLote} - {mensaje}` | Error individual |

Todos los logs usan el prefijo `[CU9]` para facilitar la busqueda.

**Nota:** El log de lote procesado muestra `loteProveedor` (codigo del proveedor), no `codigoLote` (codigo interno).

---

## 15. MEJORAS SUGERIDAS

| Prioridad | Mejora |
|-----------|--------|
| BAJA | Agregar notificacion por email cuando se procesen lotes |
| BAJA | Configurar horario de ejecucion via base de datos |
| BAJA | Agregar dashboard con estadisticas de lotes procesados |
| MEDIA | Agregar alertas tempranas (7 dias antes de expiracion) |

---

*Documento generado: 2025-12-14*
*Version: 1.0*
