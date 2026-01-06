# CU3 - MUESTREO

**Caso de Uso:** Muestreo de Lotes
**Tipo:** BAJA (Egreso/Consumo)
**Categoria:** Calidad (color purpura)
**Identificador Interno:** `CU3_MUESTREO`

---

## 1. DESCRIPCION

El caso de uso CU3 permite registrar el consumo de muestras de un lote para analisis de Control de Calidad. Existen **dos modalidades** segun el tipo de producto:

| Modalidad | Aplica a | URL Base | Seleccion |
|-----------|----------|----------|-----------|
| **Multi-Bulto** | Productos NO trazados (API, Excipiente, etc.) | `/calidad/baja/muestreo-multi-bulto` | Cantidades por bulto |
| **Trazable** | Productos trazados (UNIDAD_VENTA) | `/calidad/baja/muestreo-trazable` | Trazas individuales |

Ambas modalidades generan un movimiento de BAJA con motivo MUESTREO.

---

## 1.1 MODALIDAD MULTI-BULTO (Seccion 2-15)

Permite registrar el consumo de muestras desde multiples bultos de un lote. Reduce el stock disponible de cada bulto segun las cantidades indicadas y marca como CONSUMIDOS los bultos que queden sin stock.

---

## 2. ROLES AUTORIZADOS

| Rol | Nivel | Acceso |
|-----|-------|--------|
| ADMIN | 6 | Permitido |
| ANALISTA_CONTROL_CALIDAD | 2 | Permitido |
| Otros | - | No permitido |

**Anotacion:** `@PreAuthorize("hasAnyAuthority('ROLE_ADMIN', 'ROLE_ANALISTA_CONTROL_CALIDAD')")`

---

## 3. FLUJO DE PANTALLAS

```
+---------------------------------------------------------------------+
|  1. FORMULARIO                                                       |
|     GET /calidad/baja/muestreo-multi-bulto                          |
|     Template: calidad/baja/muestreo-multi-bulto.html                |
|  +---------------------------------------------------------------+  |
|  |  Select2: Lote a muestrear                                    |  |
|  |  (codigo, producto, analisis, dictamen)                       |  |
|  |  Detalles del Lote (dinamico al seleccionar)                  |  |
|  |  Cantidades por Bulto (input por cada bulto)                  |  |
|  |  Fecha de muestreo                                            |  |
|  |  Motivo del cambio (min 20 chars)                             |  |
|  +---------------------------------------------------------------+  |
|  [Cancelar]                              [Vista Previa] ->          |
+---------------------------------------------------------------------+
                              |
                              | POST /calidad/baja/muestreo-multi-bulto/confirm
                              v
+---------------------------------------------------------------------+
|  2. CONFIRMACION                                                     |
|     Template: calidad/baja/muestreo-multi-bulto-confirm.html        |
|  +---------------------------------------------------------------+  |
|  |  Warning Box: Consecuencias del muestreo                      |  |
|  |  - Se reducira el stock de los bultos                         |  |
|  |  - Bultos sin stock -> CONSUMIDOS                             |  |
|  |  - Genera registro de movimiento BAJA                         |  |
|  |  Preview: Info completa del lote                              |  |
|  |  Cantidades por bulto a muestrear                             |  |
|  +---------------------------------------------------------------+  |
|  [Volver a Editar]                    [Confirmar Muestreo] ->       |
+---------------------------------------------------------------------+
                              |
                              | POST /calidad/baja/muestreo-multi-bulto
                              v
+---------------------------------------------------------------------+
|  3. EXITO                                                            |
|     GET /calidad/baja/muestreo-multi-bulto-ok (redirect)            |
|     Template: calidad/baja/muestreo-multi-bulto-ok.html             |
|  +---------------------------------------------------------------+  |
|  |  Muestreo Exitoso - CU3                                       |  |
|  |  Lote actualizado con stock reducido                          |  |
|  |  Cantidades muestreadas por bulto                             |  |
|  |  Estado actualizado de bultos                                 |  |
|  |  Tabla de movimientos                                         |  |
|  +---------------------------------------------------------------+  |
|  [Nuevo Muestreo]                         [Volver al Inicio]        |
+---------------------------------------------------------------------+
```

