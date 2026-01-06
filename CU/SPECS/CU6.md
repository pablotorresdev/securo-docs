# CU6 - RESULTADO ANALISIS RECHAZADO

**Caso de Uso:** Registro de Resultado de Analisis - Rechazado
**URL Base:** `/calidad/analisis/resultado-analisis`
**Tipo:** MODIFICACION (Cambio de estado)
**Categoria:** Calidad (color purpura)
**Identificador Interno:** `CU5_CU6_RESULTADO_ANALISIS`

---

## 1. DESCRIPCION

Permite registrar el resultado de un analisis de control de calidad como RECHAZADO. El lote queda inhabilitado para uso en produccion o venta y debe gestionarse su devolucion al proveedor o descarte. Se actualiza el analisis con el dictamen RECHAZADO y genera un movimiento de MODIFICACION con motivo RESULTADO_ANALISIS.

**Nota:** Este CU comparte implementacion con CU5 (Resultado Aprobado). La diferencia radica en el dictamen final seleccionado (RECHAZADO vs APROBADO) y los campos requeridos.

---

## 2. ROLES AUTORIZADOS

| Rol | Nivel | Acceso |
|-----|-------|--------|
| ADMIN | 6 | Permitido |
| GERENTE_CONTROL_CALIDAD | 3 | Permitido |
| Otros | - | No permitido |

**Anotacion:** `@PreAuthorize("hasAnyAuthority('ROLE_ADMIN', 'ROLE_GERENTE_CONTROL_CALIDAD')")`

---

## 3. FLUJO DE PANTALLAS

```
+---------------------------------------------------------------------+
|  1. FORMULARIO                                                       |
|     GET /calidad/analisis/resultado-analisis                        |
|     Template: calidad/analisis/resultado-analisis.html              |
|  +---------------------------------------------------------------+  |
|  |  Select2: Lote (nombre, codigo, nro analisis)                 |  |
|  |  O Select2: Analisis en curso                                 |  |
|  |  Detalles del lote (dinamico al seleccionar)                  |  |
|  |  Resultado (Dictamen): APROBADO / RECHAZADO                   |  |
|  |  Fecha Realizado Analisis                                     |  |
|  |  Motivo del cambio (min 20 chars)                             |  |
|  +---------------------------------------------------------------+  |
|  [Cancelar]                              [Enviar Resultado] ->       |
+---------------------------------------------------------------------+
                              |
                              | POST /calidad/analisis/resultado-analisis/confirm
                              v
+---------------------------------------------------------------------+
|  2. CONFIRMACION                                                     |
|     POST /calidad/analisis/resultado-analisis/confirm               |
|     Template: calidad/analisis/resultado-analisis-confirm.html      |
|  +---------------------------------------------------------------+  |
|  |  Warning Box ROJO: "Esta por RECHAZAR este analisis"          |  |
|  |  - El lote cambiara su dictamen de CUARENTENA a RECHAZADO     |  |
|  |  - No podra ser utilizado en produccion ni vendido            |  |
|  |  - Debera gestionarse su devolucion o descarte                |  |
|  |  Datos del resultado: lote, analisis, dictamen, fechas        |  |
|  |  Transicion de dictamen: CUARENTENA -> RECHAZADO              |  |
|  +---------------------------------------------------------------+  |
|  [Volver a Editar]                       [Confirmar Rechazo] ->      |
+---------------------------------------------------------------------+
                              |
                              | POST /calidad/analisis/resultado-analisis
                              v
+---------------------------------------------------------------------+
|  3. EXITO                                                            |
|     GET /calidad/analisis/resultado-analisis-ok (redirect)          |
|     Template: calidad/analisis/resultado-analisis-ok.html           |
|  +---------------------------------------------------------------+  |
|  |  Cambio de dictamen a RECHAZADO exitoso                       |  |
|  |  Detalles del lote actualizado                                |  |
|  |  Tabla de movimientos                                         |  |
|  |  Tabla de analisis                                            |  |
|  +---------------------------------------------------------------+  |
|  [Continuar]                                                         |
+---------------------------------------------------------------------+
```

El flujo de 3 pasos es consistente con los demas CUs del sistema: formulario -> confirmacion -> persistencia.

