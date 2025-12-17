# CU7 - BAJA CONSUMO PRODUCCION

**Caso de Uso:** Consumo de Materiales por Produccion
**URL Base:** `/produccion/baja/consumo-produccion`
**Tipo:** BAJA (Egreso de stock)
**Categoria:** Produccion (color verde egresos)
**Identificador Interno:** `CU7_CONSUMO_PRODUCCION`

---

## 1. DESCRIPCION

Permite registrar el consumo de materiales aprobados en el proceso de produccion interna. Descuenta stock de los bultos seleccionados y actualiza el estado del lote segun el stock restante (EN_USO si queda stock, CONSUMIDO si se agota). Genera un movimiento de tipo BAJA con motivo CONSUMO_PRODUCCION.

**Caracteristicas principales:**
- Solo permite consumir lotes con dictamen APROBADO
- Soporta consumo parcial por bulto con conversion de unidades
- Cancela automaticamente analisis en curso si el lote queda sin stock
- Registra la orden de produccion asociada al consumo

---

## 2. ROLES AUTORIZADOS

| Rol | Nivel | Acceso |
|-----|-------|--------|
| ADMIN | 6 | Permitido |
| SUPERVISOR_PLANTA | 3 | Permitido |
| Otros | - | No permitido |

**Anotacion:** `@PreAuthorize("hasAnyAuthority('ROLE_ADMIN', 'ROLE_SUPERVISOR_PLANTA')")`

---

## 3. FLUJO DE PANTALLAS

```
+---------------------------------------------------------------------+
|  1. FORMULARIO                                                       |
|     GET /produccion/baja/consumo-produccion                         |
|     Template: produccion/baja/consumo-produccion.html               |
|  +---------------------------------------------------------------+  |
|  |  Select2: Lote (nombre, nro analisis, codigo interno)         |  |
|  |  Detalles del lote (dinamico al seleccionar)                  |  |
|  |  Cantidades por bulto a consumir (inputs dinamicos)           |  |
|  |  Fecha de Consumo                                             |  |
|  |  Orden de Produccion                                          |  |
|  |  Motivo del cambio (min 20 chars)                             |  |
|  +---------------------------------------------------------------+  |
|  [Cancelar]                                        [Guardar] ->      |
+---------------------------------------------------------------------+
                              |
                              | POST /produccion/baja/consumo-produccion
                              v
+---------------------------------------------------------------------+
|  2. EXITO                                                            |
|     GET /produccion/baja/consumo-produccion-ok (redirect)           |
|     Template: produccion/baja/consumo-produccion-ok.html            |
|  +---------------------------------------------------------------+  |
|  |  Consumo Registrado Exitosamente                              |  |
|  |  Detalles del lote actualizado                                |  |
|  |  Tabla de movimientos                                         |  |
|  |  Tabla de bultos con cantidades actualizadas                  |  |
|  +---------------------------------------------------------------+  |
|  [Registrar Otro Consumo]                    [Volver al Inicio]      |
+---------------------------------------------------------------------+
```

**Nota:** Este CU usa flujo de 2 pasos (formulario -> exito) sin pantalla de confirmacion intermedia.

---

## 4. ARCHIVOS INVOLUCRADOS

### Frontend (Templates)
| Archivo | Descripcion |
|---------|-------------|
| `templates/produccion/baja/consumo-produccion.html` | Formulario principal (403 lineas) |
| `templates/produccion/baja/consumo-produccion-ok.html` | Pantalla de exito (507 lineas) |

### Backend
| Archivo | Descripcion |
|---------|-------------|
| `controller/cu/BajaConsumoProduccionController.java` | Controlador (103 lineas) |
| `service/cu/BajaConsumoProduccionService.java` | Servicio de negocio (222 lineas) |
| `service/cu/AbstractCuService.java` | Clase base con validaciones comunes |

### Validadores Especializados
| Archivo | Responsabilidad |
|---------|-----------------|
| `service/cu/validator/CantidadValidator.java` | Validaciones de cantidades por bulto |
| `service/cu/validator/FechaValidator.java` | Validaciones de fechas |

