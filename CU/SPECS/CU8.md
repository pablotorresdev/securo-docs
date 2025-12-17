# CU8 - REANALISIS DE LOTE APROBADO

**Caso de Uso:** Reanalisis de Lote Aprobado
**URL Base:** `/calidad/reanalisis/inicio-reanalisis`
**Tipo:** MODIFICACION (Crea nuevo analisis)
**Categoria:** Calidad (color purpura)
**Identificador Interno:** `CU8_REANALISIS_LOTE`

---

## 1. DESCRIPCION

Permite iniciar un nuevo analisis de control de calidad para un lote que ya fue APROBADO previamente. Este caso de uso se utiliza para controles periodicos de estabilidad o cuando se requiere una verificacion adicional del producto. El lote permanece en estado APROBADO durante todo el proceso. Se crea un nuevo registro de Analisis en estado En curso y se genera un movimiento de MODIFICACION con motivo ANALISIS.

**Diferencia con CU5/CU6:** CU5/6 registran el resultado de un analisis existente (cambian dictamen). CU8 crea un nuevo analisis para un lote ya aprobado (no cambia dictamen).

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
|     GET /calidad/reanalisis/inicio-reanalisis                       |
|     Template: calidad/reanalisis/inicio-reanalisis.html             |
|  +---------------------------------------------------------------+  |
|  |  Select2: Lote APROBADO (producto, dictamen, codigo, prov)    |  |
|  |  Detalles del lote (dinamico al seleccionar)                  |  |
|  |  - Informacion del producto y proveedor                       |  |
|  |  - Fechas (ingreso, reanalisis, vencimiento)                  |  |
|  |  - Historial de analisis previos                              |  |
|  |  Fecha del Reanalisis                                         |  |
|  |  Numero de Reanalisis                                         |  |
|  |  Motivo del cambio (min 20 chars)                             |  |
|  +---------------------------------------------------------------+  |
|  [Cancelar]                                   [Iniciar Reanalisis]   |
+---------------------------------------------------------------------+
                              |
                              | POST /calidad/reanalisis/inicio-reanalisis
                              v
