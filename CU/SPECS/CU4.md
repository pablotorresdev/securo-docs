# CU4 - BAJA DEVOLUCION COMPRA

**Caso de Uso:** Devolucion de Compra al Proveedor
**URL Base:** `/compras/baja/devolucion-compra`
**Tipo:** BAJA (Egreso/Devolucion)
**Categoria:** Devoluciones (color rojo)
**Identificador Interno:** `CU4_DEVOLUCION_COMPRA`

---

## 1. DESCRIPCION

Permite registrar la devolucion de un lote completo al proveedor. El lote es devuelto con todas sus cantidades y bultos, quedando con estado DEVUELTO y dictamen RECHAZADO. Si hay un analisis en curso, este es cancelado automaticamente. Genera un movimiento de BAJA con motivo DEVOLUCION_COMPRA.

---

## 2. ROLES AUTORIZADOS

| Rol | Nivel | Acceso |
|-----|-------|--------|
| ADMIN | 6 | Permitido |
| ANALISTA_PLANTA | 2 | Permitido |
| Otros | - | No permitido |

**Anotacion:** `@PreAuthorize("hasAnyAuthority('ROLE_ADMIN', 'ROLE_ANALISTA_PLANTA')")`

---

## 3. FLUJO DE PANTALLAS

```
+---------------------------------------------------------------------+
|  1. FORMULARIO                                                       |
|     GET /compras/baja/devolucion-compra                             |
|     Template: compras/baja/devolucion-compra.html                   |
|  +---------------------------------------------------------------+  |
|  |  Select2: Lote a devolver                                     |  |
|  |  (codigo, lote prov, remito, proveedor, producto, dictamen)   |  |
|  |  Fecha de devolucion                                          |  |
|  |  Motivo del cambio (min 20 chars)                             |  |
|  +---------------------------------------------------------------+  |
|  [Cancelar]                              [Vista Previa] ->          |
+---------------------------------------------------------------------+
                              |
                              | POST /compras/baja/devolucion-compra/confirm
                              v
+---------------------------------------------------------------------+
|  2. CONFIRMACION                                                     |
|     Template: compras/baja/devolucion-compra-confirm.html           |
|  +---------------------------------------------------------------+  |
|  |  Warning Box: Consecuencias de la devolucion                  |  |
|  |  - Se devolvera el lote completo al proveedor                 |  |
|  |  - Estado -> DEVUELTO, Dictamen -> RECHAZADO                  |  |
|  |  - Cantidad -> 0                                              |  |
|  |  Preview: Info completa del lote                              |  |
|  +---------------------------------------------------------------+  |
|  [Volver a Editar]                   [Confirmar Devolucion] ->      |
+---------------------------------------------------------------------+
                              |
                              | POST /compras/baja/devolucion-compra
                              v
+---------------------------------------------------------------------+
|  3. EXITO                                                            |
|     GET /compras/baja/devolucion-compra-ok (redirect)               |
|     Template: compras/baja/devolucion-compra-ok.html                |
|  +---------------------------------------------------------------+  |
|  |  Devolucion Exitosa - CU4                                     |  |
|  |  Lote devuelto al proveedor                                   |  |
|  |  Estado final: DEVUELTO / RECHAZADO                           |  |
|  |  Tabla de movimientos                                         |  |
|  +---------------------------------------------------------------+  |
|  [Volver al Inicio]                                                 |
+---------------------------------------------------------------------+
```

---

## 4. ARCHIVOS INVOLUCRADOS

### Frontend (Templates)
| Archivo | Descripcion |
|---------|-------------|
| `templates/compras/baja/devolucion-compra.html` | Formulario principal (168 lineas) |
| `templates/compras/baja/devolucion-compra-confirm.html` | Vista previa/confirmacion |
| `templates/compras/baja/devolucion-compra-ok.html` | Pantalla de exito |

### Backend
| Archivo | Descripcion |
|---------|-------------|
| `controller/cu/BajaDevolucionCompraController.java` | Controlador (137 lineas) |
| `service/cu/BajaDevolucionCompraService.java` | Servicio de negocio (129 lineas) |
| `service/cu/AbstractCuService.java` | Clase base con validaciones comunes |

### Validadores Especializados
| Archivo | Responsabilidad |
|---------|-----------------|
| `service/cu/validator/FechaValidator.java` | Validaciones de fechas |

### Utilidades de Creacion de Entidades
| Archivo | Uso en CU4 |
|---------|------------|
| `utils/MovimientoBajaUtils.java` | Factory para crear Movimiento BAJA |

