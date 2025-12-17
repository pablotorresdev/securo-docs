# CU1 - ALTA INGRESO COMPRA

**Caso de Uso:** Ingreso de Stock por Compra
**URL Base:** `/compras/alta/ingreso-compra`
**Tipo:** ALTA (Ingreso)
**Categoria:** Ingresos (color celeste)
**Identificador Interno:** `CU1_INGRESO_COMPRA`

---

## 1. DESCRIPCION

Permite registrar el ingreso de mercaderia al sistema mediante compra a proveedor externo. Crea un nuevo lote con sus bultos asociados y registra el movimiento de alta correspondiente.

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
|     GET /compras/alta/ingreso-compra                                |
|     Template: compras/alta/ingreso-compra.html                      |
|  +---------------------------------------------------------------+  |
|  |  Select2: Proveedor, Producto, Fabricante                     |  |
|  |  Filtro por Tipo de Producto                                  |  |
|  |  Cantidad total + Unidad de medida                            |  |
|  |  Bultos (1-15) con cantidades dinamicas                       |  |
|  |  Fechas: ingreso, reanalisis, vencimiento                     |  |
|  |  Motivo del cambio (min 20 chars)                             |  |
|  +---------------------------------------------------------------+  |
|  [Cancelar]                    [Vista Previa y Confirmar] ->        |
+---------------------------------------------------------------------+
                              |
                              | POST /compras/alta/ingreso-compra/confirm
                              v
+---------------------------------------------------------------------+
|  2. CONFIRMACION                                                     |
|     Template: compras/alta/ingreso-compra-confirm.html              |
|  +---------------------------------------------------------------+  |
|  |  Preview: Todos los datos ingresados                          |  |
|  |  Producto, Proveedor, Cantidades, Bultos                      |  |
|  |  Fechas, Conservacion, Motivo del cambio                      |  |
|  +---------------------------------------------------------------+  |
|  [Volver a Editar]                        [Confirmar Ingreso] ->    |
+---------------------------------------------------------------------+
                              |
                              | POST /compras/alta/ingreso-compra
                              v