---

## 4. ARCHIVOS INVOLUCRADOS

### Frontend (Templates)
| Archivo | Descripcion |
|---------|-------------|
| `templates/calidad/analisis/resultado-analisis.html` | Formulario principal (418 lineas) |
| `templates/calidad/analisis/resultado-analisis-confirm.html` | Pantalla de confirmacion (preview) |
| `templates/calidad/analisis/resultado-analisis-ok.html` | Pantalla de exito |

### Backend
| Archivo | Descripcion |
|---------|-------------|
| `controller/cu/ModifResultadoAnalisisController.java` | Controlador (129 lineas) |
| `service/cu/ModifResultadoAnalisisService.java` | Servicio de negocio (224 lineas) |
| `service/cu/AbstractCuService.java` | Clase base con validaciones comunes |

### Validadores Especializados
| Archivo | Responsabilidad |
|---------|-----------------|
| `service/cu/validator/AnalisisValidator.java` | Validaciones de datos de analisis |
| `service/cu/validator/FechaValidator.java` | Validaciones de fechas |

### Utilidades de Creacion de Entidades
| Archivo | Uso en CU6 |
|---------|------------|
| `utils/MovimientoModificacionUtils.java` | Factory para crear Movimiento MODIFICACION |
| `dto/mapper/AnalisisMapper.java` | Factory para crear/actualizar entidad Analisis |

### DTOs y Entidades
| Archivo | Uso en CU6 |
|---------|------------|
| `dto/MovimientoDTO.java` | DTO principal del formulario |
| `entity/Lote.java` | Entidad Lote (cambia dictamen) |
| `entity/Analisis.java` | Entidad Analisis (recibe resultado) |
| `entity/Movimiento.java` | Entidad Movimiento |

---

## 5. CAMPOS DEL FORMULARIO

### 5.1 Obligatorios

| Campo | HTML ID | Tipo | Validacion Browser | Validacion Server |
|-------|---------|------|-------------------|-------------------|
| Lote | `loteSelector` | select (Select2) | - | Validado en service |
| O Analisis | `analisisSelector` | select (Select2) | - | Validado en service |
| Resultado (Dictamen) | `dictamenFinal` | select | `required` | `@NotNull` |
| Fecha Realizado Analisis | `fechaRealizadoAnalisis` | date | `required` | `@PastOrPresent` |
| Motivo del Cambio | `motivoDelCambio` | textarea | `required`, `minlength="20"` | `@NotNull`, `@Size(min=20)` |

### 5.2 Campos NO Requeridos para RECHAZADO

A diferencia de CU5 (APROBADO), cuando el dictamen es RECHAZADO los siguientes campos NO son obligatorios y se ignoran si se ingresan:

| Campo | HTML ID | Tipo | Comportamiento |
|-------|---------|------|----------------|
| Fecha de Reanalisis | `fechaReanalisis` | date | Ignorado para RECHAZADO |
| Fecha de Vencimiento | `fechaVencimiento` | date | Ignorado para RECHAZADO |
| Titulo (%) | `titulo` | number | Ignorado para RECHAZADO |

### 5.3 Campos Hidden

| Campo | HTML ID | Descripcion |
|-------|---------|-------------|
| codigoLote | `codigoLote` | Codigo del lote seleccionado |
| nroAnalisis | `nroAnalisis` | Numero del analisis en curso |
| fechaMovimiento | `fechaMovimiento` | Fecha actual (auto) |

---

## 6. VALIDACIONES POR CAPA

El sistema implementa validacion en 3 capas, cada una con responsabilidades especificas:

### 6.1 CAPA 1: FRONTEND (Browser - HTML5 + JavaScript)

**Ubicacion:** `templates/calidad/analisis/resultado-analisis.html`

**Tipo:** Validacion inmediata en el navegador antes del submit.

| Campo | Atributo HTML5 | Comportamiento |
|-------|---------------|----------------|
| `dictamenFinal` | `required` | Bloquea submit si no seleccionado |
| `fechaRealizadoAnalisis` | `required`, `type="date"` | Bloquea submit si vacio |
| `motivoDelCambio` | `required`, `minlength="20"` | Minimo 20 caracteres |

