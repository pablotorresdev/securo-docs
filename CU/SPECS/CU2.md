# CU2 - DICTAMEN CUARENTENA

**Caso de Uso:** Cambio de Dictamen a Cuarentena
**URL Base:** `/calidad/dictamen/cuarentena`
**Tipo:** MODIFICACION (Cambio de estado)
**Categoria:** Calidad (color purpura)
**Identificador Interno:** `CU2_DICTAMEN_CUARENTENA`

---

## 1. DESCRIPCION

Permite cambiar el dictamen de un lote a CUARENTENA para iniciar el proceso de analisis de control de calidad. El lote queda en estado de evaluacion y no puede ser utilizado hasta que se apruebe o rechace. Se crea un registro de analisis y un movimiento de modificacion.

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
|     GET /calidad/dictamen/cuarentena                                |
|     Template: calidad/dictamen/cuarentena.html                      |
|  +---------------------------------------------------------------+  |
|  |  Select2: Lote (codigo, producto, proveedor, dictamen)        |  |
|  |  Fecha del cambio                                             |  |
|  |  Nro Analisis (editable O readonly segun dictamen)            |  |
|  |  Nro Reanalisis (si aplica)                                   |  |
|  |  Motivo del cambio (min 20 chars)                             |  |
|  +---------------------------------------------------------------+  |
|  [Cancelar]                              [Vista Previa] ->          |
+---------------------------------------------------------------------+
                              |
                              | POST /calidad/dictamen/cuarentena/confirm
                              v
+---------------------------------------------------------------------+
|  2. CONFIRMACION                                                     |
|     Template: calidad/dictamen/cuarentena-confirm.html              |
|  +---------------------------------------------------------------+  |
|  |  Warning Box: Consecuencias del cambio                        |  |
|  |  Preview: Info completa del lote                              |  |
|  |  Dictamen: [Actual] -> [CUARENTENA]                           |  |
|  |  Cantidades, Fechas, Estado                                   |  |
|  +---------------------------------------------------------------+  |
|  [Volver a Editar]                        [Confirmar Cambio] ->     |
+---------------------------------------------------------------------+
                              |
                              | POST /calidad/dictamen/cuarentena
                              v