---

## 4. ARCHIVOS INVOLUCRADOS

### Frontend (Templates)
| Archivo | Descripcion |
|---------|-------------|
| `templates/calidad/baja/muestreo-multi-bulto.html` | Formulario principal (578 lineas) |
| `templates/calidad/baja/muestreo-multi-bulto-confirm.html` | Vista previa/confirmacion |
| `templates/calidad/baja/muestreo-multi-bulto-ok.html` | Pantalla de exito |

### Backend
| Archivo | Descripcion |
|---------|-------------|
| `controller/cu/BajaMuestreoBultoController.java` | Controlador (213 lineas) |
| `service/cu/BajaMuestreoBultoService.java` | Servicio orquestador (151 lineas) |
| `service/cu/MuestreoMultiBultoService.java` | Servicio de negocio especializado |
| `service/cu/AbstractCuService.java` | Clase base con validaciones comunes |

### Validadores Especializados
| Archivo | Responsabilidad |
|---------|-----------------|
| `service/cu/validator/CantidadValidator.java` | Validaciones de cantidades y bultos |
| `service/cu/validator/FechaValidator.java` | Validaciones de fechas |

### Utilidades de Creacion de Entidades
| Archivo | Uso en CU3 |
|---------|------------|
| `utils/MovimientoBajaUtils.java` | Factory para crear Movimiento BAJA |

### DTOs y Entidades
| Archivo | Uso en CU3 |
|---------|------------|
| `dto/LoteDTO.java` | DTO principal del formulario |
| `entity/Lote.java` | Entidad Lote (actualiza cantidadActual) |
| `entity/Bulto.java` | Entidad Bulto (actualiza cantidad y estado) |
| `entity/Movimiento.java` | Entidad Movimiento |
| `entity/DetalleMovimiento.java` | Detalle de movimiento por bulto |

---

## 5. CAMPOS DEL FORMULARIO

### 5.1 Obligatorios

| Campo | HTML ID | Tipo | Validacion Browser | Validacion Server |
|-------|---------|------|-------------------|-------------------|
| Lote a Muestrear | `loteMuestreo` | select (Select2) | `required` | Validado en service |
| Fecha de Muestreo | `fechaEgreso` | date | `required` | `@NotNull` |
| Cantidades por Bulto | `cantidadesBultos[n]` | number[] | `required`, `min="0"`, `step="0.01"` | `@NotEmpty` |
| Unidad por Bulto | `unidadMedidaBultos[n]` | select[] | `required` | Validado en service |
| Motivo del Cambio | `motivoDelCambio` | textarea | `required`, `minlength="20"` | `@NotNull`, `@Size(min=20)` |

### 5.2 Campos Hidden

| Campo | HTML ID | Descripcion |
|-------|---------|-------------|
| codigoLote | `codigoLote` | Se completa al seleccionar lote |
| nroAnalisis | `nroAnalisis` | Ultimo analisis del lote |
| nroBultoList[n] | generado | IDs de los bultos |

---

## 6. VALIDACIONES POR CAPA

El sistema implementa validacion en 3 capas, cada una con responsabilidades especificas:

### 6.1 CAPA 1: FRONTEND (Browser - HTML5 + JavaScript)

**Ubicacion:** `templates/calidad/baja/muestreo-multi-bulto.html`

**Tipo:** Validacion inmediata en el navegador antes del submit.

| Campo | Atributo HTML5 | Comportamiento |
|-------|---------------|----------------|
| `loteMuestreo` | `required` | Bloquea submit si no seleccionado |
| `fechaEgreso` | `required`, `type="date"` | Bloquea submit si vacio |
| `cantidadesBultos[n]` | `required`, `min="0"`, `step="0.01"` | Valida formato numerico |
| `unidadMedidaBultos[n]` | `required` | Bloquea submit si no seleccionado |
| `motivoDelCambio` | `required`, `minlength="20"` | Minimo 20 caracteres |