**JavaScript Dinamico:**
- Inicializa fecha de realizado con fecha actual (timezone Argentina)
- Carga detalle del analisis via AJAX (`/analisis/{nroAnalisis}`)
- Sincroniza seleccion entre selector de Lote y selector de Analisis

**Limitaciones:** No valida existencia de muestreo previo (delegado al servidor).

---

### 6.2 CAPA 2: BEAN VALIDATION (Spring - Anotaciones Jakarta)

**Ubicacion:** `dto/MovimientoDTO.java`

Se ejecuta automaticamente al recibir el DTO en el controller mediante `@Valid`.

#### Campos Obligatorios

| Campo | Anotaciones | Mensaje de Error |
|-------|-------------|------------------|
| fechaMovimiento | `@NotNull`, `@PastOrPresent` | "La fecha del movimiento es obligatoria" / "no puede ser futura" |
| motivoDelCambio | `@NotNull`, `@Size(min=20)` | "El motivo del cambio es obligatorio" / "debe tener al menos 20 caracteres" |
| fechaRealizadoAnalisis | `@PastOrPresent` | "La fecha en que se realizo el analisis no puede ser futura" |

#### Campos Opcionales con Restricciones

| Campo | Anotaciones | Mensaje de Error |
|-------|-------------|------------------|
| nroAnalisis | `@Size(max=20)` | "El numero de analisis no debe superar 20 caracteres" |

---

### 6.3 CAPA 3: VALIDACION DE NEGOCIO (Service Layer)

**Ubicacion:** `ModifResultadoAnalisisService.java` + validadores en `service/cu/validator/`

Validaciones de reglas de negocio complejas que no pueden expresarse con anotaciones.

#### Secuencia de Validacion

El metodo `validarResultadoAnalisisInput()` ejecuta las siguientes validaciones en orden:

| # | Validador | Clase |
|---|-----------|-------|
| 1 | validarMotivoDelCambio | AbstractCuService |
| 2 | validarDatosMandatoriosResultadoAnalisisInput | AnalisisValidator |
| 3 | validarDatosResultadoAnalisisAprobadoInput | AnalisisValidator (se salta para RECHAZADO) |
| 4 | existeMuestreo | MovimientoRepository |
| 5 | validarLoteExiste | Service (inline) |
| 6 | validarFechaMovimientoPosteriorIngresoLote | FechaValidator |
| 7 | validarFechaAnalisisPosteriorIngresoLote | FechaValidator |
| 8 | validarFechasReanalisis | FechaValidator (no aplica para RECHAZADO) |
| 9 | validarFechaVencimientoObligatoriaParaUV | Service (no aplica para RECHAZADO) |

#### Detalle de Validaciones

**1. validarMotivoDelCambio** (ANMAT Disp. 4159, Anexo 6, Req. 9)

| Regla | Mensaje de Error |
|-------|------------------|
| `motivoDelCambio` no nulo y >= 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

**2. validarDatosMandatoriosResultadoAnalisisInput**

| Regla | Mensaje de Error |
|-------|------------------|
| `nroAnalisis` no vacio | "El Nro de Analisis es obligatorio" |
| `dictamenFinal` no nulo | "Debe ingresar un Resultado" |
| `fechaRealizadoAnalisis` no nula | "Debe ingresar la fecha en la que se realizo el analisis" |

**3. validarDatosResultadoAnalisisAprobadoInput**

Esta validacion SE OMITE cuando el dictamen es RECHAZADO. El codigo en `AnalisisValidator.java:84` verifica:
```java
if (DictamenEnum.APROBADO != dto.getDictamenFinal()) {
    return true; // Se salta para RECHAZADO
}
```

**4. existeMuestreo**

| Regla | Mensaje de Error |
|-------|------------------|
| Debe existir un movimiento de MUESTREO para el nroAnalisis | "No se encontro un MUESTREO realizado para ese Nro de Analisis X" |

**5. validarLoteExiste**

| Regla | Mensaje de Error |
|-------|------------------|
| El lote con `codigoLote` debe existir y estar activo | "Lote no encontrado." |

**6. validarFechaMovimientoPosteriorIngresoLote**