+---------------------------------------------------------------------+
|  3. EXITO                                                            |
|     GET /calidad/dictamen/cuarentena-ok (redirect)                  |
|     Template: calidad/dictamen/cuarentena-ok.html                   |
|  +---------------------------------------------------------------+  |
|  |  Cambio Exitoso - CU2                                         |  |
|  |  Lote actualizado con nuevo dictamen                          |  |
|  |  Tabla de movimientos (incluyendo el nuevo)                   |  |
|  |  Tabla de bultos                                              |  |
|  +---------------------------------------------------------------+  |
|  [Nueva Cuarentena]                       [Volver al Inicio]        |
+---------------------------------------------------------------------+
```

---

## 4. ARCHIVOS INVOLUCRADOS

### Frontend (Templates)
| Archivo | Descripcion |
|---------|-------------|
| `templates/calidad/dictamen/cuarentena.html` | Formulario principal (465 lineas) |
| `templates/calidad/dictamen/cuarentena-confirm.html` | Vista previa/confirmacion |
| `templates/calidad/dictamen/cuarentena-ok.html` | Pantalla de exito |

### Backend
| Archivo | Descripcion |
|---------|-------------|
| `controller/cu/ModifDictamenCuarentenaController.java` | Controlador (137 lineas) |
| `service/cu/ModifDictamenCuarentenaService.java` | Servicio de negocio (163 lineas) |
| `service/cu/AbstractCuService.java` | Clase base con validaciones comunes |

### Validadores Especializados
| Archivo | Responsabilidad |
|---------|-----------------|
| `service/cu/validator/AnalisisValidator.java` | Validaciones de numero de analisis |
| `service/cu/validator/FechaValidator.java` | Validaciones de fechas |

### Utilidades de Creacion de Entidades
| Archivo | Uso en CU2 |
|---------|------------|
| `utils/MovimientoModificacionUtils.java` | Factory para crear Movimiento MODIFICACION |
| `dto/DTOUtils.java` | Factory para crear entidad Analisis |

### DTOs y Entidades
| Archivo | Uso en CU2 |
|---------|------------|
| `dto/MovimientoDTO.java` | DTO principal del formulario |
| `entity/Lote.java` | Entidad Lote (cambia dictamen) |
| `entity/Analisis.java` | Entidad Analisis (se crea nuevo) |
| `entity/Movimiento.java` | Entidad Movimiento |
| `entity/Traza.java` | Trazas del lote (cambian estado) |

---

## 5. CAMPOS DEL FORMULARIO

### 5.1 Obligatorios

| Campo | HTML ID | Tipo | Validacion Browser | Validacion Server |
|-------|---------|------|-------------------|-------------------|
| Lote | `codigoLote` | select (Select2) | `required` | Validado en service |
| Fecha del Cambio | `fechaMovimiento` | date | `required` | `@NotNull`, `@PastOrPresent` |
| Nro Analisis* | `nroAnalisis` | text + select | `required` (condicional) | `@Size(max=20)` |
| Nro Reanalisis* | `nroReanalisis` | text + select | `required` (condicional) | `@Size(max=20)` |
| Motivo del Cambio | `motivoDelCambio` | textarea | `required`, `minlength="20"` | `@NotNull`, `@Size(min=20)` |

*Solo uno de los dos segun el estado del lote

### 5.2 Logica Condicional de Campos

El formulario muestra diferentes campos segun el estado del lote seleccionado:

**Caso 1: Lote tiene analisis EN CURSO**
- Muestra nroAnalisis como campo READONLY (el analisis ya existe)
- No permite crear nuevo analisis

**Caso 2: Lote con dictamen RECIBIDO (primer analisis)**
- Muestra campo nroAnalisis EDITABLE
- El usuario ingresa el numero de analisis nuevo
- Formato: PREFIJO-N-NNNN (ej: MP-1-0001)

**Caso 3: Otros dictamenes (reanalisis)**
- Muestra nroAnalisis actual como READONLY
- Muestra campo nroReanalisis EDITABLE para ingresar nuevo numero

### 5.3 Formato del Numero de Analisis

El numero de analisis sigue el formato: `PREFIJO-N-NNNN`

| Prefijo | Tipo de Producto            |
|---------|-----------------------------|
| MP | Materia Prima               |
| ME | Material de Empaque         |
| MPD | Materia Prima para Desarrollo |
| PTD | Producto Terminado para Desarrollo |
| SE | Semi Elaborado              |
| PT | Producto Terminado          |

Ejemplo: `MP-1-0001` indica Materia Prima, numero 1-0001.

---

## 6. VALIDACIONES POR CAPA

El sistema implementa validacion en 3 capas, cada una con responsabilidades especificas:

### 6.1 CAPA 1: FRONTEND (Browser - HTML5 + JavaScript)

**Ubicacion:** `templates/calidad/dictamen/cuarentena.html`

**Tipo:** Validacion inmediata en el navegador antes del submit.

| Campo | Atributo HTML5 | Comportamiento |
|-------|---------------|----------------|
| `codigoLote` | `required` | Bloquea submit si no seleccionado |
| `fechaMovimiento` | `required`, `type="date"` | Bloquea submit si vacio |
| `nroAnalisis` | `required` (condicional via JS) | Segun logica de visibilidad |
| `nroReanalisis` | `required` (condicional via JS) | Segun logica de visibilidad |
| `motivoDelCambio` | `required`, `minlength="20"` | Minimo 20 caracteres |

**JavaScript Dinamico:**
- Actualiza visibilidad de campos segun dictamen del lote seleccionado
- Aplica mascara N-NNNN a los digitos del numero de analisis
- Combina prefijo + digitos en campo hidden antes del submit
- Inicializa fecha del movimiento con fecha actual (timezone Argentina)

**Limitaciones:** No valida unicidad del numero de analisis (delegado al servidor).

---

### 6.2 CAPA 2: BEAN VALIDATION (Spring - Anotaciones Jakarta)

**Ubicacion:** `dto/MovimientoDTO.java`

Se ejecuta automaticamente al recibir el DTO en el controller mediante `@Valid`.

#### Campos Obligatorios

| Campo | Anotaciones | Mensaje de Error |
|-------|-------------|------------------|
| fechaMovimiento | `@NotNull`, `@PastOrPresent` | "La fecha del movimiento es obligatoria" / "no puede ser futura" |
| motivoDelCambio | `@NotNull`, `@Size(min=20)` | "El motivo del cambio es obligatorio" / "debe tener al menos 20 caracteres" |

#### Campos Opcionales con Restricciones

| Campo | Anotaciones | Mensaje de Error |
|-------|-------------|------------------|
| nroAnalisis | `@Size(max=20)` | "El numero de analisis no debe superar 20 caracteres" |
| nroReanalisis | `@Size(max=20)` | "El numero de reanalisis no debe superar 20 caracteres" |

---

### 6.3 CAPA 3: VALIDACION DE NEGOCIO (Service Layer)

**Ubicacion:** `ModifDictamenCuarentenaService.java` + validadores en `service/cu/validator/`

Validaciones de reglas de negocio complejas que no pueden expresarse con anotaciones.

#### Secuencia de Validacion

El metodo `validarDictamenCuarentenaInput()` ejecuta las siguientes validaciones en orden:

| # | Validador | Clase |
|---|-----------|-------|
| 1 | validarMotivoDelCambio | AbstractCuService |
| 2 | validarNroAnalisisNotNull | AnalisisValidator |
| 3 | validarNroAnalisisUnico | AnalisisValidator |
| 4 | validarLoteExiste | Service (inline) |
| 5 | validarFechaMovimientoPosteriorIngresoLote | FechaValidator |
| 6 | validarFechaAnalisisPosteriorIngresoLote | FechaValidator |

#### Detalle de Validaciones

**1. validarMotivoDelCambio** (ANMAT Disp. 4159, Anexo 6, Req. 9)

| Regla | Mensaje de Error |
|-------|------------------|
| `motivoDelCambio` no nulo y >= 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

**2. validarNroAnalisisNotNull**

| Regla | Mensaje de Error |
|-------|------------------|
| Al menos uno de `nroAnalisis` o `nroReanalisis` debe tener valor | "Ingrese un nro de analisis" |

**3. validarNroAnalisisUnico**

| Regla | Mensaje de Error |
|-------|------------------|
| El numero de analisis/reanalisis no debe existir en la BD | "El nro de analisis X ya existe" |

**4. validarLoteExiste**

| Regla | Mensaje de Error |
|-------|------------------|
| El lote con `codigoLote` debe existir y estar activo | "Lote no encontrado." |

**5. validarFechaMovimientoPosteriorIngresoLote**

| Regla | Mensaje de Error |
|-------|------------------|
| `fechaMovimiento` >= `fechaIngreso` del lote | "La fecha del movimiento no puede ser anterior a la fecha de ingreso del lote" |

**6. validarFechaAnalisisPosteriorIngresoLote**

| Regla | Mensaje de Error |
|-------|------------------|
| `fechaRealizadoAnalisis` >= `fechaIngreso` del lote | "La fecha del analisis no puede ser anterior a la fecha de ingreso del lote" |

---

### 6.4 RESUMEN DE VALIDACIONES POR CAMPO

| Campo | Frontend | Bean Validation | Negocio |
|-------|----------|-----------------|---------|
| codigoLote | required | - | Existencia verificada |
| fechaMovimiento | required | @NotNull, @PastOrPresent | >= fechaIngreso lote |
| nroAnalisis | required (condicional) | @Size(max=20) | Not null (uno de los dos), Unico |
| nroReanalisis | required (condicional) | @Size(max=20) | Not null (uno de los dos), Unico |
| motivoDelCambio | required, minlength | @NotNull, @Size(min=20) | >= 20 caracteres (ANMAT) |

---

## 7. TEMPLATE (Frontend)

### 7.1 Estilo Visual

El formulario utiliza la paleta de colores **purpura** que identifica las operaciones de calidad en todo el sistema. Los elementos principales incluyen:

- **Fondo de pagina:** Purpura claro (`bg-calidad`)
- **Tarjeta del formulario:** Blanca con borde purpura y sombra suave
- **Botones primarios:** Gradiente purpura
- **Iconos de seccion:** Bootstrap Icons en color purpura

El selector de Lote utiliza **Select2** para permitir busqueda dentro de las opciones. Cada opcion del lote incluye: codigo, lote proveedor, producto y dictamen actual para facilitar la seleccion.

### 7.2 Librerias Utilizadas

| Libreria | Proposito |
|----------|-----------|
| Bootstrap 5.3 | Framework CSS base, grid responsivo |
| Bootstrap Icons | Iconografia en todo el formulario |
| jQuery 3.7 | Manipulacion DOM y eventos |
| Select2 | Dropdown con busqueda de texto |

### 7.3 Comportamiento JavaScript

El script del template ejecuta las siguientes logicas al cargar la pagina:

1. **Inicializacion de fecha:** Establece la fecha del movimiento con la fecha actual usando timezone de Argentina.

2. **Configuracion de selector:** Convierte el select de Lote en componente Select2 con busqueda.

3. **Visibilidad condicional:** Cuando el usuario selecciona un lote, el sistema lee los data-attributes del option para determinar que campos mostrar (nroAnalisis editable, readonly, o nroReanalisis).

4. **Mascara de analisis:** Aplica formato automatico N-NNNN a los digitos ingresados y combina con el prefijo seleccionado.

5. **Restauracion de datos:** Si el formulario se recarga por un error de validacion, los datos previamente ingresados se restauran automaticamente.

6. **Prevencion de doble submit:** Al enviar el formulario, el boton se deshabilita y muestra un spinner con texto "Procesando..." para evitar envios duplicados. Aplica tanto al boton "Vista Previa" como al boton "Confirmar Cambio" en la pantalla de confirmacion.

### 7.4 Interacciones Dinámicas

Este CU no consume endpoints AJAX. La logica dinamica se basa en data-attributes del select de lotes:

| Atributo | Uso |
|----------|-----|
| `data-dictamen` | Determina si mostrar nroAnalisis editable o readonly + nroReanalisis |
| `data-nroanalisis` | Numero del ultimo analisis (para mostrar en readonly) |
| `data-analisisencurso` | Si hay analisis en curso (bloquea nuevo ingreso) |

---

## 8. CONTROLLER

### 8.1 Clase: ModifDictamenCuarentenaController

El controlador maneja el flujo de 3 pasos del caso de uso y extiende `AbstractCuController` para heredar servicios comunes (LoteService, ProductoService, ProveedorService).

### 8.2 Flujo de Endpoints

**GET /cuarentena** - Muestra el formulario vacio

Carga en el modelo los lotes elegibles para cuarentena (mediante `loteService.findAllForCuarentenaDTOs()`). Inicializa el MovimientoDTO vacio.

**POST /cuarentena/confirm** - Valida y muestra vista previa

Recibe el DTO con los datos del formulario. Ejecuta Bean Validation automaticamente y luego invoca las validaciones de negocio del Service. Si hay errores, retorna al formulario con los mensajes. Si todo es valido, carga la informacion completa del lote para mostrar en la pantalla de confirmacion.

**POST /cuarentena** - Persiste los datos

Re-valida los datos por seguridad. Establece la fecha/hora de creacion y delega al Service para persistir el cambio. Redirige a la pantalla de exito con el LoteDTO resultante.

### 8.3 Caracteristicas Destacadas

- **Transaccion ReadOnly en confirm:** El endpoint /confirm usa `@Transactional(readOnly = true)` para garantizar que no modifica la base de datos durante la vista previa.
- **Doble validacion:** Los datos se validan tanto en /confirm como en el POST final por seguridad.
- **Flash Attributes:** El resultado se pasa a la pantalla de exito mediante RedirectAttributes para sobrevivir al redirect.
- **Filtrado de lotes:** Solo muestra lotes elegibles para cuarentena (dictamenes validos para transicion).

---

## 9. SERVICE

### 9.1 Clase: ModifDictamenCuarentenaService

El servicio contiene la logica de negocio del caso de uso. Extiende `AbstractCuService` para heredar validadores comunes y repositorios.

### 9.2 Metodo Principal: persistirDictamenCuarentena

Este metodo ejecuta toda la operacion en una unica transaccion. El flujo es:

1. **Establecer contexto de auditoria:** Registra "CU2_DICTAMEN_CUARENTENA" para que Hibernate Envers lo capture en las tablas de auditoria.

2. **Obtener usuario actual:** Recupera el usuario autenticado para registrar quien realizo el cambio.

3. **Cargar el Lote:** Busca el lote por codigo. Si no existe, lanza excepcion.

4. **Capturar dictamen anterior:** Guarda el dictamen actual para el audit trail (ANMAT Req. 9).

5. **Determinar numero de analisis:** Usa `nroAnalisis` o `nroReanalisis` segun cual tenga valor.

6. **Crear o reutilizar Analisis:** Si el numero de analisis no existe, crea una nueva entidad Analisis. Si ya existe un analisis en curso, lo reutiliza.

7. **Crear Movimiento:** Genera un movimiento de tipo MODIFICACION con motivo ANALISIS. Registra dictamenInicial y dictamenFinal (CUARENTENA).

8. **Actualizar Trazas:** Si el lote tiene trazas activas, las cambia a estado DISPONIBLE.

9. **Actualizar Lote:** Cambia el dictamen del lote a CUARENTENA y agrega el movimiento a su lista.

10. **Persistir y retornar:** Guarda el lote y retorna el DTO para mostrar en la pantalla de exito.

### 9.3 Dictamenes de Entrada Validos

El lote puede entrar a cuarentena desde los siguientes dictamenes:

| Dictamen Origen | Escenario |
|-----------------|-----------|
| RECIBIDO | Primer analisis de un lote recien ingresado |
| APROBADO | Reanalisis de un lote previamente aprobado |
| ANALISIS_EXPIRADO | Reanalisis por vencimiento del analisis anterior |
| LIBERADO | Reanalisis de un lote ya liberado para venta |
| DEVOLUCION_CLIENTES | Analisis de un lote devuelto por cliente |

---

## 10. PERSISTENCIA

### 10.1 Entidades Afectadas

| Entidad | Accion | Estado Final |
|---------|--------|--------------|
| Lote | UPDATE | dictamen=CUARENTENA |
| Analisis | INSERT (si nuevo) | Sin resultado aun |
| Movimiento | INSERT | tipo=MODIFICACION, motivo=ANALISIS |
| Traza (si existen) | UPDATE | estado=DISPONIBLE |

### 10.2 Movimiento Generado

El movimiento de modificacion incluye:

- **tipo:** MODIFICACION
- **motivo:** ANALISIS
- **dictamenInicial:** Dictamen anterior del lote
- **dictamenFinal:** CUARENTENA
- **nroAnalisis:** Numero del analisis asignado
- **motivoDelCambio:** Texto ingresado por el usuario (ANMAT)

### 10.3 Cascade y Soft Delete

El Analisis se guarda explicitamente y luego se agrega a la lista del Lote. El Movimiento tambien se guarda explicitamente.

Todas las entidades usan borrado logico: en lugar de DELETE fisico, se ejecuta UPDATE activo=false.

---

## 11. AUDITORIA (ANMAT)

El sistema cumple con los requerimientos de la Disposicion ANMAT 4159:

- **Hibernate Envers:** Todas las entidades (Lote, Analisis, Movimiento) estan auditadas. Cada cambio genera un registro en las tablas `_aud` con usuario, timestamp y caso de uso.

- **Campo motivoDelCambio:** El movimiento incluye el motivo ingresado por el usuario (mínimo 20 caracteres). Este campo es obligatorio para cumplir con el Requerimiento 9 del Anexo 6.

- **Contexto de caso de uso:** El identificador "CU2_DICTAMEN_CUARENTENA" se guarda en cada revision de auditoria.

- **Transicion de dictamen:** Se registra el dictamen anterior y el nuevo en el movimiento para trazabilidad completa.

---

## 12. SEGURIDAD

- **CSRF:** El formulario incluye token CSRF como campo oculto. Las llamadas AJAX incluyen el token en headers.
- **Autorizacion:** Solo usuarios con rol ADMIN o ANALISTA_CONTROL_CALIDAD pueden acceder.
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

- **Inicio:** Usuario, codigo de lote y numero de analisis
- **Completado:** Lote, dictamen final, numero de analisis y motivo del cambio
- **Errores:** Tipo de validacion que fallo

Todos los logs usan el prefijo `[CU2]` para facilitar la busqueda.

---

## 15. MEJORAS SUGERIDAS

| Prioridad | Mejora |
|-----------|--------|
| BAJA | Fallback local si los CDN no responden |

---

*Documento generado: 2025-12-12*
*Version: 3.1*