**JavaScript Dinamico:**
- Genera inputs de cantidad/unidad por cada bulto del lote seleccionado
- Carga subunidades compatibles via AJAX (`/enums/subunidades`)
- Cache de subunidades para evitar llamadas repetitivas
- Inicializa fecha de muestreo con fecha actual (timezone Argentina)

**Limitaciones:** No valida que las cantidades no excedan el stock de cada bulto (delegado al servidor).

---

### 6.2 CAPA 2: BEAN VALIDATION (Spring - Anotaciones Jakarta)

**Ubicacion:** `dto/LoteDTO.java`

Se ejecuta automaticamente al recibir el DTO en el controller mediante `@Validated`.

#### Campos Obligatorios

| Campo | Anotaciones | Mensaje de Error |
|-------|-------------|------------------|
| fechaEgreso | `@NotNull` | "La fecha de egreso es obligatoria" |
| cantidadesBultos | `@NotEmpty` | "Debe especificar cantidades por bulto" |
| motivoDelCambio | `@NotNull`, `@Size(min=20)` | "El motivo del cambio es obligatorio" / "debe tener al menos 20 caracteres" |

---

### 6.3 CAPA 3: VALIDACION DE NEGOCIO (Service Layer)

**Ubicacion:** `MuestreoMultiBultoService.java` + validadores en `service/cu/validator/`

Validaciones de reglas de negocio complejas que no pueden expresarse con anotaciones.

#### Secuencia de Validacion

El metodo `validarMuestreoMultiBultoInput()` ejecuta las siguientes validaciones en orden:

| # | Validador                              | Clase |
|---|----------------------------------------|-------|
| 1 | validarMotivoDelCambio                 | AbstractCuService |
| 2 | validarLoteExiste                      | Service (inline) |
| 3 | validarAnalisisAsociado                | Service (inline) |
| 4 | validarFechaEgresoLoteDtoPosteriorLote | FechaValidator |
| 5 | validarCantidadesPorMedidas            | CantidadValidator |

#### Detalle de Validaciones

**1. validarMotivoDelCambio** (ANMAT Disp. 4159, Anexo 6, Req. 9)

| Regla                                        | Mensaje de Error |
|----------------------------------------------|------------------|
| `motivoDelCambio` no nulo y >= 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

**2. validarLoteExiste**

| Regla | Mensaje de Error |
|-------|------------------|
| El lote con `codigoLote` debe existir y estar activo | "Lote no encontrado." |

**3. validarAnalisisAsociado**

| Regla | Mensaje de Error |
|-------|------------------|
| El lote debe tener un analisis asociado | "El lote no tiene Analisis asociado." |

**4. validarFechaEgresoLoteDtoPosteriorLote**

| Regla | Mensaje de Error |
|-------|------------------|
| `fechaEgreso` >= `fechaIngreso` del lote | "La fecha de egreso no puede ser anterior a la fecha de ingreso del lote" |

**5. validarCantidadesPorMedidas**

| Regla | Mensaje de Error |
|-------|------------------|
| Cantidad a muestrear <= cantidad disponible del bulto (con conversion de unidades) | "La cantidad a muestrear excede el stock disponible del bulto X" |
| La unidad seleccionada debe ser compatible con la unidad base del bulto | "Unidad de medida incompatible para el bulto X" |

---

### 6.4 RESUMEN DE VALIDACIONES POR CAMPO

| Campo | Frontend | Bean Validation | Negocio |
|-------|----------|-----------------|---------|
| loteMuestreo | required | - | Existencia verificada, Analisis asociado |
| fechaEgreso | required | @NotNull | >= fechaIngreso lote |
| cantidadesBultos[] | required, min, step | @NotEmpty | <= stock disponible (con conversion) |
| unidadMedidaBultos[] | required | - | Compatibles con unidad base |
| motivoDelCambio | required, minlength | @NotNull, @Size(min=20) | >= 20 caracteres (ANMAT) |