### Utilidades de Creacion de Entidades
| Archivo | Uso en CU7 |
|---------|------------|
| `utils/MovimientoBajaUtils.java` | Factory para crear Movimiento BAJA |
| `utils/UnidadMedidaUtils.java` | Conversion entre unidades de medida |

### DTOs y Entidades
| Archivo | Uso en CU7 |
|---------|------------|
| `dto/LoteDTO.java` | DTO principal del formulario |
| `entity/Lote.java` | Entidad Lote (actualiza cantidades y estado) |
| `entity/Bulto.java` | Entidad Bulto (descuenta cantidades) |
| `entity/Movimiento.java` | Entidad Movimiento |
| `entity/DetalleMovimiento.java` | Detalle por bulto consumido |

---

## 5. CAMPOS DEL FORMULARIO

### 5.1 Obligatorios

| Campo | HTML ID | Tipo | Validacion Browser | Validacion Server |
|-------|---------|------|-------------------|-------------------|
| Lote | `loteSelect` | select (Select2) | - | `@NotNull` (BajaProduccion) |
| Fecha de Consumo | `fechaEgreso` | date | `required` | `@NotNull`, `@PastOrPresent` |
| Orden de Produccion | `ordenProduccion` | text | `required` | `@NotNull` (BajaProduccion) |
| Motivo del Cambio | `motivoDelCambio` | textarea | `required`, `minlength="20"` | `@Size(min=20)` |

### 5.2 Campos Dinamicos (por bulto)

| Campo | HTML Name | Tipo | Validacion |
|-------|-----------|------|------------|
| Cantidad por bulto | `cantidadesBultos[i]` | number | `min="0"`, `step="0.01"` |
| Unidad de medida | `unidadMedidaBultos[i]` | select | Subunidades del bulto |
| Nro de bulto | `nroBultoList[i]` | hidden | ID del bulto |

### 5.3 Campos Hidden

| Campo | HTML ID | Descripcion |
|-------|---------|-------------|
| codigoLote | `codigoLote` | Codigo del lote seleccionado |

---

## 6. VALIDACIONES POR CAPA

El sistema implementa validacion en 3 capas:

### 6.1 CAPA 1: FRONTEND (Browser - HTML5 + JavaScript)

**Ubicacion:** `templates/produccion/baja/consumo-produccion.html`

| Campo | Atributo HTML5 | Comportamiento |
|-------|---------------|----------------|
| `fechaEgreso` | `required`, `type="date"` | Bloquea submit si vacio |
| `ordenProduccion` | `required` | Bloquea submit si vacio |
| `motivoDelCambio` | `required`, `minlength="20"` | Minimo 20 caracteres |
| `cantidadesBultos[i]` | `min="0"`, `step="0.01"`, `required` | No negativas, decimales permitidos |

**JavaScript Dinamico:**
- Inicializa fecha con fecha actual (timezone Argentina)
- Genera inputs de cantidad/unidad por cada bulto del lote
- Carga subunidades via AJAX (`/enums/subunidades?unidad=X`)
- Muestra stock maximo por bulto como referencia

---

### 6.2 CAPA 2: BEAN VALIDATION (Spring - Grupo BajaProduccion)

**Ubicacion:** `dto/LoteDTO.java`

Se ejecuta con el grupo de validacion `@Validated(BajaProduccion.class)`.

| Campo | Anotaciones | Mensaje de Error |
|-------|-------------|------------------|
| codigoLote | `@NotNull(groups=BajaProduccion)` | "Debe seleccionar un lote" |
| fechaEgreso | `@NotNull`, `@PastOrPresent` | "La fecha de consumo es obligatoria" / "no puede ser futura" |
| ordenProduccion | `@NotNull(groups=BajaProduccion)` | "La orden de produccion es obligatoria" |

---

### 6.3 CAPA 3: VALIDACION DE NEGOCIO (Service Layer)

**Ubicacion:** `BajaConsumoProduccionService.java`

El metodo `validarConsumoProduccionInput()` ejecuta las siguientes validaciones en orden:

| # | Validador | Metodo/Clase |
|---|-----------|--------------|
| 1 | validarMotivoDelCambio | AbstractCuService |
| 2 | validarLoteExiste | Service (inline) |
| 3 | validarDictamenAprobado | Service (inline) |
| 4 | validarFechaEgresoLoteDtoPosteriorLote | FechaValidator |
| 5 | validarCantidadesPorMedidas | CantidadValidator |

#### Detalle de Validaciones

**1. validarMotivoDelCambio** (ANMAT Disp. 4159, Anexo 6, Req. 9)

| Regla | Mensaje de Error |
|-------|------------------|
| `motivoDelCambio` no nulo y >= 20 caracteres | "El campo motivo del cambio es obligatorio (mínimo 20 caracteres)" |

**2. validarLoteExiste**

| Regla | Mensaje de Error |
|-------|------------------|
| El lote con `codigoLote` debe existir y estar activo | "Lote no encontrado." |

**3. validarDictamenAprobado**

| Regla | Mensaje de Error |
|-------|------------------|
| El lote debe tener dictamen APROBADO | "Solo se puede consumir un lote con dictamen APROBADO. Dictamen actual: X" |

**4. validarFechaEgresoLoteDtoPosteriorLote**

| Regla | Mensaje de Error |
|-------|------------------|
| `fechaEgreso` >= `fechaIngreso` del lote | "La fecha del movimiento no puede ser anterior a la fecha de ingreso del lote" |

**5. validarCantidadesPorMedidas**

| Regla | Mensaje de Error |
|-------|------------------|
| Lista de cantidades no vacia | "Debe ingresar las cantidades a consumir" |
| Lista de unidades no vacia | "Debe ingresar las unidades de medida" |
| Cantidad no nula | "La cantidad no puede ser nula" |
| Cantidad no negativa | "La cantidad no puede ser negativa" |
| Unidad de medida no nula | "Debe indicar la unidad" |
| Cantidad convertida <= stock del bulto | "La cantidad ingresada (X UNIDAD) no puede superar el stock actual del bulto N (Y UNIDAD)" |

---

### 6.4 RESUMEN DE VALIDACIONES POR CAMPO

| Campo | Frontend | Bean Validation | Negocio |
|-------|----------|-----------------|---------|
| loteSelect | - | @NotNull | Existencia, Dictamen APROBADO |
| fechaEgreso | required | @NotNull, @PastOrPresent | >= fechaIngreso lote |
| ordenProduccion | required | @NotNull | - |
| motivoDelCambio | required, minlength | - | >= 20 caracteres (ANMAT) |
| cantidadesBultos | min=0, required | - | No excede stock, conversiones |

---

## 7. TEMPLATE (Frontend)

### 7.1 Estilo Visual

El formulario utiliza la paleta de colores **verde egresos** que identifica las operaciones de baja/salida en todo el sistema:

- **Fondo de pagina:** Verde claro (`bg-egresos`)
- **Tarjeta del formulario:** Blanca con borde verde y sombra suave
- **Botones primarios:** Gradiente verde (`btn-egresos`)
- **Iconos de seccion:** Bootstrap Icons en color verde
- **Badge CU:** `CU7` en verde
- **Tarjetas de bulto:** Numero circular en gradiente verde

### 7.2 Librerias Utilizadas

| Libreria | Proposito |
|----------|-----------|
| Bootstrap 5.3 | Framework CSS base, grid responsivo |
| Bootstrap Icons | Iconografia en todo el formulario |
| jQuery 3.7 | Manipulacion DOM y eventos |
| Select2 | Dropdown con busqueda de lotes |

### 7.3 Comportamiento JavaScript

1. **Inicializacion de fecha:** Establece la fecha de egreso con la fecha actual usando timezone de Argentina.

2. **Configuracion de selector:** Convierte el select de Lote en componente Select2 con busqueda.

3. **Carga de detalle del lote:** Al seleccionar un lote, muestra informacion del producto, stock, analisis y fechas.

4. **Generacion dinamica de bultos:** Crea un input de cantidad y selector de unidad por cada bulto del lote seleccionado.

5. **Carga de subunidades:** Consulta `/enums/subunidades?unidad=X` para cargar las unidades compatibles con cada bulto.