### DTOs y Entidades
| Archivo | Uso en CU4 |
|---------|------------|
| `dto/MovimientoDTO.java` | DTO principal del formulario |
| `entity/Lote.java` | Entidad Lote (cambia estado y dictamen) |
| `entity/Bulto.java` | Entidad Bulto (cambia estado y cantidad) |
| `entity/Movimiento.java` | Entidad Movimiento |
| `entity/DetalleMovimiento.java` | Detalle de movimiento por bulto |
| `entity/Analisis.java` | Entidad Analisis (se cancela si en curso) |

---

## 5. CAMPOS DEL FORMULARIO

### 5.1 Obligatorios

| Campo | HTML ID | Tipo | Validacion Browser | Validacion Server |
|-------|---------|------|-------------------|-------------------|
| Lote a Devolver | `codigoLote` | select (Select2) | `required` | Validado en service |
| Fecha de Devolucion | `fechaMovimiento` | date | `required` | `@NotNull`, `@PastOrPresent` |
| Motivo del Cambio | `motivoDelCambio` | textarea | `required`, `minlength="20"` | `@NotNull`, `@Size(min=20)` |

### 5.2 Logica del Select de Lotes

El select muestra lotes "devolvibles" (elegibles para devolucion):
- Activos en el sistema (`activo = true`)
- Dictamen en (RECIBIDO, CUARENTENA, APROBADO, RECHAZADO)
- Estado diferente de DEVUELTO (pueden ser NUEVO, DISPONIBLE, EN_USO, etc.)
- Producto de tipo externo (API, EXCIPIENTE, ACOND_PRIMARIO, ACOND_SECUNDARIO)
- Con stock disponible (al menos un bulto con `cantidadActual > 0`)

Cada opcion incluye: codigo interno, lote proveedor, numero de remito, nombre del proveedor, nombre del producto y dictamen actual.

---

## 6. VALIDACIONES POR CAPA

El sistema implementa validacion en 3 capas, cada una con responsabilidades especificas:

### 6.1 CAPA 1: FRONTEND (Browser - HTML5 + JavaScript)

**Ubicacion:** `templates/compras/baja/devolucion-compra.html`

**Tipo:** Validacion inmediata en el navegador antes del submit.

| Campo | Atributo HTML5 | Comportamiento |
|-------|---------------|----------------|
| `codigoLote` | `required` | Bloquea submit si no seleccionado |
| `fechaMovimiento` | `required`, `type="date"` | Bloquea submit si vacio |
| `motivoDelCambio` | `required`, `minlength="20"` | Minimo 20 caracteres |

**JavaScript Dinamico:**
- Inicializa fecha de devolucion con fecha actual (timezone Argentina)
- Configura Select2 para el selector de lotes con busqueda

**Limitaciones:** No valida existencia del lote ni fecha vs ingreso (delegado al servidor).

---

### 6.2 CAPA 2: BEAN VALIDATION (Spring - Anotaciones Jakarta)

**Ubicacion:** `dto/MovimientoDTO.java`

Se ejecuta automaticamente al recibir el DTO en el controller mediante `@Valid`.

#### Campos Obligatorios

| Campo | Anotaciones | Mensaje de Error |
|-------|-------------|------------------|
| fechaMovimiento | `@NotNull`, `@PastOrPresent` | "La fecha del movimiento es obligatoria" / "no puede ser futura" |
| motivoDelCambio | `@NotNull`, `@Size(min=20)` | "El motivo del cambio es obligatorio" / "debe tener al menos 20 caracteres" |

---

### 6.3 CAPA 3: VALIDACION DE NEGOCIO (Service Layer)

**Ubicacion:** `BajaDevolucionCompraService.java`

Validaciones de reglas de negocio complejas que no pueden expresarse con anotaciones.

#### Secuencia de Validacion

El metodo `validarDevolucionCompraInput()` ejecuta las siguientes validaciones en orden:

| # | Validador | Clase |
|---|-----------|-------|
| 1 | validarMotivoDelCambio | AbstractCuService |
| 2 | validarLoteExiste | Service (inline) |
| 3 | validarFechaMovimientoPosteriorIngresoLote | FechaValidator |

#### Detalle de Validaciones

**1. validarMotivoDelCambio** (ANMAT Disp. 4159, Anexo 6, Req. 9)

| Regla | Mensaje de Error |
|-------|------------------|
| `motivoDelCambio` no nulo y >= 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

**2. validarLoteExiste**

| Regla | Mensaje de Error |
|-------|------------------|
| El lote con `codigoLote` debe existir y estar activo | "Lote no encontrado." |

**3. validarFechaMovimientoPosteriorIngresoLote**

| Regla | Mensaje de Error |
|-------|------------------|
| `fechaMovimiento` >= `fechaIngreso` del lote | "La fecha del movimiento no puede ser anterior a la fecha de ingreso del lote" |

---

### 6.4 RESUMEN DE VALIDACIONES POR CAMPO