---

## 7. TEMPLATE (Frontend)

### 7.1 Estilo Visual

El formulario utiliza la paleta de colores **purpura** que identifica las operaciones de calidad en todo el sistema. Los elementos principales incluyen:

- **Fondo de pagina:** Purpura claro (`bg-calidad`)
- **Tarjeta del formulario:** Blanca con borde purpura y sombra suave
- **Botones primarios:** Gradiente purpura
- **Iconos de seccion:** Bootstrap Icons en color purpura

El selector de Lote utiliza **Select2** para permitir busqueda dentro de las opciones. Los inputs de cantidades por bulto se generan dinamicamente al seleccionar un lote.

### 7.2 Librerias Utilizadas

| Libreria | Proposito |
|----------|-----------|
| Bootstrap 5.3 | Framework CSS base, grid responsivo |
| Bootstrap Icons | Iconografia en todo el formulario |
| jQuery 3.7 | Manipulacion DOM y eventos |
| Select2 | Dropdown con busqueda de texto |

### 7.3 Comportamiento JavaScript

El script del template ejecuta las siguientes logicas al cargar la pagina:

1. **Inicializacion de fecha:** Establece la fecha de muestreo con la fecha actual usando timezone de Argentina.

2. **Configuracion de selector:** Convierte el select de Lote en componente Select2 con busqueda.

3. **Generacion de inputs de bultos:** Cuando el usuario selecciona un lote, se generan dinamicamente los campos de cantidad y unidad para cada bulto del lote.

4. **Carga dinamica de unidades:** Al generar cada input de bulto, se consultan las subunidades compatibles via AJAX.

5. **Cache de subunidades:** Las subunidades se cachean en memoria para evitar llamadas AJAX repetitivas.

6. **Restauracion de datos:** Si el formulario se recarga por un error de validacion, los datos previamente ingresados se restauran automaticamente.

7. **Prevencion de doble submit:** Al enviar el formulario, el boton se deshabilita y muestra un spinner con texto "Procesando..." para evitar envios duplicados. Aplica tanto al boton "Vista Previa" como al boton "Confirmar Muestreo" en la pantalla de confirmacion.

### 7.4 Interacciones Dinámicas

Este CU consume el siguiente endpoint AJAX:

| Endpoint | Uso |
|----------|-----|
| `GET /enums/subunidades?unidad=X` | Obtiene subunidades compatibles para cada bulto |

Ademas, utiliza data-attributes en el select de lotes:

| Atributo | Uso |
|----------|-----|
| `data-nroanalisis` | Numero del ultimo analisis |
| `data-dictamen` | Dictamen actual del lote |

---

## 8. CONTROLLER

### 8.1 Clase: BajaMuestreoBultoController

El controlador maneja el flujo de 3 pasos del caso de uso y extiende `AbstractCuController` para heredar servicios comunes (LoteService, ProductoService, ProveedorService).

### 8.2 Flujo de Endpoints

**GET /muestreo-multi-bulto** - Muestra el formulario vacio

Carga en el modelo los lotes elegibles para muestreo multi-bulto (lotes sin trazas activas). Inicializa el LoteDTO vacio.

**POST /muestreo-multi-bulto/confirm** - Valida y muestra vista previa

Recibe el DTO con los datos del formulario. Ejecuta Bean Validation automaticamente y luego invoca las validaciones de negocio del Service. Si hay errores, retorna al formulario con los mensajes. Si todo es valido, carga la informacion completa del lote para mostrar en la pantalla de confirmacion.

**POST /muestreo-multi-bulto** - Persiste los datos

Re-valida los datos por seguridad. Establece la fecha/hora de creacion y delega al Service para persistir el muestreo. Redirige a la pantalla de exito con el LoteDTO resultante.

