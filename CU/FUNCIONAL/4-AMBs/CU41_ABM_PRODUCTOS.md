# CU41 - ABM PRODUCTOS: Especificacion Funcional

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
4. [Tipos de Producto](#4-tipos-de-producto)
5. [Validaciones](#5-validaciones)
6. [Campos del Formulario](#6-campos-del-formulario)
7. [Flujos del Proceso](#7-flujos-del-proceso)
8. [Casos de Uso Ejemplos](#8-casos-de-uso-ejemplos)
9. [Preguntas Frecuentes](#9-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite gestionar el catalogo de **Productos** del sistema. Los productos definen los materiales que pueden ser ingresados, consumidos, producidos y vendidos.

### 1.2 Importancia en el Sistema

Los productos son **datos maestros fundamentales** porque:
- Determinan que tipo de lotes pueden crearse
- Definen la unidad de medida base para cantidades
- Establecen relaciones de genealogia (producto destino)
- Controlan que operaciones son validas (ej: solo UNIDAD_VENTA puede venderse)

### 1.3 Tipos de Producto Soportados

| Grupo | Tipos | Descripcion |
|-------|-------|-------------|
| Materia Prima | API, EXCIPIENTE | Ingredientes farmaceuticos |
| Acondicionamiento | ACOND_PRIMARIO, ACOND_SECUNDARIO | Materiales de empaque |
| Semielaborados | SEMIELABORADO, GRANEL_* | Productos intermedios |
| Producto Final | UNIDAD_VENTA | Producto terminado para venta |

---

## 2. Usuarios Autorizados

### 2.1 Roles con Acceso

| Rol | Nivel | Alta | Modificacion | Baja | Consulta |
|-----|-------|------|--------------|------|----------|
| ADMIN | 6 | SI | SI | SI | SI |
| GERENTE_CONTROL_CALIDAD | 3 | SI | SI | SI | SI |

### 2.2 Roles Sin Acceso

| Rol | Motivo |
|-----|--------|
| DT | Solo operaciones de liberacion |
| GERENTE_GARANTIA_CALIDAD | Solo operaciones de calidad post-analisis |
| SUPERVISOR_PLANTA | Solo operaciones de produccion |
| ANALISTA_CONTROL_CALIDAD | Solo operaciones de analisis |
| ANALISTA_PLANTA | Solo operaciones de planta |
| AUDITOR | Solo lectura (no puede modificar) |

---

## 3. Operaciones Disponibles

### 3.1 Alta de Producto

| Aspecto | Detalle |
|---------|---------|
| URL | `/productos/add-producto` |
| Metodo HTTP | GET (formulario), POST (guardar) |
| Resultado | Nuevo producto con activo=true |

### 3.2 Modificacion de Producto

| Aspecto | Detalle |
|---------|---------|
| URL | `/productos/edit-producto/{id}` |
| Metodo HTTP | GET (formulario), POST (guardar) |
| Restriccion | Solo productos activos |

### 3.3 Baja de Producto (Soft Delete)

| Aspecto | Detalle |
|---------|---------|
| URL | `/productos/delete-producto` |
| Metodo HTTP | POST |
| Efecto | Marca activo=false |
| Restriccion | No puede tener lotes activos asociados |

### 3.4 Listado de Productos

| Aspecto | Detalle |
|---------|---------|
| URL | `/productos/list-productos` |
| Metodo HTTP | GET |
| Filtro | Solo productos con activo=true |

---

## 4. Tipos de Producto

### 4.1 Catalogo Completo

| Tipo | Grupo | Codigo | Requiere Producto Destino |
|------|-------|--------|---------------------------|
| API | MatPrima | 1 | SI |
| EXCIPIENTE | MatPrima | 1 | NO |
| ACOND_PRIMARIO | MatAcond | 2 | NO |
| ACOND_SECUNDARIO | MatAcond | 2 | SI |
| SEMIELABORADO | SemiElab | 6 | SI |
| GRANEL_MEZCLA_POLVO | SemiElab | 6 | SI |
| GRANEL_CAPSULAS | SemiElab | 6 | SI |
| GRANEL_COMPRIMIDOS | SemiElab | 6 | SI |
| GRANEL_FRASCOS | SemiElab | 6 | SI |
| UNIDAD_VENTA | UniVenta | 9 | NO |

### 4.2 Producto Destino

Algunos tipos de producto **requieren** indicar a que producto final contribuyen:

| Tipo Origen | Producto Destino Requerido | Ejemplo |
|-------------|---------------------------|---------|
| API | SI | API "Paracetamol" → destino "Tafirol 500mg" |
| ACOND_SECUNDARIO | SI | Caja → destino "Tafirol 500mg x 20" |
| SEMIELABORADO | SI | Granel capsulas → destino "Tafirol 500mg" |

### 4.3 Restricciones por Tipo

| Tipo | CU1 (Compra) | CU20 (Produccion) | CU7 (Consumo) | CU23 (Venta) |
|------|--------------|-------------------|---------------|--------------|
| API | SI | NO | SI | NO |
| EXCIPIENTE | SI | NO | SI | NO |
| ACOND_* | SI | NO | SI | NO |
| SEMIELABORADO | NO | SI | SI | NO |
| UNIDAD_VENTA | NO | SI | NO | SI |

---

## 5. Validaciones

### 5.1 Validaciones de Campo

| Campo | Regla | Mensaje de Error |
|-------|-------|------------------|
| Nombre Generico | Obligatorio | "El nombre generico es obligatorio" |
| Codigo Producto | Obligatorio, max 50 chars | "El codigo de producto es obligatorio" |
| Tipo Producto | Obligatorio | "El tipo de producto es obligatorio" |
| Unidad Medida | Obligatorio | "La unidad de medida es obligatoria" |
| Producto Destino | Condicional | "El producto destino es obligatorio para este tipo" |

### 5.2 Restriccion de Unicidad

La combinacion de estos tres campos debe ser **unica**:
- Nombre Generico
- Codigo Producto
- Tipo Producto

**Ejemplo valido:**
- "Paracetamol" + "1-05701" + API
- "Paracetamol" + "1-05701" + SEMIELABORADO (diferente tipo)

**Ejemplo invalido:**
- "Paracetamol" + "1-05701" + API (ya existe)

### 5.3 Validaciones de Negocio

| Validacion | Momento | Consecuencia |
|------------|---------|--------------|
| Unicidad (nombre+codigo+tipo) | Alta/Modificacion | Rechaza operacion |
| Producto destino requerido | Alta/Modificacion | Rechaza si falta |
| Producto activo | Modificacion | Solo permite editar activos |

**Nota:** Actualmente NO se valida si el producto tiene lotes asociados antes de la baja. Ver mejoras sugeridas.

---

## 6. Campos del Formulario

### 6.1 Campos Obligatorios

| Campo | Tipo | Formato | Ejemplo |
|-------|------|---------|---------|
| Nombre Generico | Texto | Libre | "Paracetamol 500mg" |
| Codigo Producto | Texto | Max 50 chars | "1-05701" |
| Tipo Producto | Selector | TipoProductoEnum | API |
| Unidad Medida | Selector | UnidadMedidaEnum | KILOGRAMO |

### 6.2 Campos Condicionales

| Campo | Condicion | Tipo |
|-------|-----------|------|
| Producto Destino | Tipo requiere destino | Selector de productos UNIDAD_VENTA |

### 6.3 Campos Opcionales

| Campo             | Tipo | Formato | Uso |
|-------------------|------|---------|-----|
| Detalles Producto | Texto | TEXT | Notas adicionales |

### 6.4 Campos Automaticos

| Campo | Valor | Momento |
|-------|-------|---------|
| activo | true | Alta |
| activo | true | Modificacion |
| activo | false | Baja |

---

## 7. Flujos del Proceso

### 7.1 Flujo de Alta

```
[Usuario accede a /productos/add-producto]
       |
       v
[Sistema muestra formulario vacio]
       |
       v
[Usuario selecciona Tipo Producto]
       |
       v
[Sistema muestra/oculta Producto Destino segun tipo]
       |
       v
[Usuario completa campos]
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
[Verifica unicidad (nombre+codigo+tipo)]
       |
       +---> (Duplicado) ---> [Error: "Ya existe producto con esos datos"]
       |
       v
[Verifica producto destino si requerido]
       |
       +---> (Falta) ---> [Error: "Producto destino obligatorio"]
       |
       v
[Guarda producto con activo=true]
       |
       v
[Redirige a listado con mensaje exito]
```

### 7.2 Flujo de Modificacion

```
[Usuario accede a /productos/edit-producto/{id}]
       |
       v
[Sistema carga datos del producto]
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
[Sistema valida campos y unicidad]
       |
       v
[Guarda cambios]
       |
       v
[Redirige a listado con mensaje exito]
```

### 7.3 Flujo de Baja

```
[Usuario hace clic en Eliminar desde listado]
       |
       v
[Sistema verifica si tiene lotes asociados]
       |
       +---> (Tiene lotes) ---> [Error: "No se puede eliminar"]
       |
       v
[Marca producto como activo=false]
       |
       v
[Redirige a listado con mensaje exito]
```

---

## 8. Casos de Uso Ejemplos

### 8.1 Alta de API con Producto Destino

**Situacion:** Se da de alta un nuevo API que sera usado para producir un medicamento.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Nombre Generico | Paracetamol DC 90 |
| Codigo Producto | API-PAR-001 |
| Tipo Producto | API |
| Unidad Medida | KILOGRAMO |
| Producto Destino | Tafirol 500mg x 20 comp |
| Detalles Producto | Paracetamol compactado directo 90% pureza |

**Resultado:** Producto creado. Disponible para CU1 (Ingreso Compra).

### 8.2 Alta de Unidad de Venta

**Situacion:** Se da de alta un nuevo producto terminado.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Nombre Generico | Tafirol 500mg x 20 comprimidos |
| Codigo Producto | UDV-TAF-500-20 |
| Tipo Producto | UNIDAD_VENTA |
| Unidad Medida | UNIDAD |
| Producto Destino | (no aplica) |

**Resultado:** Producto creado. Disponible para CU20 (Ingreso Produccion) y CU23 (Venta).

### 8.3 Alta de Semielaborado

**Situacion:** Se define un producto intermedio de produccion.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Nombre Generico | Granel capsulas Tafirol 500 |
| Codigo Producto | GRN-TAF-500 |
| Tipo Producto | GRANEL_CAPSULAS |
| Unidad Medida | UNIDAD |
| Producto Destino | Tafirol 500mg x 20 comprimidos |

**Resultado:** Semielaborado creado. Disponible para CU20 y CU7.

### 8.4 Intento de Alta Duplicada

**Situacion:** Se intenta crear un producto con misma combinacion nombre+codigo+tipo.

**Resultado:** Sistema rechaza con error "Ya existe un producto con esos datos".

---

## 9. Preguntas Frecuentes

### 9.1 Sobre los Productos

**P: ¿Puedo cambiar el tipo de un producto existente?**
R: Tecnicamente si, pero no se recomienda si ya tiene lotes asociados.

**P: ¿Que pasa si elimino un producto con lotes?**
R: El sistema NO permite eliminar productos con lotes asociados.

**P: ¿Puedo reactivar un producto eliminado?**
R: No existe esa funcionalidad directa. Se debe crear como nuevo producto.

### 9.2 Sobre Unidades de Medida

**P: ¿Puedo cambiar la unidad de medida de un producto?**
R: Si, pero afectara la interpretacion de las cantidades en lotes futuros.

**P: ¿Que unidades puedo usar?**
R: Masa (kg, g, mg), Volumen (L, mL), Unidad generica, y otras segun UnidadMedidaEnum.

### 9.3 Sobre Producto Destino

**P: ¿Que es el producto destino?**
R: Es el producto final al que contribuye una materia prima o semielaborado. Permite establecer relaciones de genealogia.

**P: ¿Todos los productos requieren destino?**
R: No. Solo API, ACOND_SECUNDARIO y SEMIELABORADOS requieren producto destino.

### 9.4 Sobre Tipos de Producto

**P: ¿Que diferencia hay entre SEMIELABORADO y GRANEL_*?**
R: GRANEL_* es mas especifico (capsulas, comprimidos, etc.). Ambos son del grupo SemiElab.

**P: ¿Por que UNIDAD_VENTA no puede usarse en CU1?**
R: Porque UNIDAD_VENTA es produccion interna (Conifarma), no compra externa.

---

## Resumen de Puntos Clave

1. **Tipo:** ABM de datos maestros
2. **Usuarios:** ADMIN, GERENTE_CONTROL_CALIDAD
3. **Operaciones:** Alta, Modificacion, Baja (soft delete), Listado
4. **Unicidad:** Combinacion (nombre + codigo + tipo)
5. **Tipos:** 10 tipos de producto en 4 grupos
6. **Producto destino:** Requerido para API, ACOND_SECUNDARIO, SEMIELABORADOS
7. **Restriccion baja:** No puede tener lotes asociados

---

## Relacion con Otros CUs

### CUs que Usan Productos

| CU | Tipos Permitidos | Uso |
|----|------------------|-----|
| CU1 | API, EXCIPIENTE, ACOND_* | Ingreso por compra |
| CU20 | SEMIELABORADO, UNIDAD_VENTA | Ingreso por produccion |
| CU7 | Todos excepto UNIDAD_VENTA | Consumo para produccion |
| CU23 | Solo UNIDAD_VENTA | Venta de producto |

### Jerarquia de Productos

```
UNIDAD_VENTA
    ↑
    |-- SEMIELABORADO / GRANEL_*
    |       ↑
    |       |-- API
    |       |-- EXCIPIENTE
    |
    |-- ACOND_PRIMARIO
    |-- ACOND_SECUNDARIO
```

---

**Fin del Documento - CU41_ABM_PRODUCTOS v1.0 - Especificacion Funcional**