| Regla | Mensaje de Error |
|-------|------------------|
| `fechaMovimiento` >= `fechaIngreso` del lote | "La fecha del movimiento no puede ser anterior a la fecha de ingreso del lote" |

**7. validarFechaAnalisisPosteriorIngresoLote**

| Regla | Mensaje de Error |
|-------|------------------|
| `fechaRealizadoAnalisis` >= `fechaIngreso` del lote | "La fecha de realizado el analisis no puede ser anterior a la fecha de ingreso del lote" |

---

### 6.4 RESUMEN DE VALIDACIONES POR CAMPO

| Campo | Frontend | Bean Validation | Negocio |
|-------|----------|-----------------|---------|
| loteSelector/analisisSelector | - | - | Existencia verificada, Muestreo previo |
| dictamenFinal | required | - | @NotNull en AnalisisValidator |
| fechaRealizadoAnalisis | required | @PastOrPresent | >= fechaIngreso lote |
| motivoDelCambio | required, minlength | @NotNull, @Size(min=20) | >= 20 caracteres (ANMAT) |

---

## 7. TEMPLATE (Frontend)

### 7.1 Estilo Visual

El formulario utiliza la paleta de colores **purpura** que identifica las operaciones de calidad en todo el sistema. Los elementos principales incluyen:

- **Fondo de pagina:** Purpura claro (`bg-calidad`)
- **Tarjeta del formulario:** Blanca con borde purpura y sombra suave
- **Botones primarios:** Gradiente purpura (`btn-calidad`)
- **Iconos de seccion:** Bootstrap Icons en color purpura
- **Badge CU:** `CU5/6` en purpura

Para el caso RECHAZADO, la pantalla de confirmacion muestra un **warning box rojo** con las consecuencias del rechazo:
- El lote cambiara su dictamen de CUARENTENA a RECHAZADO
- No podra ser utilizado en produccion ni vendido
- Debera gestionarse su devolucion al proveedor o descarte

### 7.2 Librerias Utilizadas

| Libreria | Proposito |
|----------|-----------|
| Bootstrap 5.3 | Framework CSS base, grid responsivo |
| Bootstrap Icons | Iconografia en todo el formulario |
| jQuery 3.7 | Manipulacion DOM y eventos |
| Select2 | Dropdowns con busqueda de texto |

### 7.3 Comportamiento JavaScript

El script del template ejecuta las siguientes logicas al cargar la pagina:

1. **Inicializacion de fecha:** Establece la fecha de realizado con la fecha actual usando timezone de Argentina.

2. **Configuracion de selectores:** Convierte los selects de Lote y Analisis en componentes Select2 con busqueda.

3. **Sincronizacion de selectores:** Cuando el usuario selecciona un lote, el selector de analisis se limpia y viceversa. Los campos hidden `codigoLote` y `nroAnalisis` se actualizan automaticamente.

4. **Carga de detalle via AJAX:** Al seleccionar un lote/analisis, se consulta `/analisis/{nroAnalisis}` para mostrar informacion adicional (codigo lote, fecha muestreo, dictamen actual).

5. **Restauracion de datos:** Si el formulario se recarga por un error de validacion, los datos previamente ingresados se restauran automaticamente.

6. **Prevencion de doble submit:** Al enviar el formulario, el boton se deshabilita y muestra un spinner con texto "Procesando..." para evitar envios duplicados. Aplica tanto al boton "Enviar Resultado" como al boton "Confirmar Rechazo" en la pantalla de confirmacion.

### 7.4 Interacciones Dinamicas

Este CU consume el siguiente endpoint AJAX:

| Endpoint | Uso |
|----------|-----|
| `GET /analisis/{nroAnalisis}` | Obtiene detalle del analisis (codigoLote, fechaMuestreo, dictamen) |

Ademas, utiliza data-attributes en los options de los selectores:

| Atributo | Uso |
|----------|-----|
| `attr-codigoLote` | Codigo del lote asociado al analisis |
| `data-es-unidad-venta` | Determina si el producto es Unidad de Venta |

---

## 8. CONTROLLER

### 8.1 Clase: ModifResultadoAnalisisController

El controlador maneja el flujo estandar de 3 pasos (formulario -> confirmacion -> persistencia) y extiende `AbstractCuController` para heredar servicios comunes (LoteService, ProductoService, ProveedorService, AnalisisService).