### 8.3 Caracteristicas Destacadas

- **Doble validacion:** Los datos se validan tanto en /confirm como en el POST final por seguridad.
- **Flash Attributes:** El resultado se pasa a la pantalla de exito mediante RedirectAttributes para sobrevivir al redirect.
- **Filtrado de lotes:** Solo muestra lotes elegibles para muestreo multi-bulto (sin trazas activas).
- **Preservacion de cantidades:** Las cantidades muestreadas se preservan para mostrar en la pantalla de exito.

---

## 9. SERVICE

### 9.1 Clase: BajaMuestreoBultoService

El servicio actua como orquestador y delega la logica especifica a `MuestreoMultiBultoService`. Extiende `AbstractCuService` para heredar validadores comunes y repositorios.

### 9.2 Metodo Principal: bajamuestreoMultiBulto

Este metodo ejecuta toda la operacion en una unica transaccion. El flujo es:

1. **Establecer contexto de auditoria:** Registra "CU3_MUESTREO" para que Hibernate Envers lo capture en las tablas de auditoria.

2. **Obtener usuario actual:** Recupera el usuario autenticado para registrar quien realizo el muestreo.

3. **Delegar a servicio especializado:** Invoca `MuestreoMultiBultoService.procesarMuestreoMultiBulto()`.

4. **Log de completado:** Registra la finalizacion exitosa del muestreo.

5. **Retornar DTO:** Retorna el lote actualizado para mostrar en la pantalla de exito.

### 9.3 Logica de MuestreoMultiBultoService

El servicio especializado ejecuta:

1. **Cargar el Lote:** Busca el lote por codigo con sus bultos.

2. **Procesar cada bulto:** Por cada bulto indicado, reduce la cantidad actual segun lo muestreado.

3. **Marcar bultos consumidos:** Si un bulto queda con cantidad 0, cambia su estado a CONSUMIDO.

4. **Actualizar cantidad del lote:** Recalcula la cantidad total del lote sumando los bultos activos.

5. **Crear Movimiento:** Genera un movimiento de tipo BAJA con motivo MUESTREO.

6. **Crear Detalles:** Por cada bulto muestreado, crea un DetalleMovimiento.

7. **Persistir y retornar:** Guarda todas las entidades y retorna el DTO actualizado.

---

## 10. PERSISTENCIA

### 10.1 Entidades Afectadas

| Entidad | Accion | Estado Final |
|---------|--------|--------------|
| Lote | UPDATE | cantidadActual reducida |
| Bulto (multiples) | UPDATE | cantidadActual reducida, estado=CONSUMIDO si cantidad=0 |
| Movimiento | INSERT | tipo=BAJA, motivo=MUESTREO |
| DetalleMovimiento (1 por bulto) | INSERT | registra cantidad muestreada por bulto |

### 10.2 Movimiento Generado

El movimiento de baja incluye:

- **tipo:** BAJA
- **motivo:** MUESTREO
- **cantidad:** Total muestreado (suma convertida)
- **motivoDelCambio:** Texto ingresado por el usuario (ANMAT)

### 10.3 Cascade y Soft Delete

Los DetalleMovimiento se guardan automaticamente al guardar el Movimiento (cascade PERSIST).

Todas las entidades usan borrado logico: en lugar de DELETE fisico, se ejecuta UPDATE activo=false.

---

## 11. AUDITORIA (ANMAT)

El sistema cumple con los requerimientos de la Disposicion ANMAT 4159:

- **Hibernate Envers:** Todas las entidades (Lote, Bulto, Movimiento) estan auditadas. Cada cambio genera un registro en las tablas `_aud` con usuario, timestamp y caso de uso.

- **Campo motivoDelCambio:** El movimiento incluye el motivo ingresado por el usuario (mínimo 20 caracteres). Este campo es obligatorio para cumplir con el Requerimiento 9 del Anexo 6.