+---------------------------------------------------------------------+
|  3. EXITO                                                            |
|     GET /compras/alta/ingreso-compra-ok (redirect)                  |
|     Template: compras/alta/ingreso-compra-ok.html                   |
|  +---------------------------------------------------------------+  |
|  |  Ingreso Exitoso - CU1                                        |  |
|  |  Codigo de lote generado                                      |  |
|  |  Detalle completo del lote creado                             |  |
|  |  Tabla de bultos                                              |  |
|  |  Tabla de movimientos                                         |  |
|  +---------------------------------------------------------------+  |
|  [Nuevo Ingreso]                          [Volver al Inicio]        |
+---------------------------------------------------------------------+
```

---

## 4. ARCHIVOS INVOLUCRADOS

### Frontend (Templates)
| Archivo | Descripcion |
|---------|-------------|
| `templates/compras/alta/ingreso-compra.html` | Formulario principal (547 lineas) |
| `templates/compras/alta/ingreso-compra-confirm.html` | Vista previa/confirmacion |
| `templates/compras/alta/ingreso-compra-ok.html` | Pantalla de exito |

### Backend
| Archivo | Descripcion |
|---------|-------------|
| `controller/cu/AltaIngresoCompraController.java` | Controlador (170 lineas) |
| `service/cu/AltaIngresoCompraService.java` | Servicio de negocio (146 lineas) |
| `service/cu/AbstractCuService.java` | Clase base con validaciones comunes |

### Validadores Especializados
| Archivo | Responsabilidad |
|---------|-----------------|
| `service/cu/validator/CantidadValidator.java` | Validaciones de cantidades y bultos |
| `service/cu/validator/FechaValidator.java` | Validaciones de fechas |

### Utilidades de Creacion de Entidades
| Archivo | Uso en CU1 |
|---------|------------|
| `utils/LoteEntityUtils.java` | Factory para crear entidad Lote |
| `utils/MovimientoAltaUtils.java` | Factory para crear Movimiento ALTA |

### DTOs y Entidades
| Archivo | Uso en CU1 |
|---------|------------|
| `dto/LoteDTO.java` | DTO principal del formulario |
| `dto/validation/AltaCompra.java` | Grupo de validacion Bean Validation |
| `entity/Lote.java` | Entidad Lote |
| `entity/Bulto.java` | Entidad Bulto |
| `entity/Movimiento.java` | Entidad Movimiento |
| `entity/DetalleMovimiento.java` | Detalle de movimiento por bulto |

---

## 5. CAMPOS DEL FORMULARIO

### 5.1 Obligatorios

| Campo | HTML ID | Tipo | Validacion Browser | Validacion Server |
|-------|---------|------|-------------------|-------------------|
| Fecha de Ingreso | `fechaIngreso` | date | `required` | `@NotNull`, `@PastOrPresent` |
| Proveedor | `proveedorId` | select (Select2) | `required` | `@NotNull` |
| Producto | `productoId` | select (Select2) | `required` | `@NotNull` |
| Lote Proveedor | `loteProveedor` | text | `required` | `@NotNull` |
| Cantidad Total | `cantidadInicial` | number | `required`, `step="0.0001"` | `@NotNull`, `@Positive` |
| Unidad de Medida | `unidadMedida` | select | `required` | `@NotNull` |
| Cantidad de Bultos | `bultosTotales` | number | `required`, `min="1"`, `max="15"` | `@NotNull`, `@Positive` |
| Motivo del Cambio | `motivoDelCambio` | textarea | `required`, `minlength="20"` | Service: min 20 chars |

### 5.2 Opcionales

| Campo | HTML ID | Tipo | Validacion |
|-------|---------|------|------------|
| Numero de Remito | `nroRemito` | text | `maxlength="30"` / `@Size(max=30)` |
| Fabricante | `fabricanteId` | select (Select2) | - |
| Pais de Origen | `paisOrigen` | select | - |
| Fecha Reanalisis Prov. | `fechaReanalisisProveedor` | date | `@Future` |
| Fecha Vencimiento Prov. | `fechaVencimientoProveedor` | date | `@Future` |
| Detalle Conservacion | `detalleConservacion` | textarea | - |

### 5.3 Dinamicos (si bultosTotales > 1)

| Campo | Nombre Form | Tipo |
|-------|-------------|------|
| Cantidad por bulto N | `cantidadesBultos[N]` | `List<BigDecimal>` |
| Unidad por bulto N | `unidadMedidaBultos[N]` | `List<UnidadMedidaEnum>` |

---

## 6. VALIDACIONES POR CAPA

El sistema implementa validacion en 3 capas, cada una con responsabilidades especificas:

### 6.1 CAPA 1: FRONTEND (Browser - HTML5 + JavaScript)

**Ubicacion:** `templates/compras/alta/ingreso-compra.html`

**Tipo:** Validacion inmediata en el navegador antes del submit.

| Campo | Atributo HTML5 | Comportamiento |
|-------|---------------|----------------|
| `fechaIngreso` | `required`, `type="date"` | Bloquea submit si vacio |
| `proveedorId` | `required` | Bloquea submit si no seleccionado |
| `productoId` | `required` | Bloquea submit si no seleccionado |
| `loteProveedor` | `required` | Bloquea submit si vacio |
| `cantidadInicial` | `required`, `step="0.0001"` | Valida formato numerico |
| `unidadMedida` | `required` | Bloquea submit si no seleccionado |
| `bultosTotales` | `required`, `min="1"`, `max="15"` | Rango 1-15 |
| `motivoDelCambio` | `required`, `minlength="20"` | Minimo 20 caracteres |
| `nroRemito` | `maxlength="30"` | Maximo 30 caracteres |

**JavaScript Dinamico:**
- Filtra productos por tipo de producto seleccionado
- Carga unidades de medida compatibles via AJAX (`/enums/unidades-compatibles`)
- Genera campos dinamicos de bultos cuando `bultosTotales > 1`
- Inicializa fecha de ingreso con fecha actual (timezone Argentina)

**Limitaciones:** No valida suma de bultos vs cantidad total (delegado al servidor).

---

### 6.2 CAPA 2: BEAN VALIDATION (Spring - Anotaciones Jakarta)

**Ubicacion:** `dto/LoteDTO.java` | **Grupo:** `AltaCompra.class`

Se ejecuta automaticamente al recibir el DTO en el controller mediante `@Validated(AltaCompra.class)`.

#### Campos Obligatorios

| Campo | Anotaciones | Mensaje de Error |
|-------|-------------|------------------|
| fechaIngreso | `@NotNull`, `@PastOrPresent` | "La fecha de ingreso es obligatoria" / "no puede ser futura" |
| productoId | `@NotNull` | "El producto es obligatorio" |
| proveedorId | `@NotNull` | "El proveedor es obligatorio" |
| cantidadInicial | `@NotNull`, `@Positive` | "La cantidad inicial es obligatoria" / "debe ser mayor a cero" |
| unidadMedida | `@NotNull` | "La unidad de medida es obligatoria" |
| bultosTotales | `@NotNull`, `@Positive` | "La cantidad de bultos totales es obligatoria" |
| loteProveedor | `@NotNull` | "El lote del proveedor es obligatorio" |

#### Campos Opcionales con Restricciones

| Campo | Anotaciones | Mensaje de Error |
|-------|-------------|------------------|
| nroRemito | `@Size(max=30)` | "El numero de remito no debe superar 30 caracteres" |
| fechaReanalisisProveedor | `@Future` | "La fecha de reanalisis del proveedor debe ser futura" |
| fechaVencimientoProveedor | `@Future` | "La fecha de vencimiento del proveedor debe ser futura" |

---

### 6.3 CAPA 3: VALIDACION DE NEGOCIO (Service Layer)

**Ubicacion:** `AltaIngresoCompraService.java` + validadores en `service/cu/validator/`

Validaciones de reglas de negocio complejas que no pueden expresarse con anotaciones.

#### Secuencia de Validacion

El metodo `validarIngresoCompraInput()` ejecuta las siguientes validaciones en orden:

| # | Validador              | Clase |
|---|------------------------|-------|
| 1 | validarMotivoDelCambio | AbstractCuService |
| 2 | validarCantidadIngreso | CantidadValidator |
| 3 | validarFechasProveedor | FechaValidator |
| 4 | validarBultos          | CantidadValidator |

#### Detalle de Validaciones

**1. validarMotivoDelCambio** (ANMAT Disp. 4159, Anexo 6, Req. 9)

| Regla                                        | Mensaje de Error |
|----------------------------------------------|------------------|
| `motivoDelCambio` no nulo y >= 20 caracteres | "El campo motivo del cambio/observaciones es obligatorio (mínimo 20 caracteres)" |

**2. validarCantidadIngreso**

| Regla | Mensaje de Error |
|-------|------------------|
| `cantidadInicial` no puede ser nula | "La cantidad no puede ser nula" |
| Si `unidadMedida = UNIDAD`: cantidad debe ser entera | "La cantidad debe ser un numero entero positivo cuando la unidad es UNIDAD" |
| Si `unidadMedida = UNIDAD`: cantidad >= bultosTotales | "La cantidad de Unidades (X) no puede ser menor a la cantidad de Bultos totales: Y" |

**3. validarFechasProveedor**

| Regla | Mensaje de Error |
|-------|------------------|
| Si ambas fechas presentes: `fechaReanalisis <= fechaVencimiento` | "La fecha de reanalisis no puede ser posterior a la fecha de vencimiento" |

**4. validarBultos** (solo si bultosTotales > 1)

| Regla | Mensaje de Error |
|-------|------------------|
| Lista `cantidadesBultos` no vacia | "La cantidad del Lote no puede ser nula" |
| Lista `unidadMedidaBultos` no vacia | "Las unidades de medida del Lote no pueden ser nulas" |
| Cantidad de cada bulto no nula | "La cantidad del Bulto X no puede ser nula" |
| Si unidad del bulto = UNIDAD: cantidad entera | "La cantidad del Bulto X debe ser un numero entero positivo cuando la unidad es UNIDAD" |
| Cantidad de cada bulto > 0 | "La cantidad del Bulto X debe ser mayor a 0" |
| Suma convertida de bultos == cantidadInicial | "La suma de las cantidades individuales (X) no coincide con la cantidad total (Y)" |

---

### 6.4 RESUMEN DE VALIDACIONES POR CAMPO

| Campo | Frontend | Bean Validation | Negocio |
|-------|----------|-----------------|---------|
| fechaIngreso | required | @NotNull, @PastOrPresent | - |
| proveedorId | required | @NotNull | Existencia verificada en altaStockPorCompra |
| productoId | required | @NotNull | Existencia verificada en altaStockPorCompra |
| loteProveedor | required | @NotNull | - |
| cantidadInicial | required, step | @NotNull, @Positive | Entero si UNIDAD, >= bultos |
| unidadMedida | required | @NotNull | - |
| bultosTotales | required, min/max | @NotNull, @Positive | - |
| motivoDelCambio | required, minlength | - | >= 20 caracteres (ANMAT) |
| nroRemito | maxlength | @Size(max=30) | - |
| fechaReanalisisProveedor | - | @Future | <= fechaVencimiento |
| fechaVencimientoProveedor | - | @Future | >= fechaReanalisis |
| cantidadesBultos[] | required (JS) | - | Suma == cantidadInicial |
| unidadMedidaBultos[] | required (JS) | - | Compatibles con unidadMedida |

---

## 7. TEMPLATE (Frontend)

### 7.1 Estilo Visual

El formulario utiliza la paleta de colores **celeste** que identifica las operaciones de ingreso en todo el sistema. Los elementos principales incluyen:

- **Fondo de pagina:** Celeste claro (`bg-ingresos`)
- **Tarjeta del formulario:** Blanca con borde celeste y sombra suave
- **Botones primarios:** Gradiente celeste
- **Iconos de seccion:** Bootstrap Icons en color celeste

Los selectores de Proveedor, Producto y Fabricante utilizan **Select2** para permitir busqueda dentro de las opciones. Los campos de bultos se muestran en tarjetas individuales con efecto hover (borde celeste y sombra al pasar el mouse).

### 7.2 Librerias Utilizadas

| Libreria | Proposito |
|----------|-----------|
| Bootstrap 5.3 | Framework CSS base, grid responsivo |
| Bootstrap Icons | Iconografia en todo el formulario |
| jQuery 3.7 | Manipulacion DOM y eventos |
| Select2 | Dropdowns con busqueda de texto |

### 7.3 Comportamiento JavaScript

El script del template ejecuta las siguientes logicas al cargar la pagina:

1. **Inicializacion de fecha:** Establece la fecha de ingreso con la fecha actual usando timezone de Argentina.

2. **Configuracion de selectores:** Convierte los selects de Proveedor, Fabricante y Producto en componentes Select2 con busqueda.

3. **Filtrado de productos:** Cuando el usuario selecciona un tipo de producto, el selector de Producto se actualiza para mostrar solo los productos de ese tipo.

4. **Carga dinamica de unidades:** Al seleccionar un producto, el sistema consulta al servidor las unidades de medida compatibles (ej: si el producto usa KILOGRAMO, ofrece KILOGRAMO y GRAMO).

5. **Generacion de campos de bultos:** Cuando el usuario indica mas de un bulto, se generan dinamicamente los campos de cantidad y unidad para cada bulto individual.

6. **Restauracion de datos:** Si el formulario se recarga por un error de validacion, los datos previamente ingresados se restauran automaticamente.

7. **Prevencion de doble submit:** Al enviar el formulario, el boton se deshabilita y muestra un spinner con texto "Procesando..." para evitar envios duplicados. Aplica tanto al boton "Vista Previa" como al boton "Confirmar Ingreso" en la pantalla de confirmacion.

### 7.4 Interacciones Dinámicas

Este CU consume los siguientes endpoints AJAX:

| Endpoint | Uso |
|----------|-----|
| `GET /enums/unidades-compatibles?unidad=X` | Obtiene unidades compatibles para la cantidad total |
| `GET /enums/subunidades?unidad=X` | Obtiene unidades para cada bulto individual |

Ambos endpoints requieren el token CSRF en el header para funcionar correctamente.

---

## 8. CONTROLLER

### 8.1 Clase: AltaIngresoCompraController

El controlador maneja el flujo de 3 pasos del caso de uso y extiende `AbstractCuController` para heredar servicios comunes (LoteService, ProductoService, ProveedorService).

### 8.2 Flujo de Endpoints

**GET /ingreso-compra** - Muestra el formulario vacio

Carga en el modelo: productos (solo externos), proveedores, tipos de producto y lista de paises. Inicializa las listas de bultos vacias.

**POST /ingreso-compra/confirm** - Valida y muestra vista previa

Recibe el DTO con los datos del formulario. Ejecuta Bean Validation automaticamente y luego invoca las validaciones de negocio del Service. Si hay errores, retorna al formulario con los mensajes. Si todo es valido, resuelve los nombres de producto/proveedor para mostrar en la pantalla de confirmacion.

**POST /ingreso-compra** - Persiste los datos

Re-valida los datos por seguridad (el usuario podria haber manipulado el formulario entre confirm y submit). Establece la fecha/hora de creacion y delega al Service para crear el lote. Redirige a la pantalla de exito con el DTO resultante.

### 8.3 Caracteristicas Destacadas

- **Doble validacion:** Los datos se validan tanto en /confirm como en el POST final por seguridad.
- **Flash Attributes:** El resultado se pasa a la pantalla de exito mediante RedirectAttributes para sobrevivir al redirect.
- **Filtrado de productos:** Solo muestra productos externos (no SEMIELABORADO ni UNIDAD_VENTA) ya que este CU es para compras a proveedores.

---

## 9. SERVICE

### 9.1 Clase: AltaIngresoCompraService

El servicio contiene la logica de negocio del caso de uso. Extiende `AbstractCuService` para heredar validadores comunes y repositorios.

### 9.2 Metodo Principal: altaStockPorCompra

Este metodo ejecuta toda la operacion en una unica transaccion. El flujo es:

1. **Establecer contexto de auditoria:** Registra "CU1_INGRESO_COMPRA" para que Hibernate Envers lo capture en las tablas de auditoria.

2. **Obtener usuario actual:** Recupera el usuario autenticado para registrar quien creo el lote.

3. **Cargar entidades relacionadas:** Busca el Proveedor, Producto y opcionalmente el Fabricante. Si no existen, lanza excepcion.

4. **Crear el Lote:** Genera la entidad Lote con estado NUEVO y dictamen RECIBIDO. Genera el codigo de lote con formato `{codigoProducto}_{yyMM}{####}`.

5. **Crear los Bultos:** Por cada bulto indicado, crea una entidad Bulto con su cantidad y unidad. Si es un solo bulto, recibe toda la cantidad; si son multiples, cada uno recibe su cantidad del array.

6. **Persistir Lote:** Guarda el lote en la base de datos. Los bultos se guardan automaticamente por cascade.

7. **Crear Movimiento:** Genera un movimiento de tipo ALTA con motivo COMPRA. Incluye el campo `motivoDelCambio` requerido por ANMAT.

8. **Crear Detalles:** Por cada bulto, crea un DetalleMovimiento que vincula el movimiento con el bulto.

9. **Persistir Movimiento:** Guarda el movimiento y sus detalles.

10. **Retornar DTO:** Convierte el lote guardado a DTO para mostrar en la pantalla de exito.

### 9.3 Generacion del Codigo de Lote

El codigo de lote sigue el formato: `{codigoProducto}_{yyMM}{####}`

Ejemplo: `1-05701_25120001` donde:
- `1-05701`: Codigo del producto
- `2512`: Diciembre 2025
- `0001`: Primer lote del anio para ese producto

El sistema busca los lotes existentes del anio actual y calcula el siguiente secuencial disponible.

---

## 10. PERSISTENCIA

### 10.1 Entidades Creadas

| Entidad | Estado Inicial |
|---------|----------------|
| Lote | estado=NUEVO, dictamen=RECIBIDO, activo=true |
| Bulto (1 o mas) | estado=NUEVO, activo=true |
| Movimiento | tipo=ALTA, motivo=COMPRA, activo=true |
| DetalleMovimiento (1 por bulto) | activo=true |

### 10.2 Relaciones

El Lote es la entidad central. Contiene una lista de Bultos (relacion uno-a-muchos con cascade). El Movimiento referencia al Lote y contiene DetalleMovimientos que a su vez referencian cada Bulto.

### 10.3 Cascade y Soft Delete

Los Bultos se guardan automaticamente al guardar el Lote (cascade PERSIST). Lo mismo ocurre con los DetalleMovimiento al guardar el Movimiento.

Todas las entidades usan borrado logico: en lugar de DELETE fisico, se ejecuta UPDATE activo=false.

---

## 11. AUDITORIA (ANMAT)

El sistema cumple con los requerimientos de la Disposicion ANMAT 4159:

- **Hibernate Envers:** Todas las entidades (Lote, Bulto, Movimiento) estan auditadas. Cada cambio genera un registro en las tablas `_aud` con usuario, timestamp y caso de uso.

- **Campo motivoDelCambio:** El movimiento incluye el motivo ingresado por el usuario (mínimo 20 caracteres). Este campo es obligatorio para cumplir con el Requerimiento 9 del Anexo 6.

- **Contexto de caso de uso:** El identificador "CU1_INGRESO_COMPRA" se guarda en cada revision de auditoria.

---

## 12. SEGURIDAD

- **CSRF:** El formulario incluye token CSRF como campo oculto. Las llamadas AJAX incluyen el token en headers.
- **Autorizacion:** Solo usuarios con rol ADMIN o ANALISTA_PLANTA pueden acceder.
- **XSS:** Todos los datos se renderizan con escape automatico de HTML (th:text).

---

## 13. MANEJO DE ERRORES

Los errores se manejan en diferentes niveles:

- **Service:** Si el proveedor o producto no existen, se lanza una excepcion con mensaje descriptivo.
- **Controller:** Los errores de validacion (Bean Validation y negocio) se muestran junto a cada campo en el formulario.
- **Excepciones no controladas:** Redirigen a la pagina de error global del sistema.

---

## 14. LOGGING

El service registra eventos en los siguientes puntos:

- **Inicio:** Usuario, producto y proveedor involucrados
- **Creacion:** Codigo del lote generado
- **Completado:** Resumen con codigo, cantidad, bultos y motivo
- **Errores:** Tipo de validacion que fallo (motivo, cantidad, fechas, bultos)

Todos los logs usan el prefijo `[CU1]` para facilitar la busqueda.

---

## 15. MEJORAS SUGERIDAS

| Prioridad | Mejora |
|-----------|--------|
| MEDIA | Validar suma de bultos en JavaScript antes de enviar |
| BAJA | Fallback local si los CDN no responden |

---

*Documento generado: 2025-12-12*
*Version: 4.1*