+---------------------------------------------------------------------+
|  2. EXITO                                                            |
|     GET /calidad/reanalisis/inicio-reanalisis-ok (redirect)         |
|     Template: calidad/reanalisis/inicio-reanalisis-ok.html          |
|  +---------------------------------------------------------------+  |
|  |  Mensaje de exito: "Analisis asignado con exito"              |  |
|  |  Detalles del lote                                            |  |
|  |  Tabla de movimientos                                         |  |
|  |  Tabla de analisis (mostrando el nuevo en curso)              |  |
|  +---------------------------------------------------------------+  |
|  [Continuar]                                                         |
+---------------------------------------------------------------------+
```

**Nota:** Este CU usa flujo de 2 pasos (formulario -> exito) sin pantalla de confirmacion intermedia.

---

## 4. ARCHIVOS INVOLUCRADOS

### Frontend (Templates)
| Archivo | Descripcion | Lineas |
|---------|-------------|--------|
| `templates/calidad/reanalisis/inicio-reanalisis.html` | Formulario principal | 514 |
| `templates/calidad/reanalisis/inicio-reanalisis-ok.html` | Pantalla de exito | 144 |

### Backend
| Archivo | Descripcion | Lineas |
|---------|-------------|--------|
| `controller/cu/ModifReanalisisLoteController.java` | Controlador | 102 |
| `service/cu/ModifReanalisisLoteService.java` | Servicio de negocio | 136 |
| `service/cu/AbstractCuService.java` | Clase base con validaciones comunes | - |

### Validadores Especializados
| Archivo | Responsabilidad |
|---------|-----------------|
| `service/cu/validator/FechaValidator.java` | Validaciones de fechas |

### Utilidades de Creacion de Entidades
| Archivo | Uso en CU8 |
|---------|------------|
| `utils/MovimientoModificacionUtils.java` | Factory para crear Movimiento MODIFICACION |
| `utils/MovimientoCommonUtils.java` | Formateo de motivoDelCambio con prefijo CU |

### DTOs y Entidades
| Archivo | Uso en CU8 |
|---------|------------|
| `dto/MovimientoDTO.java` | DTO principal del formulario |
| `dto/LoteDTO.java` | DTO de respuesta |
| `entity/Lote.java` | Entidad Lote (actualiza listas) |
| `entity/Analisis.java` | Entidad Analisis (nuevo registro) |
| `entity/Movimiento.java` | Entidad Movimiento |

---

## 5. CAMPOS DEL FORMULARIO

### 5.1 Obligatorios

| Campo | HTML ID | Tipo | Validacion Browser | Validacion Server |
|-------|---------|------|-------------------|-------------------|
| Lote | codigoLote | select (Select2) | required | Existencia verificada |
| Fecha del Reanalisis | fechaMovimiento | date | required | @NotNull, @PastOrPresent |
| Numero de Reanalisis | nroReanalisis | text | required, maxlength=20 | @Size(max=20), Unico |
| Motivo del Cambio | motivoDelCambio | textarea | required, minlength=20 | @NotNull, @Size(min=20) |

---

## 6. VALIDACIONES POR CAPA

El sistema implementa validacion en 3 capas: Frontend (HTML5), Bean Validation (Spring), y Validacion de Negocio (Service).

### 6.1 Capa 1 - Frontend (HTML5 + JavaScript)

**Ubicacion:** `templates/calidad/reanalisis/inicio-reanalisis.html`

| Campo | Validacion HTML5 | Comportamiento |
|-------|------------------|----------------|
| Lote | required | Seleccion obligatoria |
| Fecha del Reanalisis | required, type=date | Campo obligatorio |
| Numero de Reanalisis | required, maxlength=20 | Maximo 20 caracteres |
| Motivo del Cambio | required, minlength=20 | Minimo 20 caracteres |

**JavaScript Dinamico:**
- Inicializa fecha con fecha actual (timezone Argentina)
- Select2 con tema Bootstrap 5
- Carga detalles del lote al seleccionar (desde array inyectado)
- Muestra historial de analisis previos del lote

### 6.2 Capa 2 - Bean Validation (Spring)

**Ubicacion:** `dto/MovimientoDTO.java`

| Campo DTO | Anotacion | Mensaje |
|-----------|-----------|---------|
| codigoLote | @NotNull | Lote requerido |
| fechaMovimiento | @NotNull, @PastOrPresent | Fecha invalida |
| nroAnalisis | @Size(max=20) | Nro analisis muy largo |
| motivoDelCambio | @NotNull, @Size(min=20) | Motivo muy corto |

### 6.3 Capa 3 - Validacion de Negocio (Service)

**Ubicacion:** `ModifReanalisisLoteService.java`

El metodo `validarReanalisisLoteInput()` ejecuta las siguientes validaciones en orden:

| Orden | Validacion | Mensaje Error |
|-------|------------|---------------|
| 1 | BindingResult previo | Errores de Bean Validation |
| 2 | Motivo del cambio >= 20 chars | El motivo del cambio es obligatorio (mínimo 20 caracteres) |
| 3 | Nro analisis no nulo | Ingrese un nro de analisis |
| 4a | Nro analisis unico (otro lote) | Nro de analisis ya registrado en otro lote |
| 4b | Nro analisis unico (mismo lote con dictamen) | Nro de analisis ya registrado en el mismo lote |
| 5 | Lote existe | Lote no encontrado |
| 6 | Fecha movimiento >= fecha ingreso | Fecha anterior a ingreso del lote |
| 7 | Fecha analisis >= fecha ingreso | Fecha anterior a ingreso del lote |

### 6.4 RESUMEN DE VALIDACIONES POR CAMPO

| Campo | Frontend | Bean Validation | Negocio |
|-------|----------|-----------------|---------|
| codigoLote | required | @NotNull | Existencia |
| fechaMovimiento | required | @NotNull, @PastOrPresent | >= fechaIngreso lote |
| nroReanalisis | required, maxlength | @Size(max=20) | No nulo, unico |
| motivoDelCambio | required, minlength | @NotNull, @Size(min=20) | >= 20 caracteres (ANMAT) |

---

## 7. TEMPLATE (Frontend)

### 7.1 Estilo Visual

El formulario utiliza la paleta de colores **purpura** que identifica las operaciones de calidad:

- **Fondo de pagina:** Gradiente purpura (`#8b5cf6 -> #7c3aed`)
- **Tarjeta del formulario:** Blanca con sombra suave
- **Botones primarios:** Gradiente purpura (`btn-custom-primary`)
- **Iconos de seccion:** Bootstrap Icons en blanco sobre fondo purpura
- **Badge CU:** `CU8` en purpura
- **Info box:** Fondo lavanda con borde purpura