- **Contexto de caso de uso:** El identificador "CU3_MUESTREO" se guarda en cada revision de auditoria.

- **Trazabilidad de stock:** Se registra la reduccion de cada bulto individual para trazabilidad completa.

---

## 12. SEGURIDAD

- **CSRF:** El formulario incluye token CSRF como campo oculto. Las llamadas AJAX incluyen el token en headers.
- **Autorizacion:** Solo usuarios con rol ADMIN o ANALISTA_CONTROL_CALIDAD pueden acceder.
- **XSS:** Todos los datos se renderizan con escape automatico de HTML (th:text).

---

## 13. MANEJO DE ERRORES

Los errores se manejan en diferentes niveles:

- **Service:** Si el lote no existe o las cantidades exceden el stock, se rechaza el campo con mensaje descriptivo.
- **Controller:** Los errores de validacion (Bean Validation y negocio) se muestran junto a cada campo en el formulario.
- **Excepciones no controladas:** Redirigen a la pagina de error global del sistema.

---

## 14. LOGGING

El service registra eventos en los siguientes puntos:

- **Inicio:** Usuario y codigo de lote
- **Completado:** Codigo de lote muestreado
- **Errores:** Tipo de validacion que fallo

Todos los logs usan el prefijo `[CU3]` para facilitar la busqueda.

---

## 15. MEJORAS SUGERIDAS

| Prioridad | Mejora |
|-----------|--------|
| MEDIA | Validar cantidades vs stock en JavaScript antes de enviar |
| BAJA | Fallback local si los CDN no responden |

---

## 16. MODALIDAD MUESTREO TRAZABLE

### 16.1 Descripcion

Permite registrar el consumo de muestras de productos trazados (UNIDAD_VENTA) seleccionando **trazas individuales**. Cada traza seleccionada cambia su estado a CONSUMIDO, manteniendo la trazabilidad individual de cada unidad muestreada.

**URL Base:** `/calidad/baja/muestreo-trazable`

### 16.2 Roles Autorizados

| Rol | Nivel | Acceso |
|-----|-------|--------|
| ADMIN | 6 | Permitido |
| ANALISTA_CONTROL_CALIDAD | 2 | Permitido |
| Otros | - | No permitido |

**Anotacion:** `@PreAuthorize("hasAnyAuthority('ROLE_ADMIN', 'ROLE_ANALISTA_CONTROL_CALIDAD')")`

### 16.3 Pre-requisitos del Lote

| Condicion | Descripcion |
|-----------|-------------|
| Activo | `activo = true` |
| Tipo Producto | `UNIDAD_VENTA` (productos trazados) |
| Unidad de Medida | `UNIDAD` (obligatorio para trazas) |
| Con Analisis | Debe tener analisis asociado |
| Con Trazas | Debe tener trazas en estado DISPONIBLE |

### 16.4 Archivos Involucrados

#### Backend
| Archivo | Descripcion |
|---------|-------------|
| `controller/cu/BajaMuestreoBultoController.java` | Controlador compartido |
| `service/cu/MuestreoTrazableService.java` | Servicio especializado (285 lineas) |
| `service/cu/AbstractCuService.java` | Clase base con validaciones comunes |

### 16.5 Campos del Formulario

| Campo | HTML ID | Tipo | Validacion Server |
|-------|---------|------|-------------------|
| Lote | `codigoLote` | select (Select2) | Existencia verificada |
| Bulto | `nroBulto` | select | Existencia verificada |
| Nro Analisis | `nroAnalisis` | hidden | Debe coincidir con analisis en curso |
| Trazas | `trazaDTOs` | checkbox[] | Al menos una seleccionada |
| Fecha de Muestreo | `fechaMovimiento` | date | `@NotNull`, `@PastOrPresent` |
| Motivo del Cambio | `motivoDelCambio` | textarea | `@NotNull`, `@Size(min=20)` |

### 16.6 Validaciones de Negocio

**Ubicacion:** `MuestreoTrazableService.validarMuestreoTrazableInput()`