### 8.2 Flujo de Endpoints

**GET /resultado-analisis** - Muestra el formulario vacio

Carga en el modelo:
- Lotes elegibles para resultado (`loteService.findAllForResultadoAnalisisDTOs()`)
- Analisis en curso (`analisisService.findAllEnCursoForLotesCuarentenaDTOs()`)
- Opciones de resultado: APROBADO, RECHAZADO
- Inicializa fecha de movimiento con fecha actual

**POST /resultado-analisis/confirm** - Valida y muestra confirmacion

Recibe el DTO con los datos del formulario. Ejecuta Bean Validation automaticamente y luego invoca las validaciones de negocio del Service. Si hay errores, retorna al formulario con los mensajes. Si todo es valido, carga el LoteDTO para mostrar informacion completa y redirige a la pantalla de confirmacion.

**POST /resultado-analisis** - Persiste resultado (desde confirmacion)

Recibe el DTO desde la pantalla de confirmacion. Ejecuta las validaciones nuevamente (por seguridad) y persiste el resultado. Redirige a la pantalla de exito.

**GET /resultado-analisis-ok** - Muestra resultado exitoso

Recibe el LoteDTO via FlashAttributes y muestra los detalles del lote actualizado incluyendo tablas de movimientos y analisis.

### 8.3 Caracteristicas Destacadas

- **Flujo de 3 pasos:** Consistente con los demas CUs (formulario -> confirmacion -> exito).
- **Doble selector:** El usuario puede buscar por Lote o por Analisis, facilitando el flujo de trabajo.
- **Flash Attributes:** El resultado se pasa a la pantalla de exito mediante RedirectAttributes para sobrevivir al redirect.
- **Filtrado de lotes:** Solo muestra lotes en CUARENTENA con analisis en curso.
- **Confirmacion visual diferenciada:** La pantalla de confirmacion muestra warning ROJO para RECHAZADO (a diferencia del verde de APROBADO).

---

## 9. SERVICE

### 9.1 Clase: ModifResultadoAnalisisService

El servicio contiene la logica de negocio del caso de uso. Extiende `AbstractCuService` para heredar validadores comunes y repositorios.

### 9.2 Metodo Principal: persistirResultadoAnalisis

Este metodo ejecuta toda la operacion en una unica transaccion. El flujo para RECHAZADO es:

1. **Establecer contexto de auditoria:** Registra "CU5_CU6_RESULTADO_ANALISIS" para que Hibernate Envers lo capture en las tablas de auditoria.

2. **Obtener usuario actual:** Recupera el usuario autenticado para registrar quien realizo el cambio.

3. **Actualizar Analisis:** Invoca `addResultadoAnalisis()` que:
   - Busca el analisis por nroAnalisis
   - Establece fechaRealizado y dictamen=RECHAZADO
   - **Para RECHAZADO:** No establece fechaReanalisis, fechaVencimiento ni titulo

4. **Cargar el Lote:** Obtiene el lote desde el analisis.

5. **Crear Movimiento:** Genera un movimiento de tipo MODIFICACION con motivo RESULTADO_ANALISIS usando `MovimientoModificacionUtils`. Registra dictamenInicial y dictamenFinal=RECHAZADO.

6. **Formatear motivoDelCambio:** Agrega prefijo `[CU6]` al motivo del cambio.

7. **Actualizar Lote:** Cambia el dictamen del lote a RECHAZADO y agrega el movimiento.

8. **Persistir y retornar:** Guarda el lote y retorna el DTO para mostrar en la pantalla de exito.

### 9.3 Diferencia con CU5 (Aprobado)

La logica en el servicio diferencia ambos casos en `ModifResultadoAnalisisService.java`:

```java
if (DictamenEnum.APROBADO == dto.getDictamenFinal()) {
    movimiento.setMotivoDelCambio(formatMotivoDelCambioWithCU(UseCaseTag.CU5, dto.getMotivoDelCambio()));
} else {
    movimiento.setMotivoDelCambio(formatMotivoDelCambioWithCU(UseCaseTag.CU6, dto.getMotivoDelCambio()));
}
```

