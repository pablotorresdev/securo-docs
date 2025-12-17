# CU40 - ABM PROVEEDORES: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-06
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** ABM (Alta/Baja/Modificacion)
**Categoria:** Datos Maestros

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Usuarios Autorizados](#2-usuarios-autorizados)
3. [Operaciones Disponibles](#3-operaciones-disponibles)
4. [Validaciones](#4-validaciones)
5. [Campos del Formulario](#5-campos-del-formulario)
6. [Flujos del Proceso](#6-flujos-del-proceso)
7. [Casos de Uso Ejemplos](#7-casos-de-uso-ejemplos)
8. [Preguntas Frecuentes](#8-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite gestionar el catalogo de **Proveedores** del sistema. Los proveedores son entidades externas que suministran materiales (APIs, excipientes, acondicionamiento) o internas (Conifarma para produccion propia).

### 1.2 Importancia en el Sistema

Los proveedores son **datos maestros criticos** porque:
- Son referenciados por todos los lotes de compra (CU1)
- El proveedor "Conifarma" identifica produccion interna (CU20)
- El pais del proveedor puede determinar el pais de origen del lote
- Son auditables segun normativa ANMAT

### 1.3 Proveedor Especial: Conifarma

| Atributo | Valor |
|----------|-------|
| Razon Social | Conifarma S.A. |
| Tipo | Proveedor interno |
| Uso | Produccion propia (CU20) |
| Restriccion | NO puede usarse en CU1 (Ingreso Compra) |

---

## 2. Usuarios Autorizados

### 2.1 Roles con Acceso

| Rol | Nivel | Alta | Modificacion | Baja | Consulta |
|-----|-------|------|--------------|------|----------|
| ADMIN | 6 | SI | SI | SI | SI |
| ANALISTA_PLANTA | 2 | SI | SI | SI | SI |

### 2.2 Roles Sin Acceso

| Rol | Motivo |
|-----|--------|
| DT | Solo operaciones de liberacion |
| GERENTE_GARANTIA_CALIDAD | Solo operaciones de calidad |
| GERENTE_CONTROL_CALIDAD | Solo operaciones de calidad |
| SUPERVISOR_PLANTA | Solo operaciones de produccion |
| ANALISTA_CONTROL_CALIDAD | Solo operaciones de calidad |
| AUDITOR | Solo lectura (no puede modificar) |

---

## 3. Operaciones Disponibles

### 3.1 Alta de Proveedor

| Aspecto | Detalle |
|---------|---------|
| URL | `/proveedores/add-proveedor` |
| Metodo HTTP | GET (formulario), POST (guardar) |
| Resultado | Nuevo proveedor con activo=true |

### 3.2 Modificacion de Proveedor

| Aspecto | Detalle |
|---------|---------|
| URL | `/proveedores/edit-proveedor/{id}` |
| Metodo HTTP | GET (formulario), POST (guardar) |
| Restriccion | Solo proveedores activos |

### 3.3 Baja de Proveedor (Soft Delete)

| Aspecto | Detalle |
|---------|---------|
| URL | `/proveedores/delete-proveedor` |
| Metodo HTTP | POST |
| Efecto | Marca activo=false (no elimina fisicamente) |
| Restriccion | No puede tener lotes activos asociados |

### 3.4 Listado de Proveedores

| Aspecto | Detalle |
|---------|---------|
| URL | `/proveedores/list-proveedores` |
| Metodo HTTP | GET |
| Filtro | Solo proveedores con activo=true |

---

## 4. Validaciones

### 4.1 Validaciones de Campo

| Campo | Regla | Mensaje de Error |
|-------|-------|------------------|
| Razon Social | Obligatorio | "La razon social es obligatoria" |
| CUIT | Obligatorio | "El CUIT es obligatorio" |
| Direccion | Obligatorio | "La direccion es obligatoria" |
| Ciudad | Obligatorio, max 100 chars | "La ciudad es obligatoria" |
| Pais | Obligatorio, max 100 chars | "El pais es obligatorio" |
| Telefono | Opcional, max 50 chars | - |
| Email | Opcional, max 100 chars | - |
| Contacto | Opcional, max 100 chars | - |
| Motivo del cambio | Opcional, max 300 chars | - |

### 4.2 Validaciones de Negocio

| Validacion | Momento | Consecuencia |
|------------|---------|--------------|
| Proveedor activo | Modificacion | Solo permite editar activos |
| Sin lotes asociados | Baja | No permite baja si tiene lotes |

**Nota:** Actualmente NO se valida la unicidad de CUIT. Ver mejoras sugeridas.

---

## 5. Campos del Formulario

### 5.1 Campos Obligatorios

| Campo | Tipo | Formato | Ejemplo |
|-------|------|---------|---------|
| Razon Social | Texto | Libre | "Sigma-Aldrich Argentina S.A." |
| CUIT | Texto | XX-XXXXXXXX-X | "30-12345678-9" |
| Direccion | Texto | Libre | "Av. Corrientes 1234" |
| Ciudad | Texto | Max 100 chars | "Buenos Aires" |
| Pais | Texto | Max 100 chars | "Argentina" |

### 5.2 Campos Opcionales

| Campo              | Tipo | Formato | Uso |
|--------------------|------|---------|-----|
| Telefono           | Texto | Max 50 chars | Contacto telefonico |
| Email              | Texto | Max 100 chars | Contacto electronico |
| Contacto           | Texto | Max 100 chars | Nombre de persona de contacto |
| Detalles Proveedor | Texto | Max 300 chars | Notas adicionales |

### 5.3 Campos Automaticos

| Campo | Valor | Momento |
|-------|-------|---------|
| activo | true | Alta |
| activo | true | Modificacion |
| activo | false | Baja |

---

## 6. Flujos del Proceso

### 6.1 Flujo de Alta

```
[Usuario accede a /proveedores/add-proveedor]
       |
       v
[Sistema muestra formulario vacio]
       |
       v
[Usuario completa campos obligatorios]
       |
       v
[Usuario hace clic en Guardar]
       |
       v
[Sistema valida campos]
       |
       +---> (Errores) ---> [Muestra errores en formulario]
       |
       v
[Verifica CUIT unico]
       |
       +---> (Duplicado) ---> [Error: "Ya existe proveedor con ese CUIT"]
       |
       v
[Guarda proveedor con activo=true]
       |
       v
[Redirige a listado con mensaje exito]
```

### 6.2 Flujo de Modificacion

```
[Usuario accede a /proveedores/edit-proveedor/{id}]
       |
       v
[Sistema carga datos del proveedor]
       |
       +---> (No existe o inactivo) ---> [Error 404]
       |
       v
[Sistema muestra formulario con datos]
       |
       v
[Usuario modifica campos]
       |
       v
[Usuario hace clic en Guardar]
       |
       v
[Sistema valida campos]
       |
       v
[Guarda cambios]
       |
       v
[Redirige a listado con mensaje exito]
```

### 6.3 Flujo de Baja

```
[Usuario hace clic en Eliminar desde listado]
       |
       v
[Sistema verifica si tiene lotes asociados]
       |
       +---> (Tiene lotes) ---> [Error: "No se puede eliminar"]
       |
       v
[Marca proveedor como activo=false]
       |
       v
[Redirige a listado con mensaje exito]
```

---

## 7. Casos de Uso Ejemplos

### 7.1 Alta de Proveedor Nacional

**Situacion:** Se incorpora un nuevo proveedor de excipientes argentino.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Razon Social | Drogueria del Sud S.A. |
| CUIT | 30-98765432-1 |
| Direccion | Av. Belgrano 5678 |
| Ciudad | Cordoba |
| Pais | Argentina |
| Telefono | +54 351 4567890 |
| Email | compras@drogueriadelsud.com.ar |
| Contacto | Juan Perez |

**Resultado:** Proveedor creado, disponible para CU1.

### 7.2 Alta de Proveedor Internacional

**Situacion:** Se incorpora un proveedor de APIs de India.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Razon Social | Cipla Pharmaceuticals Ltd. |
| CUIT | 33-00000001-0 |
| Direccion | 1234 Industrial Area |
| Ciudad | Mumbai |
| Pais | India |
| Detalles Proveedor | Proveedor de APIs certificado |

**Resultado:** Proveedor creado. El pais "India" sera usado como pais de origen por defecto en CU1.

### 7.3 Baja de Proveedor sin Uso

**Situacion:** Un proveedor deja de operar y no tiene lotes en el sistema.

**Accion:** Analista ejecuta Eliminar desde listado.

**Resultado:** Proveedor marcado como inactivo. Ya no aparece en listados ni selectores.

### 7.4 Intento de Baja con Lotes Asociados

**Situacion:** Se intenta eliminar un proveedor que tiene lotes activos.

**Resultado:** Sistema rechaza la operacion con mensaje de error.

---

## 8. Preguntas Frecuentes

### 8.1 Sobre el Proveedor

**P: ¿Puedo eliminar fisicamente un proveedor?**
R: No. El sistema usa soft delete (activo=false) para mantener la trazabilidad historica.

**P: ¿Que pasa con los lotes si doy de baja un proveedor?**
R: Los lotes existentes mantienen la referencia al proveedor. Solo no podras crear nuevos lotes con ese proveedor.

**P: ¿Puedo reactivar un proveedor dado de baja?**
R: No existe esa funcionalidad directa. Se debe crear como nuevo proveedor.

### 8.2 Sobre el CUIT

**P: ¿El CUIT debe tener formato especifico?**
R: El sistema no valida formato de CUIT, solo unicidad.

**P: ¿Puedo tener dos proveedores con el mismo CUIT?**
R: No. El CUIT es unico en el sistema.

### 8.3 Sobre Conifarma

**P: ¿Puedo modificar el proveedor Conifarma?**
R: Si, pero no se recomienda ya que es el proveedor interno del sistema.

**P: ¿Puedo usar Conifarma en CU1?**
R: No. CU1 valida que el proveedor NO sea Conifarma.

---

## Resumen de Puntos Clave

1. **Tipo:** ABM de datos maestros
2. **Usuarios:** ADMIN, ANALISTA_PLANTA
3. **Operaciones:** Alta, Modificacion, Baja (soft delete), Listado
4. **Campo unico:** CUIT
5. **Soft delete:** activo=false
6. **Proveedor especial:** Conifarma (interno)
7. **Restriccion baja:** No puede tener lotes asociados
8. **Auditoria:** Registro de cambios para ANMAT

---

## Relacion con Otros CUs

### CUs que Requieren Proveedor

| CU | Nombre | Uso del Proveedor |
|----|--------|-------------------|
| CU1 | Ingreso Compra | Proveedor del lote (NO Conifarma) |
| CU20 | Ingreso Produccion | Solo Conifarma |

### Impacto en Lotes

- El **pais** del proveedor se usa como default para pais de origen del lote
- El proveedor queda registrado permanentemente en el lote
- Cambios al proveedor NO afectan lotes ya creados

---

**Fin del Documento - CU40_ABM_PROVEEDORES v1.0 - Especificacion Funcional**
