# CU20 - INGRESO PRODUCCION: Especificacion Funcional

**Version:** 1.0
**Fecha:** 2025-12-07
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** ALTA
**Motivo:** PRODUCCION_PROPIA

---

## Tabla de Contenidos

1. [Descripcion General](#1-descripcion-general)
2. [Perfiles de Usuario](#2-perfiles-de-usuario)
3. [Pre-condiciones](#3-pre-condiciones)
4. [Formulario de Ingreso](#4-formulario-de-ingreso)
5. [Validaciones](#5-validaciones)
6. [Resultado de la Operacion](#6-resultado-de-la-operacion)
7. [Flujo de Pantallas](#7-flujo-de-pantallas)
8. [Operaciones Posteriores](#8-operaciones-posteriores)
9. [Casos de Uso Ejemplos](#9-casos-de-uso-ejemplos)
10. [Preguntas Frecuentes](#10-preguntas-frecuentes)

---

## 1. Descripcion General

### 1.1 Proposito

Este caso de uso permite registrar el **ingreso de stock por produccion interna**. Cuando Conifarma fabrica un producto (semielaborado, granel o unidad de venta), se crea un nuevo lote en el sistema con las cantidades producidas.

### 1.2 Cuando Usar Este CU

Usar CU20 cuando:
- Se completa la fabricacion de un lote de semielaborado
- Se completa la fabricacion de un lote de producto a granel
- Se completa la fabricacion de un lote de unidades de venta
- Se necesita dar de alta producto fabricado internamente

**NO usar CU20 cuando:**
- El producto proviene de un proveedor externo → usar **CU1 (Alta Ingreso Compra)**
- Se quiere ajustar stock existente → usar **CU30 (Ajuste Stock)**
- Se quiere registrar una devolucion de cliente → usar **CU24 (Devolucion Venta)**

### 1.3 Diferencia con CU1 (Ingreso Compra)

| Aspecto | CU1 - Ingreso Compra | CU20 - Ingreso Produccion |
|---------|----------------------|---------------------------|
| Origen | Proveedor externo | Produccion interna (Conifarma) |
| Proveedor | Seleccionable | Siempre "Conifarma" |
| Productos | API, Excipiente, Acondicionamiento | Semielaborado, Granel, Unidad de Venta |
| Dictamen inicial | RECIBIDO | RECIBIDO |
| Requiere analisis | Si, antes de usar | Si, antes de usar/vender |

### 1.4 Resultado del CU

Al completar exitosamente:
- Se crea un **nuevo lote** con el producto seleccionado
- El proveedor se asigna automaticamente como **Conifarma**
- Se crean los **bultos** segun la distribucion indicada
- El lote queda con dictamen **RECIBIDO** (pendiente de analisis)
- Se registra un **movimiento de ALTA** con motivo PRODUCCION_PROPIA
- Para Unidades de Venta: se generan las **trazas** correspondientes

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Supervisor de Planta | SI | Usuario principal para esta operacion |
| Administrador | SI | Acceso completo al sistema |
| Analista de Planta | NO | No tiene acceso a registro de produccion |
| Analista Control Calidad | NO | Su rol es analizar, no registrar produccion |
| Gerentes | NO | Roles de supervision, no operativos |
| DT | NO | Rol de aprobacion final |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar que la produccion fisica esta completa
- Registrar correctamente las cantidades producidas
- Distribuir correctamente las cantidades entre bultos
- Indicar la orden de produccion correspondiente
- Documentar el motivo del ingreso (mínimo 20 caracteres)
- Para Unidades de Venta: indicar el numero de traza inicial

---

## 3. Pre-condiciones

### 3.1 Requisitos del Sistema

| Condicion | Descripcion |
|-----------|-------------|
| **Proveedor Conifarma** | Debe existir el proveedor "Conifarma" en el sistema |
| **Productos internos** | Debe existir al menos un producto de tipo interno |

### 3.2 Tipos de Producto Permitidos

Solo se pueden registrar productos de fabricacion interna:

| Tipo de Producto | Permitido | Descripcion |
|------------------|-----------|-------------|
| Semielaborado | SI | Producto intermedio de fabricacion |
| Granel Comprimidos | SI | Comprimidos a granel sin acondicionar |
| Granel Frascos | SI | Producto liquido a granel |
| Unidad de Venta | SI | Producto terminado listo para venta |
| API | NO | Se compra a proveedor (CU1) |
| Excipiente | NO | Se compra a proveedor (CU1) |
| Acond. Primario | NO | Se compra a proveedor (CU1) |
| Acond. Secundario | NO | Se compra a proveedor (CU1) |

### 3.3 Estado Inicial del Lote

El lote creado inicia con:

| Atributo | Valor |
|----------|-------|
| Dictamen | RECIBIDO |
| Estado | NUEVO |
| Proveedor | Conifarma |
| Activo | SI |

---

## 4. Formulario de Ingreso

### 4.1 Campos Principales

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Fecha de Ingreso** | SI | Fecha | Fecha de finalizacion de produccion | Hoy |
| **Orden de Produccion** | SI | Texto | Numero de la orden de produccion | - |
| **Producto** | SI | Selector | Producto fabricado | - |
| **Codigo de Lote (Sufijo)** | SI | Texto | Sufijo del codigo de lote (ver formato abajo) | - |
| **Lote Proveedor** | SI | Texto | Identificador del lote interno | - |
| **Cantidad Total** | SI | Numero | Cantidad total producida | - |
| **Unidad de Medida** | SI | Selector | Unidad de la cantidad | Segun producto |
| **Cantidad de Bultos** | SI | Numero | Numero de bultos (1-15) | 1 |

### 4.1.1 Formato del Codigo de Lote

El codigo de lote se compone de dos partes:

```
{CodigoProducto}_{YYMM####S}
```

| Parte | Descripcion | Ejemplo |
|-------|-------------|---------|
| CodigoProducto | Codigo del producto seleccionado (automatico) | 9-05701 |
| _ | Separador | _ |
| YY | Ano (2 digitos) | 25 |
| MM | Mes (01-12, validado) | 01 |
| #### | Secuencial (4 digitos) | 0001 |
| S | Letra identificadora (A-Z) | A |

**Ejemplo completo:** `9-05701_25010001A`

**Notas:**
- El prefijo (CodigoProducto_) se muestra automaticamente al seleccionar producto
- Solo se ingresa el sufijo (YYMM####S)
- La letra se convierte automaticamente a mayuscula
- El sistema valida que el codigo completo sea unico

### 4.2 Distribucion por Bulto (si bultos > 1)

Cuando se indica mas de un bulto, aparece seccion adicional:

| Campo | Obligatorio | Tipo | Descripcion |
|-------|-------------|------|-------------|
| **Cantidad Bulto N** | SI | Numero | Cantidad en cada bulto |
| **Unidad Bulto N** | SI | Selector | Unidad de cada bulto |

**Nota:** La suma de las cantidades de todos los bultos debe ser igual a la cantidad total.

### 4.3 Campos Adicionales (Opcionales)

| Campo | Obligatorio | Tipo | Descripcion |
|-------|-------------|------|-------------|
| **Detalle de Conservacion** | NO | Texto | Condiciones especiales de almacenamiento |
| **Motivo del Cambio** | SI | Texto | Descripcion del ingreso (min 20 caracteres) |

### 4.4 Campo Especial: Traza Inicial (Solo Unidad de Venta)

Para productos de tipo **UNIDAD_VENTA** con unidad **UNIDAD**:

| Campo | Obligatorio | Tipo | Descripcion |
|-------|-------------|------|-------------|
| **Traza Inicial** | Condicional | Numero | Numero de traza inicial para las unidades |

El sistema valida que el numero sea mayor al ultimo registrado para ese producto.

---

## 5. Validaciones

### 5.1 Validaciones del Formulario

| # | Campo | Validacion | Mensaje de Error |
|---|-------|------------|------------------|
| 1 | Fecha de Ingreso | Debe ingresar fecha | "La fecha es obligatoria" |
| 2 | Orden de Produccion | Debe ingresar valor | "La orden de produccion es obligatoria" |
| 3 | Producto | Debe seleccionar uno | "El producto es obligatorio" |
| 4 | Codigo de Lote | Debe ingresar valor | "El codigo de lote es obligatorio" |
| 5 | Codigo de Lote | Formato YYMM####S | "El codigo debe tener formato YYMM####S (ej: 25010001A)" |
| 6 | Codigo de Lote | MM entre 01 y 12 | "El mes debe estar entre 01 y 12" |
| 7 | Codigo de Lote | Codigo unico en sistema | "El codigo de lote 'XXX' ya existe en el sistema" |
| 8 | Lote Proveedor | Debe ingresar valor | "El lote es obligatorio" |
| 9 | Cantidad Total | Debe ser mayor a 0 | "La cantidad debe ser mayor a cero" |
| 10 | Unidad de Medida | Debe seleccionar una | "La unidad de medida es obligatoria" |
| 11 | Cantidad de Bultos | Entre 1 y 15 | "La cantidad de bultos debe estar entre 1 y 15" |
| 12 | Motivo del Cambio | Debe ingresar valor | "El motivo del cambio es obligatorio" |
| 13 | Motivo del Cambio | Minimo 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |
| 14 | Motivo del Cambio | Maximo 300 caracteres | "El motivo no puede exceder 300 caracteres" |

### 5.2 Validaciones de Bultos

| Validacion | Mensaje de Error |
|------------|------------------|
| La suma de cantidades de bultos debe igualar la cantidad total | "La suma de los bultos debe ser igual a la cantidad total" |
| Cada bulto debe tener cantidad mayor a 0 | "Cada bulto debe tener cantidad mayor a cero" |
| La unidad de cada bulto debe ser compatible | "Unidad de medida no compatible" |

### 5.3 Validaciones de Traza (Solo Unidad de Venta)

| Validacion | Mensaje de Error |
|------------|------------------|
| Solo aplica a productos con unidad UNIDAD | "El numero de traza solo aplica a unidades de venta" |
| Debe ser mayor al ultimo registrado para el producto | "El numero de traza debe ser mayor al ultimo registrado. {ultimo}" |

---

## 6. Resultado de la Operacion

### 6.1 Lote Creado

| Atributo | Valor |
|----------|-------|
| Codigo Lote | {CodigoProducto}_{Sufijo ingresado} - ej: 9-05701_25010001A |
| Producto | Producto seleccionado |
| Proveedor | Conifarma |
| Lote Proveedor | Valor ingresado |
| Cantidad Inicial | Cantidad total ingresada |
| Cantidad Actual | Cantidad total ingresada |
| Unidad de Medida | Unidad seleccionada |
| Fecha de Ingreso | Fecha ingresada |
| Dictamen | RECIBIDO |
| Estado | NUEVO |
| Activo | SI |

### 6.2 Bultos Creados

Para cada bulto (segun cantidad indicada):

| Atributo | Valor |
|----------|-------|
| Numero de Bulto | 1, 2, 3... |
| Cantidad Inicial | Cantidad del bulto |
| Cantidad Actual | Cantidad del bulto |
| Unidad de Medida | Unidad del bulto |
| Estado | NUEVO |
| Activo | SI |

### 6.3 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | ALTA |
| Motivo | PRODUCCION_PROPIA |
| Cantidad | Cantidad total |
| Unidad de Medida | Unidad del lote |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de ingreso indicada |
| Motivo del cambio | Motivo del cambio ingresado |
| Activo | SI |

### 6.4 Detalles del Movimiento

Para cada bulto:

| Atributo | Valor |
|----------|-------|
| Bulto | Referencia al bulto |
| Cantidad | Cantidad inicial del bulto |
| Unidad de Medida | Unidad del bulto |
| Activo | SI |

### 6.5 Trazas Creadas (Solo Unidad de Venta)

Si el producto es UNIDAD_VENTA con unidad UNIDAD, se crean trazas:

| Atributo | Valor |
|----------|-------|
| Numero de Traza | Desde traza inicial hasta traza inicial + cantidad - 1 |
| Producto | Producto seleccionado |
| Lote | Lote creado |
| Estado | DISPONIBLE |
| Activo | SI |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario Ingreso Produccion] ────────────────┐
      |                                          |
      | (Completar datos)                        | (Cancelar)
      |                                          |
      v                                          v
[Pantalla de Confirmacion] ─────────────┐   [Menu Principal]
      |                                  |
      | (Confirmar)                      | (Volver a editar)
      v                                  v
[Pantalla de Exito] ◄───────────── [Formulario Ingreso]
      |
      | (Nuevo Ingreso) o (Ir a Inicio)
      v
[Formulario Ingreso] o [Menu Principal]
```

### 7.2 Pantalla de Confirmacion

Antes de crear el lote, se muestra resumen con:

- Producto seleccionado (nombre y codigo)
- Lote proveedor
- Orden de produccion
- Cantidad total y unidad
- Distribucion de bultos
- Fecha de ingreso
- Motivo del cambio

**Advertencia:** "Verifique los datos antes de confirmar. El lote se creara con dictamen RECIBIDO."

### 7.3 Pantalla de Exito

Muestra:
- Mensaje: "Ingreso de stock por produccion exitoso"
- Codigo del lote creado
- Cantidad ingresada
- Opciones: "Registrar otro ingreso" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Flujo Normal Despues de CU20

```
CU20: Ingreso Produccion (RECIBIDO)
         |
         v
CU2: Cuarentena (CUARENTENA + Nro Analisis)
         |
         v
CU3: Muestreo (muestra para analisis)
         |
         v
    [Ensayos de Laboratorio]
         |
    +----+----+
    |         |
    v         v
 CU5:       CU6:
APROBADO   RECHAZADO
    |         |
    v         v
 CU22      Descarte
(si UV)    o Reproceso
```

### 8.2 Operaciones Posibles Despues de CU20

| CU | Nombre | Cuando Aplicar | Usuario |
|----|--------|----------------|---------|
| CU2 | Cuarentena | Para iniciar analisis del lote | Analista Control Calidad |
| CU31 | Reverso | Si el ingreso fue incorrecto | Usuario que creo el lote |
| CU30 | Ajuste Stock | Si hay error en cantidades | Supervisor de Planta |

### 8.3 Operaciones NO Posibles Hasta Aprobar

| CU | Nombre | Por Que No |
|----|--------|------------|
| CU7 | Consumo Produccion | Requiere lote APROBADO |
| CU22 | Confirmar Liberacion | Requiere lote APROBADO |
| CU23 | Venta | Requiere lote LIBERADO |

---

## 9. Casos de Uso Ejemplos

### 9.1 Ingreso de Semielaborado: Granulado

**Situacion:** Se completa la fabricacion de un lote de granulado de paracetamol.

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Fecha de Ingreso | 06/12/2025 |
| Orden de Produccion | OP-2025-0150 |
| Producto | Granulado Paracetamol (SEMI-001) |
| Codigo de Lote (Sufijo) | 25120001A |
| Lote Proveedor | LOTE-GRAN-2025-045 |
| Cantidad Total | 50 |
| Unidad de Medida | KILOGRAMO |
| Cantidad de Bultos | 2 |
| Bulto 1 | 25 KILOGRAMO |
| Bulto 2 | 25 KILOGRAMO |
| Motivo del Cambio | Ingreso de granulado segun orden OP-2025-0150, producido en mezcladora MZ-001 |

**Resultado:**
- Lote **SEMI-001_25120001A** creado con 50 KG
- 2 bultos de 25 KG cada uno
- Dictamen: RECIBIDO
- Proveedor: Conifarma
- Movimiento de ALTA por PRODUCCION_PROPIA registrado

### 9.2 Ingreso de Unidad de Venta con Trazas

**Situacion:** Se completa el acondicionamiento de un lote de comprimidos.

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Fecha de Ingreso | 06/12/2025 |
| Orden de Produccion | OP-2025-0152 |
| Producto | Ibuprofeno 400mg Blister x 20 (UDV-001) |
| Codigo de Lote (Sufijo) | 25120002B |
| Lote Proveedor | LOTE-IBU-2025-012 |
| Cantidad Total | 1000 |
| Unidad de Medida | UNIDAD |
| Cantidad de Bultos | 1 |
| Traza Inicial | 50001 |
| Motivo del Cambio | Acondicionamiento completado segun OP-2025-0152, linea de blisteo BL-002 |

**Resultado:**
- Lote **UDV-001_25120002B** creado con 1000 UNIDADES
- 1 bulto de 1000 UNIDADES
- 1000 trazas creadas (50001 a 51000)
- Dictamen: RECIBIDO
- Proveedor: Conifarma

### 9.3 Ingreso de Granel Frascos

**Situacion:** Se fabrica un lote de jarabe a granel.

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Fecha de Ingreso | 06/12/2025 |
| Orden de Produccion | OP-2025-0153 |
| Producto | Jarabe Pediatrico a Granel (GRAN-002) |
| Codigo de Lote (Sufijo) | 25120003C |
| Lote Proveedor | LOTE-JAR-2025-008 |
| Cantidad Total | 500 |
| Unidad de Medida | LITRO |
| Cantidad de Bultos | 5 |
| Bulto 1-5 | 100 LITRO cada uno |
| Detalle Conservacion | Almacenar entre 15-25°C, proteger de la luz |
| Motivo del Cambio | Fabricacion de jarabe pediatrico lote LOTE-JAR-2025-008 segun formula FM-JAR-001 |

**Resultado:**
- Lote **GRAN-002_25120003C** creado con 500 LITROS
- 5 bultos de 100 LITROS cada uno
- Dictamen: RECIBIDO
- Detalle de conservacion registrado

### 9.4 Caso Error: Suma de Bultos Incorrecta

**Situacion:** El usuario indica cantidad total 100 KG pero los bultos suman 90 KG.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Cantidad Total | 100 KILOGRAMO |
| Bulto 1 | 50 KILOGRAMO |
| Bulto 2 | 40 KILOGRAMO |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "La suma de los bultos debe ser igual a la cantidad total"
- El usuario debe corregir: 50 + 50 = 100

### 9.5 Caso Error: Traza Duplicada

**Situacion:** El usuario indica una traza inicial que ya fue usada.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Producto | Ibuprofeno 400mg Blister x 20 |
| Traza Inicial | 40000 |
| Ultimo registrado | 50000 |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "El numero de traza debe ser mayor al ultimo registrado. 50000"
- El usuario debe indicar 50001 o mayor

### 9.6 Caso Error: Codigo de Lote Duplicado

**Situacion:** El usuario intenta crear un lote con un codigo que ya existe.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Producto | Granulado Paracetamol (SEMI-001) |
| Codigo de Lote (Sufijo) | 25010001A |
| Codigo existente | SEMI-001/25010001A |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "El codigo de lote 'SEMI-001/25010001A' ya existe en el sistema"
- El usuario debe usar un codigo diferente (ej: 25010001B o 25010002A)

### 9.7 Caso Error: Mes Invalido en Codigo

**Situacion:** El usuario ingresa un mes invalido en el codigo de lote.

**Datos ingresados:**
| Campo | Valor |
|-------|-------|
| Codigo de Lote (Sufijo) | 25150001A |

**Resultado:**
- El sistema rechaza el formulario
- Mensaje: "El codigo debe tener formato YYMM####S (ej: 25010001A)"
- El mes "15" no es valido (debe ser 01-12)
- El usuario debe corregir a un mes valido

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Proceso

**P: ¿Por que el proveedor es siempre Conifarma?**
R: Porque CU20 es para productos de fabricacion interna. Los productos de proveedores externos se ingresan con CU1.

**P: ¿Por que el lote queda con dictamen RECIBIDO?**
R: Porque todo producto fabricado debe pasar por Control de Calidad antes de poder usarse o venderse.

**P: ¿Puedo ingresar productos que se compran a proveedores?**
R: No. CU20 solo muestra productos de tipo Semielaborado, Granel o Unidad de Venta. Para API, Excipientes y Acondicionamiento use CU1.

**P: ¿Que es la traza inicial?**
R: Es el numero de identificacion individual de la primera unidad del lote. El sistema genera trazas consecutivas desde ese numero.

### 10.1.1 Sobre el Codigo de Lote

**P: ¿Como se forma el codigo de lote?**
R: Se compone de {CodigoProducto}/{YYMM####S}. El prefijo (codigo de producto) es automatico, solo ingresa el sufijo.

**P: ¿Que significa cada parte del sufijo YYMM####S?**
R: YY = ano (2 digitos), MM = mes (01-12), #### = secuencial (4 digitos), S = letra identificadora (A-Z).

**P: ¿Para que sirve la letra final?**
R: Permite distinguir sub-lotes o variantes dentro del mismo mes y secuencial. Ej: 25010001A, 25010001B.

**P: ¿Puedo usar letras minusculas?**
R: Si, el sistema las convierte automaticamente a mayusculas.

**P: ¿Que pasa si el codigo ya existe?**
R: El sistema rechaza el formulario indicando que el codigo ya esta en uso. Debe elegir otro.

### 10.2 Sobre las Cantidades

**P: ¿Cuantos bultos puedo crear?**
R: Minimo 1 y maximo 15 bultos por lote.

**P: ¿Que pasa si indico 1 bulto?**
R: La seccion de distribucion por bulto no aparece. El unico bulto tendra la cantidad total.

**P: ¿Puedo usar diferentes unidades en cada bulto?**
R: Si, siempre que sean compatibles con la unidad base (ej: gramos en un bulto cuando el lote es en kilos).

### 10.3 Sobre Trazas

**P: ¿Para que productos se generan trazas?**
R: Solo para Unidades de Venta con unidad de medida UNIDAD.

**P: ¿Cuantas trazas se crean?**
R: Una traza por cada unidad. Si ingresa 1000 unidades con traza inicial 50001, se crean trazas 50001 a 51000.

**P: ¿Por que debe ser mayor al ultimo registrado?**
R: Para evitar duplicados y garantizar trazabilidad unica de cada unidad.

### 10.4 Sobre Errores

**P: ¿Que hago si ingrese mal la cantidad?**
R: Use CU31 (Reverso) para anular el ingreso y repita con los datos correctos.

**P: ¿Que hago si seleccione el producto incorrecto?**
R: Use CU31 (Reverso) para anular el lote y cree uno nuevo con el producto correcto.

**P: ¿Puedo modificar el lote despues de crearlo?**
R: No directamente. Debe usar Reverso y crear uno nuevo, o Ajuste de Stock para corregir cantidades.

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Supervisor de Planta o Administrador
2. **Para que:** Registrar ingreso de productos fabricados internamente
3. **Tipos de producto:** Semielaborado, Granel Comprimidos, Granel Frascos, Unidad de Venta
4. **Proveedor:** Siempre Conifarma (automatico)
5. **Dictamen inicial:** RECIBIDO (requiere analisis)
6. **Tipo de movimiento:** ALTA
7. **Motivo del movimiento:** PRODUCCION_PROPIA
8. **Codigo de lote:** Formato {CodigoProducto}_{YYMM####S} - usuario ingresa sufijo
9. **Campo critico:** Motivo del cambio (mínimo 20 caracteres)
10. **Especial UV:** Traza inicial obligatoria para Unidades de Venta

---

## Relacion con Otros CUs

### Operaciones Previas (que habilitan CU20)

| CU | Nombre | Resultado |
|----|--------|-----------|
| CU7 | Consumo Produccion | Materiales consumidos para fabricar |

### Operaciones Siguientes (despues de CU20)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU2 | Cuarentena | Lote recien creado con dictamen RECIBIDO |
| CU31 | Reverso | Si se necesita anular el ingreso |

---

**Fin del Documento - CU20_INGRESO_PRODUCCION v1.1 - Especificacion Funcional**

---

## Historial de Cambios

| Version | Fecha | Cambio |
|---------|-------|--------|
| 1.0 | 2025-12-06 | Documentacion inicial |
| 1.1 | 2025-12-07 | Nuevo formato codigo lote: {CodigoProducto}/{YYMM####S} con ingreso manual del sufijo |