6. **Restauracion de datos:** Si el formulario se recarga por error, restaura lote seleccionado, cantidades y unidades previas.

7. **Prevencion de doble submit:** Al enviar el formulario, el boton se deshabilita y muestra spinner "Procesando...".

### 7.4 Interacciones Dinamicas

| Endpoint | Uso |
|----------|-----|
| `GET /enums/subunidades?unidad=X` | Obtiene subunidades compatibles (ej: KILOGRAMO -> [KILOGRAMO, GRAMO]) |

---

## 8. CONTROLLER

### 8.1 Clase: BajaConsumoProduccionController

El controlador maneja el flujo de 2 pasos (formulario -> exito) y extiende `AbstractCuController` para heredar servicios comunes.

### 8.2 Flujo de Endpoints

**GET /consumo-produccion** - Muestra el formulario vacio

Carga en el modelo:
- Lotes elegibles para consumo (`loteService.findAllForConsumoProduccionDTOs()`)
- LoteDTO vacio para binding

**POST /consumo-produccion** - Valida y persiste consumo

Recibe el DTO con grupo de validacion `BajaProduccion`. Ejecuta Bean Validation y luego validaciones de negocio del Service. Si hay errores, retorna al formulario. Si todo es valido, persiste el consumo y redirige a exito.

**GET /consumo-produccion-ok** - Muestra resultado exitoso

Recibe el LoteDTO via FlashAttributes y muestra los detalles del lote actualizado incluyendo tablas de movimientos y bultos.

**GET /cancelar** - Cancela operacion

Redirige al home.

### 8.3 Caracteristicas Destacadas

- **Flujo de 2 pasos:** Sin pantalla de confirmacion intermedia.
- **Consumo multi-bulto:** Permite especificar cantidades diferentes por cada bulto.
- **Conversion de unidades:** Soporta consumir en unidades diferentes a las del bulto.
- **Flash Attributes:** El resultado se pasa a la pantalla de exito via RedirectAttributes.

---

## 9. SERVICE

### 9.1 Clase: BajaConsumoProduccionService

El servicio contiene la logica de negocio del caso de uso. Extiende `AbstractCuService` para heredar validadores comunes y repositorios.

### 9.2 Metodo Principal: bajaConsumoProduccion

Este metodo ejecuta toda la operacion en una unica transaccion. El flujo es:

1. **Establecer contexto de auditoria:** Registra "CU7_CONSUMO_PRODUCCION" para Hibernate Envers.

2. **Obtener usuario actual:** Recupera el usuario autenticado.

3. **Cargar el Lote:** Obtiene el lote por codigoLote.

4. **Procesar cada bulto:** Para cada bulto en la lista:
   - Obtiene la cantidad a consumir y la unidad de medida
   - Convierte la cantidad si la unidad difiere de la del bulto
   - Descuenta la cantidad del bulto y del lote
   - Actualiza el estado del bulto (EN_USO o CONSUMIDO)

5. **Actualizar estado del lote:**
   - Si `cantidadActual == 0`: estado = CONSUMIDO
   - Si `cantidadActual > 0`: estado = EN_USO

6. **Cancelar analisis en curso:** Si el lote queda sin stock y tiene un analisis sin dictamen, lo marca como CANCELADO.

7. **Crear Movimiento:** Genera movimiento BAJA/CONSUMO_PRODUCCION con detalles por bulto.

8. **Persistir y retornar:** Guarda el lote y retorna el DTO.

### 9.3 Metodo: persistirMovimientoBajaConsumoProduccion

Crea el movimiento de baja con los siguientes pasos:

1. Calcula la unidad de medida del movimiento (la mayor de todas las usadas)
2. Suma las cantidades convertidas a esa unidad
3. Crea un DetalleMovimiento por cada bulto consumido
4. Persiste el movimiento

### 9.4 Pre-requisitos del Lote

| Condicion | Descripcion |
|-----------|-------------|
| Activo | El lote debe estar activo (no eliminado) |
| Dictamen APROBADO | Solo lotes aprobados pueden consumirse |
| Tipo de producto | No debe ser UNIDAD_VENTA (solo materias primas y semielaborados) |
| Con stock | Debe tener al menos un bulto con cantidad actual > 0 |