| # | Validacion | Mensaje de Error |
|---|-----------|------------------|
| 1 | BindingResult previo | Errores de Bean Validation |
| 2 | validarMotivoDelCambio | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 3 | validarNroAnalisisNotNull | "Ingrese un nro de analisis" |
| 4 | Lote existe | "Lote no encontrado." |
| 5 | Producto es UNIDAD_VENTA | (filtrado en query) |
| 6 | Trazas seleccionadas | "Debe seleccionar al menos una unidad a muestrear." |
| 7 | Fecha >= fechaIngreso lote | "Fecha anterior a ingreso del lote" |
| 8 | Bulto existe | "Bulto no encontrado." |
| 9 | Cantidades validas | Validacion de conversion |

### 16.7 Validaciones Especiales de Trazas

| Validacion | Mensaje de Error |
|------------|------------------|
| Unidad debe ser UNIDAD | "La traza solo es aplicable a UNIDADES" |
| Cantidad debe ser entera | "La cantidad de Unidades debe ser entero" |
| Nro Analisis coincide | "El número de análisis no coincide con el análisis en curso" |

### 16.8 Flujo del Servicio

El metodo `procesarMuestreoTrazable()` ejecuta:

1. **Log de inicio:** `[CU3-TRAZABLE] Iniciando muestreo trazable`
2. **Cargar Lote:** Busca el lote por codigo
3. **Validar Analisis:** Verifica que nroAnalisis coincida con el analisis en curso
4. **Crear Movimiento:** BAJA/MUESTREO asociado al analisis
5. **Descontar Bulto:** Resta cantidad del bulto seleccionado
6. **Descontar Lote:** Resta cantidad del lote
7. **Procesar Trazas:** Marca cada traza seleccionada como CONSUMIDO
8. **Actualizar Estados:** Bulto y lote a EN_USO o CONSUMIDO
9. **Persistir:** Guarda lote con movimiento
10. **Log de completado:** `[CU3-TRAZABLE] Muestreo trazable completado`

### 16.9 Entidades Afectadas

| Entidad | Accion | Estado Final |
|---------|--------|--------------|
| Lote | UPDATE | cantidadActual reducida |
| Bulto | UPDATE | cantidadActual reducida, estado=EN_USO o CONSUMIDO |
| Traza (multiples) | UPDATE | estado=CONSUMIDO |
| Movimiento | INSERT | tipo=BAJA, motivo=MUESTREO |
| DetalleMovimiento | INSERT | con trazas asociadas |

### 16.10 Movimiento Generado

| Campo | Valor |
|-------|-------|
| tipo | BAJA |
| motivo | MUESTREO |
| cantidad | Numero de trazas seleccionadas |
| unidadMedida | UNIDAD |
| analisis | Analisis en curso del lote |
| motivoDelCambio | `[CU3]` + texto ingresado |

### 16.11 Diferencias con Multi-Bulto

| Aspecto | Multi-Bulto | Trazable |
|---------|-------------|----------|
| Tipo producto | Cualquiera excepto UV | Solo UNIDAD_VENTA |
| Seleccion | Cantidades por bulto | Trazas individuales |
| Unidad | Cualquiera compatible | Solo UNIDAD |
| Multi-bulto | SI (varios bultos) | NO (un bulto) |
| Trazabilidad | Por cantidad | Por unidad individual |
| Service | MuestreoMultiBultoService | MuestreoTrazableService |

### 16.12 Logging

| Evento | Nivel | Formato |
|--------|-------|---------|
| Inicio | INFO | `[CU3-TRAZABLE] Iniciando muestreo trazable - Usuario: {}, Lote: {}, Análisis: {}` |
| Completado | INFO | `[CU3-TRAZABLE] Muestreo trazable completado - Lote: {}, CantidadActual: {}, Estado: {}, MotivoDelCambio: {}` |

---

*Documento generado: 2025-12-14*
*Version: 3.3*