| Campo | Frontend | Bean Validation | Negocio |
|-------|----------|-----------------|---------|
| codigoLote | required | - | Existencia verificada |
| fechaMovimiento | required | @NotNull, @PastOrPresent | >= fechaIngreso lote |
| motivoDelCambio | required, minlength | @NotNull, @Size(min=20) | >= 20 caracteres (ANMAT) |

---

## 7. TEMPLATE (Frontend)

### 7.1 Estilo Visual

El formulario utiliza la paleta de colores **rojo** que identifica las operaciones de devolucion/baja en todo el sistema. Los elementos principales incluyen:

- **Fondo de pagina:** Rojo claro (`bg-devoluciones`)
- **Tarjeta del formulario:** Blanca con borde rojo y sombra suave
- **Botones primarios:** Gradiente rojo
- **Iconos de seccion:** Bootstrap Icons en color rojo

El selector de Lote utiliza **Select2** para permitir busqueda dentro de las opciones. Cada opcion del lote incluye: codigo, lote proveedor, remito, proveedor, producto y dictamen actual para facilitar la seleccion.

### 7.2 Librerias Utilizadas

| Libreria | Proposito |
|----------|-----------|
| Bootstrap 5.3 | Framework CSS base, grid responsivo |
| Bootstrap Icons | Iconografia en todo el formulario |
| jQuery 3.7 | Manipulacion DOM y eventos |
| Select2 | Dropdown con busqueda de texto |

### 7.3 Comportamiento JavaScript

El script del template ejecuta las siguientes logicas al cargar la pagina:

1. **Inicializacion de fecha:** Establece la fecha de devolucion con la fecha actual usando timezone de Argentina.

2. **Configuracion de selector:** Convierte el select de Lote en componente Select2 con busqueda.

3. **Restauracion de datos:** Si el formulario se recarga por un error de validacion, los datos previamente ingresados se restauran automaticamente.

4. **Prevencion de doble submit:** Al enviar el formulario, el boton se deshabilita y muestra un spinner con texto "Procesando..." para evitar envios duplicados. Aplica tanto al boton "Vista Previa" como al boton "Confirmar Devolucion" en la pantalla de confirmacion.

### 7.4 Interacciones Dinamicas

Este CU no consume endpoints AJAX adicionales. Los datos del lote vienen precargados en el Select via Thymeleaf.

---

## 8. CONTROLLER

### 8.1 Clase: BajaDevolucionCompraController

El controlador maneja el flujo de 3 pasos del caso de uso y extiende `AbstractCuController` para heredar servicios comunes (LoteService, ProductoService, ProveedorService).

### 8.2 Flujo de Endpoints

**GET /devolucion-compra** - Muestra el formulario vacio

Carga en el modelo los lotes devolvibles (mediante `loteService.findAllForDevolucionCompraDTOs()`). Inicializa el MovimientoDTO vacio.

**POST /devolucion-compra/confirm** - Valida y muestra vista previa

Recibe el DTO con los datos del formulario. Ejecuta Bean Validation automaticamente y luego invoca las validaciones de negocio del Service. Si hay errores, retorna al formulario con los mensajes. Si todo es valido, carga la informacion completa del lote para mostrar en la pantalla de confirmacion.

**POST /devolucion-compra** - Persiste los datos

Re-valida los datos por seguridad. Establece la fecha/hora de creacion y delega al Service para persistir la devolucion. Redirige a la pantalla de exito con el LoteDTO resultante.

### 8.3 Caracteristicas Destacadas

- **Transaccion ReadOnly en confirm:** El endpoint /confirm usa `@Transactional(readOnly = true)` para garantizar que no modifica la base de datos durante la vista previa.
- **Doble validacion:** Los datos se validan tanto en /confirm como en el POST final por seguridad.
- **Flash Attributes:** El resultado se pasa a la pantalla de exito mediante RedirectAttributes para sobrevivir al redirect.
- **Filtrado de lotes:** Solo muestra lotes elegibles para devolucion (productos externos con dictamen RECIBIDO/CUARENTENA/APROBADO/RECHAZADO, stock disponible y estado diferente de DEVUELTO).

---

## 9. SERVICE

### 9.1 Clase: BajaDevolucionCompraService

El servicio contiene la logica de negocio del caso de uso. Extiende `AbstractCuService` para heredar validadores comunes y repositorios.

### 9.2 Metodo Principal: bajaBultosDevolucionCompra

Este metodo ejecuta toda la operacion en una unica transaccion. El flujo es:

1. **Establecer contexto de auditoria:** Registra "CU4_DEVOLUCION_COMPRA" para que Hibernate Envers lo capture en las tablas de auditoria.

2. **Obtener usuario actual:** Recupera el usuario autenticado para registrar quien realizo la devolucion.

3. **Cargar el Lote:** Busca el lote por codigo. Si no existe, lanza excepcion.