---

## 10. PERSISTENCIA

### 10.1 Entidades Afectadas

| Entidad | Accion | Campos Modificados |
|---------|--------|-------------------|
| Lote | UPDATE | cantidadActual, estado |
| Bulto | UPDATE | cantidadActual, estado (por cada bulto consumido) |
| Analisis | UPDATE | dictamen=CANCELADO (si lote queda sin stock) |
| Movimiento | INSERT | tipo=BAJA, motivo=CONSUMO_PRODUCCION |
| DetalleMovimiento | INSERT | uno por cada bulto consumido |

### 10.2 Movimiento Generado

El movimiento de baja incluye:

- **tipo:** BAJA
- **motivo:** CONSUMO_PRODUCCION
- **fecha:** fechaEgreso
- **cantidad:** Suma total consumida (convertida a la mayor unidad)
- **unidadMedida:** La mayor unidad usada en el consumo
- **ordenProduccion:** Numero de orden de produccion
- **motivoDelCambio:** Motivo ingresado por el usuario (con prefijo [CU7])
- **detalles:** Lista de DetalleMovimiento con cantidad/unidad por bulto

### 10.3 Estados Finales

| Escenario | Estado Bulto | Estado Lote |
|-----------|--------------|-------------|
| Consumo parcial (queda stock) | EN_USO | EN_USO |
| Consumo total del bulto | CONSUMIDO | EN_USO (si otros bultos tienen stock) |
| Consumo total del lote | CONSUMIDO | CONSUMIDO |

### 10.4 Cascade y Soft Delete

El Movimiento se guarda explicitamente con sus DetalleMovimiento (cascade). Todas las entidades usan borrado logico.

---

## 11. AUDITORIA (ANMAT)

El sistema cumple con los requerimientos de la Disposicion ANMAT 4159:

- **Hibernate Envers:** Todas las entidades (Lote, Bulto, Movimiento) estan auditadas. Cada cambio genera un registro en las tablas `_aud`.

- **Campo motivoDelCambio:** El movimiento incluye el motivo ingresado por el usuario (mínimo 20 caracteres). Cumple con Requerimiento 9 del Anexo 6.

- **Contexto de caso de uso:** El identificador "CU7_CONSUMO_PRODUCCION" se guarda en cada revision de auditoria.

- **Orden de produccion:** Se registra el numero de orden de produccion para trazabilidad.

- **Detalle por bulto:** Cada DetalleMovimiento registra la cantidad exacta consumida de cada bulto.

---

## 12. SEGURIDAD

- **CSRF:** El formulario incluye token CSRF como campo oculto. Las llamadas AJAX incluyen el token en headers via meta tags.
- **Autorizacion:** Solo usuarios con rol ADMIN o SUPERVISOR_PLANTA pueden acceder.
- **XSS:** Todos los datos se renderizan con escape automatico de HTML (th:text).
- **Validacion de dictamen:** Impide consumir lotes no aprobados.

---

## 13. MANEJO DE ERRORES

Los errores se manejan en diferentes niveles:

- **Service:** Si el lote no existe, no esta aprobado, o las cantidades exceden el stock, se rechaza con mensaje descriptivo.
- **Controller:** Los errores de validacion se muestran junto a cada campo en el formulario.
- **Excepciones no controladas:** Redirigen a la pagina de error global del sistema.

---

## 14. LOGGING

El service registra eventos en los siguientes puntos:

- **Inicio:** Usuario y codigo de lote
- **Completado:** Lote, cantidad restante, estado final y motivo del cambio

Todos los logs usan el prefijo `[CU7]` para facilitar la busqueda.

---

## 15. MEJORAS SUGERIDAS

| Prioridad | Mejora |
|-----------|--------|
| MEDIA | Agregar pantalla de confirmacion para consumos grandes (>50% del stock) |
| BAJA | Mostrar alerta si el lote tiene fecha de reanalisis proxima |

---

*Documento generado: 2025-12-13*
*Version: 1.1*