### 7.2 Librerias Utilizadas

| Libreria | Proposito |
|----------|-----------|
| Bootstrap 5.3 | Framework CSS base, grid responsivo |
| Bootstrap Icons 1.11 | Iconografia en todo el formulario |
| jQuery 3.7.1 | Manipulacion DOM y eventos |
| Select2 4.1 | Dropdown con busqueda de lotes |

### 7.3 Comportamiento JavaScript

1. **Inicializacion de fecha:** Establece la fecha del reanalisis con la fecha actual usando timezone de Argentina.

2. **Configuracion de selector:** Convierte el select de Lote en componente Select2 con busqueda.

3. **Carga de detalle del lote:** Al seleccionar un lote, muestra informacion del producto, proveedor, fechas y analisis previos.

4. **Restauracion de datos:** Si el formulario se recarga por error de validacion, restaura el lote seleccionado y la fecha.

5. **Enfoque en error:** Automaticamente enfoca el primer campo con error de validacion.

6. **Prevencion de doble submit:** Al enviar el formulario, el boton se deshabilita y muestra spinner "Procesando...".

### 7.4 Interacciones Dinamicas

Este CU no consume endpoints AJAX adicionales. La logica dinamica se basa en datos inyectados via Thymeleaf:

| Dato | Uso |
|------|-----|
| `loteReanalisisDTOs` | Array JSON con lotes APROBADOS para poblar detalles al seleccionar |

---

## 8. CONTROLLER

### 8.1 Clase: ModifReanalisisLoteController

El controlador maneja el flujo de 2 pasos (formulario -> exito) y extiende `AbstractCuController` para heredar servicios comunes.

### 8.2 Flujo de Endpoints

| Metodo | URL | Descripcion |
|--------|-----|-------------|
| GET | /calidad/reanalisis/cancelar | Cancela operacion, redirige a home |
| GET | /calidad/reanalisis/inicio-reanalisis | Muestra formulario |
| POST | /calidad/reanalisis/inicio-reanalisis | Procesa reanalisis |
| GET | /calidad/reanalisis/inicio-reanalisis-ok | Pantalla exito |

### 8.3 Modelo en GET

- `loteReanalisisDTOs`: Lotes APROBADOS disponibles para reanalisis
- `movimientoDTO`: DTO del formulario (inicializado vacio)

### 8.4 Caracteristicas Destacadas

- **Flujo de 2 pasos:** Sin pantalla de confirmacion intermedia.
- **Flash Attributes:** El resultado (LoteDTO) se pasa a la pantalla de exito via RedirectAttributes.
- **Filtrado de lotes:** Solo muestra lotes elegibles para reanalisis (ver 8.5).

### 8.5 Pre-requisitos del Lote (Query findAllForReanalisisLote)

| Condicion | Descripcion |
|-----------|-------------|
| Activo | `activo = true` |
| Dictamen APROBADO | `dictamen = APROBADO` |
| Estado valido | `estado IN (NUEVO, DISPONIBLE, EN_USO)` |
| Sin analisis en curso | No debe existir un analisis con `dictamen = null` y `fechaRealizado = null` |

---

## 9. SERVICE

### 9.1 Clase: ModifReanalisisLoteService

El servicio contiene la logica de negocio del caso de uso. Extiende `AbstractCuService` para heredar validadores comunes y repositorios.

### 9.2 Metodo Principal: persistirReanalisisLote

Este metodo ejecuta toda la operacion en una unica transaccion. El flujo es:

1. **Establecer contexto de auditoria:** Registra "CU8_REANALISIS_LOTE" para Hibernate Envers.

2. **Obtener usuario actual:** Recupera el usuario autenticado.

3. **Buscar lote por codigo:** Obtiene el lote por codigoLote.

4. **Crear nuevo Analisis:** Crea un registro de Analisis con dictamen=null (En curso).

5. **Guardar analisis:** Persiste el nuevo analisis.

6. **Crear movimiento MODIFICACION/ANALISIS:** Genera movimiento con dictamenInicial = dictamenFinal = APROBADO (sin cambio).

7. **Actualizar listas del lote:** Agrega el movimiento y el analisis a las listas del lote.

8. **Guardar lote:** Persiste los cambios.

9. **Retornar LoteDTO:** Convierte y retorna el lote actualizado.

### 9.3 Movimiento Generado

El movimiento de modificacion incluye:

- **tipo:** MODIFICACION
- **motivo:** ANALISIS
- **fecha:** fechaMovimiento del formulario
- **dictamenInicial:** APROBADO
- **dictamenFinal:** APROBADO (sin cambio)
- **nroAnalisis:** Numero del nuevo analisis
- **motivoDelCambio:** Formateado con prefijo `[CU8]`

---

## 10. PERSISTENCIA

### 10.1 Entidades Afectadas

| Entidad | Accion | Estado Final |
|---------|--------|--------------|
| Lote | UPDATE (listas) | dictamen=APROBADO (sin cambio) |
| Analisis | INSERT | dictamen=null (En curso) |
| Movimiento | INSERT | tipo=MODIFICACION, motivo=ANALISIS |

### 10.2 Cascade y Soft Delete

El Analisis y Movimiento se guardan explicitamente. El Lote se actualiza para incluirlos en sus listas.

Todas las entidades usan borrado logico.

---

## 11. AUDITORIA (ANMAT)

El sistema cumple con los requerimientos de la Disposicion ANMAT 4159:

- **Hibernate Envers:** Todas las entidades (Lote, Analisis, Movimiento) estan auditadas. Cada cambio genera un registro en las tablas `_aud`.

- **Campo motivoDelCambio:** El movimiento incluye el motivo ingresado por el usuario (mínimo 20 caracteres). Cumple con Requerimiento 9 del Anexo 6.

- **Contexto de caso de uso:** El identificador "CU8_REANALISIS_LOTE" se guarda en cada revision de auditoria.

- **Prefijo de CU:** El motivoDelCambio incluye `[CU8]` para trazabilidad.

---

## 12. SEGURIDAD

- **CSRF:** El formulario incluye token CSRF como campo oculto. Meta tags disponibles para AJAX.
- **Autorizacion:** Solo usuarios con rol ADMIN o GERENTE_CONTROL_CALIDAD pueden acceder.
- **XSS:** Todos los datos se renderizan con escape automatico de HTML (th:text).
- **Validacion de lote:** Solo permite reanalizar lotes APROBADOS existentes.

---

## 13. MANEJO DE ERRORES

Los errores se manejan en diferentes niveles:

- **Service:** Si el lote no existe, el nroAnalisis esta duplicado, o las fechas son invalidas, se rechaza con mensaje descriptivo.
- **Controller:** Los errores de validacion se muestran junto a cada campo en el formulario.
- **Excepciones no controladas:** Redirigen a la pagina de error global del sistema.

---

## 14. LOGGING

El service registra eventos en los siguientes puntos:

- **Inicio:** `[CU8] Iniciando reanalisis de lote - Usuario: {}, Lote: {}, NuevoAnalisis: {}`
- **Completado:** `[CU8] Reanalisis completado - Lote: {}, NuevoAnalisis: {}, MotivoDelCambio: {}`

Todos los logs usan el prefijo `[CU8]` para facilitar la busqueda.

---

## 15. MEJORAS SUGERIDAS

| Prioridad | Mejora |
|-----------|--------|
| BAJA | Agregar pantalla de confirmacion intermedia para consistencia con otros CUs |

---

*Documento generado: 2025-12-13*
*Version: 1.5*