4. **Crear Movimiento:** Genera un movimiento de tipo BAJA con motivo DEVOLUCION_COMPRA usando `MovimientoBajaUtils.createMovimientoDevolucionCompra()`. Registra dictamenInicial, cantidad y unidad.

5. **Crear Detalles:** Por cada bulto del lote, crea un DetalleMovimiento con la cantidad actual del bulto.

6. **Persistir Movimiento:** Guarda el movimiento con sus detalles.

7. **Actualizar Bultos:** Pone la cantidad de cada bulto en CERO y cambia su estado a DEVUELTO.

8. **Actualizar Lote:** Cambia el estado a DEVUELTO, el dictamen a RECHAZADO y la cantidad actual a CERO.

9. **Cancelar Analisis (si existe):** Si hay un analisis en curso (sin dictamen), lo cancela automaticamente.

10. **Persistir y retornar:** Guarda el lote y retorna el DTO para mostrar en la pantalla de exito.

---

## 10. PERSISTENCIA

### 10.1 Entidades Afectadas

| Entidad | Accion | Estado Final |
|---------|--------|--------------|
| Lote | UPDATE | estado=DEVUELTO, dictamen=RECHAZADO, cantidadActual=0 |
| Bulto (todos) | UPDATE | estado=DEVUELTO, cantidadActual=0 |
| Movimiento | INSERT | tipo=BAJA, motivo=DEVOLUCION_COMPRA |
| DetalleMovimiento (1 por bulto) | INSERT | registra cantidad devuelta por bulto |
| Analisis (si existe en curso) | UPDATE | dictamen=CANCELADO |

### 10.2 Movimiento Generado

El movimiento de baja incluye:

- **tipo:** BAJA
- **motivo:** DEVOLUCION_COMPRA
- **dictamenInicial:** Dictamen anterior del lote
- **dictamenFinal:** RECHAZADO
- **cantidad:** Cantidad total del lote al momento de devolucion
- **unidadMedida:** Unidad de medida del lote
- **motivoDelCambio:** Texto ingresado por el usuario con prefijo [CU4] (ANMAT)
- **creadoPor:** Usuario que realizo la operacion

### 10.3 Cascade y Soft Delete

Los DetalleMovimiento se guardan automaticamente al guardar el Movimiento (cascade PERSIST).

Todas las entidades usan borrado logico: en lugar de DELETE fisico, se ejecuta UPDATE activo=false.

---

## 11. AUDITORIA (ANMAT)

El sistema cumple con los requerimientos de la Disposicion ANMAT 4159:

- **Hibernate Envers:** Todas las entidades (Lote, Bulto, Movimiento, Analisis) estan auditadas. Cada cambio genera un registro en las tablas `_aud` con usuario, timestamp y caso de uso.

- **Campo motivoDelCambio:** El movimiento incluye el motivo ingresado por el usuario (mínimo 20 caracteres), formateado con prefijo `[CU4]`. Este campo es obligatorio para cumplir con el Requerimiento 9 del Anexo 6.

- **Contexto de caso de uso:** El identificador "CU4_DEVOLUCION_COMPRA" se guarda en cada revision de auditoria.

- **Transicion de estado:** Se registra el cambio de estado (anterior -> DEVUELTO) y dictamen (anterior -> RECHAZADO) para trazabilidad completa.

- **Cancelacion de analisis:** Si se cancela un analisis en curso, queda registrado con dictamen CANCELADO.

---

## 12. SEGURIDAD

- **CSRF:** El formulario incluye token CSRF como campo oculto. Las llamadas AJAX incluyen el token en headers.
- **Autorizacion:** Solo usuarios con rol ADMIN o ANALISTA_PLANTA pueden acceder.
- **XSS:** Todos los datos se renderizan con escape automatico de HTML (th:text).
- **Transaccion ReadOnly:** El endpoint de confirmacion no puede modificar datos.

---

## 13. MANEJO DE ERRORES

Los errores se manejan en diferentes niveles:

- **Service:** Si el lote no existe, se rechaza el campo con mensaje descriptivo.
- **Controller:** Los errores de validacion (Bean Validation y negocio) se muestran junto a cada campo en el formulario.
- **Excepciones no controladas:** Redirigen a la pagina de error global del sistema.

---

## 14. LOGGING

El service registra eventos en los siguientes puntos:

- **Inicio:** Usuario y codigo de lote involucrados
- **Analisis cancelado:** Si habia un analisis en curso (nivel DEBUG)
- **Completado:** Lote, estado final, dictamen final y motivo del cambio

Todos los logs usan el prefijo `[CU4]` para facilitar la busqueda.

---

## 15. MEJORAS SUGERIDAS

| Prioridad | Mejora |
|-----------|--------|
| MEDIA | Confirmacion modal adicional antes de devolver |
| BAJA | Fallback local si los CDN no responden |

---

*Documento generado: 2025-12-13*
*Version: 2.5*