### 9.4 Pre-requisitos del Lote

El lote debe cumplir las siguientes condiciones para aparecer en la lista:

| Condicion | Descripcion |
|-----------|-------------|
| Activo | El lote debe estar activo (no eliminado) |
| Dictamen CUARENTENA | El lote debe estar en cuarentena |
| Analisis en curso | Debe existir un analisis sin dictamen y sin fecha de realizacion |
| Con stock | Debe tener al menos un bulto con cantidad actual > 0 |
| Muestreo previo | Debe existir un movimiento de MUESTREO para el nroAnalisis |

---

## 10. PERSISTENCIA

### 10.1 Entidades Afectadas

| Entidad | Accion | Estado Final |
|---------|--------|--------------|
| Lote | UPDATE | dictamen=RECHAZADO |
| Analisis | UPDATE | dictamen=RECHAZADO, fechaRealizado |
| Movimiento | INSERT | tipo=MODIFICACION, motivo=RESULTADO_ANALISIS |

### 10.2 Movimiento Generado

El movimiento de modificacion incluye:

- **tipo:** MODIFICACION
- **motivo:** RESULTADO_ANALISIS
- **fecha:** fechaRealizadoAnalisis (no fechaMovimiento)
- **dictamenInicial:** CUARENTENA
- **dictamenFinal:** RECHAZADO
- **nroAnalisis:** Numero del analisis dictaminado
- **motivoDelCambio:** Formateado con prefijo `[CU6]`

### 10.3 Cascade y Soft Delete

El Analisis se guarda explicitamente. El Movimiento tambien se guarda explicitamente y luego se agrega a la lista del Lote.

Todas las entidades usan borrado logico: en lugar de DELETE fisico, se ejecuta UPDATE activo=false.

---

## 11. AUDITORIA (ANMAT)

El sistema cumple con los requerimientos de la Disposicion ANMAT 4159:

- **Hibernate Envers:** Todas las entidades (Lote, Analisis, Movimiento) estan auditadas. Cada cambio genera un registro en las tablas `_aud` con usuario, timestamp y caso de uso.

- **Campo motivoDelCambio:** El movimiento incluye el motivo ingresado por el usuario (mínimo 20 caracteres). Este campo es obligatorio para cumplir con el Requerimiento 9 del Anexo 6.

- **Contexto de caso de uso:** El identificador "CU5_CU6_RESULTADO_ANALISIS" se guarda en cada revision de auditoria.

- **Prefijo de CU:** El motivoDelCambio incluye `[CU6]` para identificar que fue un rechazo.

- **Transicion de dictamen:** Se registra el dictamen anterior (CUARENTENA) y el nuevo (RECHAZADO) en el movimiento para trazabilidad completa.

---

## 12. SEGURIDAD

- **CSRF:** El formulario incluye token CSRF como campo oculto. Las llamadas AJAX incluyen el token en headers via meta tags.
- **Autorizacion:** Solo usuarios con rol ADMIN o GERENTE_CONTROL_CALIDAD pueden acceder.
- **XSS:** Todos los datos se renderizan con escape automatico de HTML (th:text).
- **Validacion de muestreo:** Impide rechazar lotes sin muestreo previo registrado.

---

## 13. MANEJO DE ERRORES

Los errores se manejan en diferentes niveles:

- **Service:** Si el lote no existe, no hay muestreo previo, o las validaciones de negocio fallan, se rechaza el campo con mensaje descriptivo.
- **Controller:** Los errores de validacion (Bean Validation y negocio) se muestran junto a cada campo en el formulario.
- **Excepciones no controladas:** Redirigen a la pagina de error global del sistema.

---

## 14. LOGGING

El service registra eventos en los siguientes puntos:

- **Inicio:** Usuario, codigo de lote, numero de analisis y dictamen final (RECHAZADO)
- **Completado:** Lote, dictamen final y motivo del cambio

Todos los logs usan el prefijo `[CU5/6]` para facilitar la busqueda.

---

## 15. MEJORAS SUGERIDAS

| Prioridad | Mejora |
|-----------|--------|
| BAJA | Fallback local si los CDN no responden |

---

*Documento generado: 2025-12-13*
*Version: 1.0*
