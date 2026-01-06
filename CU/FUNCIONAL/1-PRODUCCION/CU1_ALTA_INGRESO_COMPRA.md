# CU1 - ALTA INGRESO COMPRA: Especificacion Funcional

**Version:** 2.2
**Fecha:** 2025-12-09
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Tipo Operacion:** ALTA
**Motivo:** COMPRA

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

Este caso de uso permite registrar el **ingreso de materiales externos** al inventario cuando se reciben de un proveedor externo (compra). Es el punto de entrada para materias primas, excipientes y materiales de acondicionamiento.

### 1.2 Cuando Usar Este CU

Usar CU1 cuando:
- Se recibe mercaderia de un proveedor **externo** (no Conifarma)
- El material es de tipo: API, Excipiente, Acondicionamiento Primario o Secundario

**NO usar CU1 cuando:**
- El material proviene de produccion interna → usar **CU20 (Ingreso Produccion)**
- El material es Semielaborado o Unidad de Venta → usar **CU20**

### 1.3 Resultado del CU

Al completar exitosamente:
- Se crea un **nuevo lote** con estado NUEVO y dictamen RECIBIDO
- Se generan los **bultos** asociados (1 a N)
- Se registra un **movimiento de ALTA** por motivo COMPRA
- El lote queda disponible para ser enviado a Cuarentena (CU2)

---

## 2. Perfiles de Usuario

### 2.1 Quien Puede Ejecutar Este CU

| Perfil | Puede Ejecutar | Observaciones |
|--------|----------------|---------------|
| Analista de Planta | SI | Usuario principal para esta operacion |
| Administrador | SI | Acceso completo al sistema |
| Supervisor de Planta | NO | Debe solicitar a Analista de Planta |
| Analista Control Calidad | NO | Su rol inicia en CU2 |
| Gerentes | NO | Roles de supervision, no operativos |
| DT | NO | Rol de aprobacion, no operativo |
| Auditor | NO | Solo lectura |

### 2.2 Responsabilidades del Usuario

- Verificar fisicamente la mercaderia recibida
- Completar correctamente todos los datos del formulario
- Documentar el motivo del ingreso (mínimo 20 caracteres)
- Confirmar los datos antes de guardar

---

## 3. Pre-condiciones

### 3.1 Requisitos Previos

Antes de iniciar el CU1, asegurarse de que:

| Requisito | Descripcion | Si no existe... |
|-----------|-------------|-----------------|
| **Producto** | Debe estar dado de alta en el sistema | Solicitar a Gerente Control Calidad que ejecute alta de producto |
| **Proveedor** | Debe estar dado de alta en el sistema | Solicitar a Analista de Planta que ejecute alta de proveedor |
| **Fabricante** (opcional) | Si se indica, debe existir como proveedor | Dar de alta primero el fabricante como proveedor |

### 3.2 Restricciones de Producto

| Tipo de Producto | Permitido en CU1 | Alternativa |
|------------------|------------------|-------------|
| API | SI | - |
| Excipiente | SI | - |
| Acondicionamiento Primario | SI | - |
| Acondicionamiento Secundario | SI | - |
| Semielaborado | NO | Usar CU20 |
| Unidad de Venta | NO | Usar CU20 |

### 3.3 Restriccion de Proveedor

El proveedor seleccionado **NO puede ser Conifarma** (proveedor interno).
Los productos de Conifarma se ingresan mediante CU20 (Produccion Interna).

---

## 4. Formulario de Ingreso

### 4.1 Campos del Formulario

| Campo | Obligatorio | Tipo | Descripcion | Valor por Defecto |
|-------|-------------|------|-------------|-------------------|
| **Producto** | SI | Selector | Producto a ingresar (solo tipos permitidos) | - |
| **Proveedor** | SI | Selector | Proveedor de la compra (excluye Conifarma) | - |
| **Fabricante** | NO | Selector | Fabricante del producto (si difiere del proveedor) | - |
| **Cantidad** | SI | Numerico | Cantidad total a ingresar | - |
| **Unidad de Medida** | SI | Selector | Unidad de la cantidad | Unidad del producto |
| **Cantidad de Bultos** | SI | Numerico | Numero de bultos/envases | 1 |
| **Cantidades por Bulto** | Condicional | Numerico | Si hay mas de 1 bulto, indicar cantidad de cada uno | - |
| **Unidades por Bulto** | Condicional | Selector | Si hay mas de 1 bulto, indicar unidad de cada uno | - |
| **Lote Proveedor** | SI | Texto | Numero de lote asignado por el proveedor | - |
| **Fecha de Ingreso** | SI | Fecha | Fecha de recepcion del material | Hoy |
| **Fecha de Reanalisis** | NO | Fecha | Fecha de reanalisis indicada por proveedor | - |
| **Fecha de Vencimiento** | NO | Fecha | Fecha de vencimiento indicada por proveedor | - |
| **Pais de Origen** | NO | Selector | Pais de origen del material | Pais del fabricante o proveedor |
| **Nro Remito/Factura** | NO | Texto | Numero del documento de compra | - |
| **Detalle Conservacion** | NO | Texto | Condiciones de almacenamiento | - |
| **Motivo del Cambio/Observaciones** | SI | Texto | Descripcion del motivo del ingreso (min 20 chars) | - |

### 4.2 Cuando Aparecen Campos Condicionales

**Cantidades por Bulto y Unidades por Bulto:**
- Aparecen solo si "Cantidad de Bultos" es mayor a 1
- Se debe indicar la cantidad y unidad de cada bulto individualmente
- La suma de las cantidades debe coincidir con la cantidad total

### 4.3 Determinacion del Pais de Origen

El sistema determina el pais de origen automaticamente:

1. Si el usuario ingresa un pais → se usa ese valor
2. Si no, y hay fabricante → se usa el pais del fabricante
3. Si no hay fabricante → se usa el pais del proveedor

---

## 5. Validaciones

### 5.1 Validaciones al Enviar el Formulario

| # | Campo | Validacion | Mensaje de Error                                      |
|---|-------|------------|-------------------------------------------------------|
| 1 | Producto | Debe seleccionar uno | "El producto es obligatorio"                          |
| 2 | Proveedor | Debe seleccionar uno | "El proveedor es obligatorio"                         |
| 3 | Cantidad | Debe ingresar valor | "La cantidad es obligatoria"                          |
| 4 | Cantidad | Debe ser mayor a cero | "La cantidad debe ser mayor a 0"                      |
| 5 | Unidad de Medida | Debe seleccionar una | "La unidad de medida es obligatoria"                  |
| 6 | Lote Proveedor | Debe ingresar valor | "El lote proveedor es obligatorio"                    |
| 7 | Fecha de Ingreso | Debe ingresar fecha | "La fecha de ingreso es obligatoria"                  |
| 8 | Fecha de Ingreso | No puede ser futura | "La fecha de ingreso no puede ser futura"             |
| 9 | Cantidad de Bultos | Minimo 1 | "Debe haber al menos 1 bulto"                         |
| 10 | Nro Remito | Maximo 30 caracteres | "El nro de remito no puede exceder 30 caracteres"     |
| 11 | Motivo del Cambio/Observaciones | Maximo 300 caracteres | "El valor no puede exceder 300 caracteres"            |
| 12 | Fecha Vencimiento | Si se indica, debe ser futura | "La fecha de vencimiento del proveedor debe ser futura" |
| 13 | Fecha Reanalisis | Si se indica, debe ser futura | "La fecha de reanalisis del proveedor debe ser futura" |

### 5.2 Validaciones Especiales

#### Validacion de Cantidad con Unidad UNIDAD

Cuando la unidad de medida es "UNIDAD" (items indivisibles como frascos, ampollas):

| Validacion | Mensaje de Error |
|------------|------------------|
| La cantidad debe ser un numero entero (sin decimales) | "La cantidad debe ser un numero entero cuando la unidad es UNIDAD" |
| La cantidad debe ser mayor o igual a la cantidad de bultos | "La cantidad de Unidades (X) no puede ser menor a la cantidad de Bultos (Y)" |

**Ejemplo valido:** 10 UNIDADES en 3 bultos ✓
**Ejemplo invalido:** 10.5 UNIDADES ✗
**Ejemplo invalido:** 2 UNIDADES en 5 bultos ✗

#### Validacion de Fechas

Si se ingresan ambas fechas (reanalisis y vencimiento):

| Validacion | Mensaje de Error |
|------------|------------------|
| La fecha de reanalisis debe ser anterior a la de vencimiento | "La fecha de reanalisis no puede ser posterior a la fecha de vencimiento" |

#### Validacion de Suma de Bultos

Cuando hay mas de 1 bulto:

| Validacion | Mensaje de Error |
|------------|------------------|
| La suma de las cantidades de cada bulto debe ser igual a la cantidad total | "La suma de las cantidades individuales (X) no coincide con la cantidad total (Y)" |

**Ejemplo con unidades mixtas:**
- Cantidad total: 1.5 KILOGRAMO
- Bulto 1: 1.0 KILOGRAMO
- Bulto 2: 500 GRAMO (equivale a 0.5 KG)
- Suma: 1.0 + 0.5 = 1.5 KG ✓

#### Validacion de Motivo del Cambio

| Validacion | Mensaje de Error |
|------------|------------------|
| Debe tener al menos 20 caracteres | "El motivo del cambio es obligatorio (mínimo 20 caracteres)" |

---

## 6. Resultado de la Operacion

### 6.1 Datos Generados Automaticamente

| Dato | Formato | Ejemplo |
|------|---------|---------|
| Codigo de Lote | {codigoProducto}_{AAMM}{####} | 1-05701_25120001 |
| Estado del Lote | Siempre NUEVO | NUEVO |
| Dictamen del Lote | Siempre RECIBIDO | RECIBIDO |
| Numeracion de Bultos | Secuencial 1, 2, 3... | Bulto 1, Bulto 2, Bulto 3 |
| Codigo de Movimiento | {codigoLote}_MOV_{timestamp} | 1-05701_25120001_MOV_251215_103045 |

### 6.2 Estado Final del Lote

Despues de ejecutar CU1:

| Atributo | Valor |
|----------|-------|
| Estado | NUEVO |
| Dictamen | RECIBIDO |
| Cantidad Inicial | Cantidad ingresada |
| Cantidad Actual | Igual a cantidad inicial |
| Activo | SI |

### 6.3 Estado Final de Cada Bulto

| Atributo | Valor |
|----------|-------|
| Numero de Bulto | 1, 2, 3... (secuencial) |
| Estado | NUEVO |
| Cantidad Inicial | Cantidad asignada al bulto |
| Cantidad Actual | Igual a cantidad inicial |
| Activo | SI |

### 6.4 Movimiento Registrado

| Atributo | Valor |
|----------|-------|
| Tipo de Movimiento | ALTA |
| Motivo | COMPRA |
| Cantidad | Cantidad total ingresada |
| Usuario | Usuario que realizo la operacion |
| Fecha | Fecha de ingreso indicada |
| Activo | SI |

---

## 7. Flujo de Pantallas

### 7.1 Secuencia de Navegacion

```
[Menu Principal]
      |
      v
[Formulario de Ingreso] ──────────────────────┐
      |                                        |
      | (Completar datos y enviar)             | (Cancelar)
      v                                        v
[Pantalla de Confirmacion] ──────────┐    [Menu Principal]
      |                               |
      | (Confirmar)                   | (Volver a editar)
      v                               v
[Pantalla de Exito] ◄─────────── [Formulario de Ingreso]
      |
      | (Nuevo Ingreso) o (Ir a Inicio)
      v
[Formulario de Ingreso] o [Menu Principal]
```

### 7.2 Pantalla de Confirmacion

Antes de guardar, el sistema muestra un resumen con:

- Nombre del producto (resuelto desde el ID)
- Nombre del proveedor (resuelto desde el ID)
- Nombre del fabricante si aplica (resuelto desde el ID)
- Cantidad y unidad de medida
- Detalle de bultos
- Fechas ingresadas
- Motivo del cambio

El usuario debe confirmar o puede volver a editar.

### 7.3 Pantalla de Exito

Muestra:
- Mensaje de exito
- Codigo del lote generado
- Opciones: "Realizar otro ingreso" o "Ir al inicio"

---

## 8. Operaciones Posteriores

### 8.1 Flujo Normal Despues de CU1

El flujo tipico despues de ingresar un lote por compra es:

```
CU1: Ingreso Compra (RECIBIDO)
         |
         v
CU2: Pasaje a Cuarentena (CUARENTENA)
    - Control de Calidad asigna Nro de Analisis
         |
         v
CU3: Muestreo
    - Se toma muestra para analisis
         |
         v
CU5 o CU6: Resultado de Analisis
    - CU5: APROBADO → lote disponible para produccion
    - CU6: RECHAZADO → lote debe devolverse
         |
    +----+----+
    |         |
    v         v
  CU7       CU4
Consumo   Devolucion
Produccion  Compra
```

### 8.2 Operaciones Posibles Despues de CU1

| CU | Nombre | Cuando Aplicar | Usuario |
|----|--------|----------------|---------|
| CU2 | Cuarentena | Siempre - siguiente paso normal | Analista Control Calidad |
| CU4 | Devolucion Compra | Si el material tiene problemas antes de analizar | Analista Planta |
| CU30 | Ajuste Stock | Si hay error en las cantidades ingresadas | Supervisor o Analista Planta |
| CU31 | Reverso Movimiento | Si se ingreso por error y debe anularse | Usuario que hizo el ingreso |

### 8.3 Operaciones NO Posibles Inmediatamente

| CU | Nombre | Por Que No |
|----|--------|------------|
| CU3 | Muestreo | Requiere primero CU2 (asignar Nro Analisis) |
| CU5/CU6 | Resultado Analisis | Requiere CU2 y CU3 previos |
| CU7 | Consumo Produccion | Requiere lote APROBADO |
| CU23 | Venta | Solo para Unidades de Venta LIBERADAS |

---

## 9. Casos de Uso Ejemplos

### 9.1 Caso Simple: 1 Bulto de API

**Situacion:** Llega un tambor de 25 kg de Paracetamol del proveedor Sigma-Aldrich

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Producto | Paracetamol (1-05701) |
| Proveedor | Sigma-Aldrich |
| Cantidad | 25 |
| Unidad de Medida | KILOGRAMO |
| Cantidad de Bultos | 1 |
| Lote Proveedor | LP-2025-001 |
| Fecha de Ingreso | (fecha de hoy) |
| Motivo del Cambio | Recepcion de compra segun orden OC-2025-123 |

**Resultado:**
- Lote: 1-05701_25120001
- 1 bulto de 25 kg
- Estado: NUEVO / RECIBIDO

### 9.2 Caso Multiple: 3 Bultos con Misma Unidad

**Situacion:** Llegan 3 bolsas de excipiente, cada una con diferente peso

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Producto | Lactosa (1-05710) |
| Proveedor | ProvQuim SA |
| Cantidad | 60 |
| Unidad de Medida | KILOGRAMO |
| Cantidad de Bultos | 3 |
| Bulto 1 | 25 KILOGRAMO |
| Bulto 2 | 20 KILOGRAMO |
| Bulto 3 | 15 KILOGRAMO |
| Lote Proveedor | LOT-EXC-789 |
| Motivo del Cambio | Ingreso de lactosa para produccion de capsulas |

**Validacion automatica:** 25 + 20 + 15 = 60 ✓

**Resultado:**
- Lote con 3 bultos numerados
- Cada bulto con su cantidad individual

### 9.3 Caso con Unidades Mixtas

**Situacion:** Llegan 2 envases, uno grande y uno pequeno del mismo producto

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Producto | Alcohol Etilico (1-05720) |
| Proveedor | Quimica Industrial |
| Cantidad | 10.5 |
| Unidad de Medida | LITRO |
| Cantidad de Bultos | 2 |
| Bulto 1 | 10 LITRO |
| Bulto 2 | 500 MILILITRO |
| Motivo del Cambio | Reposicion de stock de alcohol para limpieza |

**Validacion automatica:** 10 L + 0.5 L = 10.5 L ✓

### 9.4 Caso con UNIDAD (Items Indivisibles)

**Situacion:** Llegan cajas de frascos de vidrio (material de acondicionamiento)

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Producto | Frasco Ambar 100ml (ACOND-001) |
| Proveedor | Envases SRL |
| Cantidad | 500 |
| Unidad de Medida | UNIDAD |
| Cantidad de Bultos | 5 |
| Bulto 1 a 5 | 100 UNIDAD cada uno |
| Motivo del Cambio | Recepcion de frascos para linea de produccion |

**Validaciones:**
- 500 es entero ✓
- 500 >= 5 (bultos) ✓
- 100 × 5 = 500 ✓

### 9.5 Caso con Fabricante Diferente al Proveedor

**Situacion:** El proveedor es un distribuidor, pero el fabricante real es otro

**Datos a ingresar:**
| Campo | Valor |
|-------|-------|
| Producto | Ibuprofeno (1-05702) |
| Proveedor | Distribuidora Farma (Argentina) |
| Fabricante | BASF (Alemania) |
| Cantidad | 50 |
| Unidad de Medida | KILOGRAMO |
| Pais de Origen | (se deja vacio) |
| Motivo del Cambio | Compra a distribuidor local de API importado |

**Resultado:**
- Pais de Origen se completa automaticamente como "Alemania" (del fabricante)

---

## 10. Preguntas Frecuentes

### 10.1 Sobre el Formulario

**P: ¿Puedo ingresar un producto tipo Semielaborado o Unidad de Venta?**
R: No. Esos productos se ingresan con CU20 (Produccion Interna) porque son fabricados por Conifarma.

**P: ¿Por que no aparece Conifarma en la lista de proveedores?**
R: Conifarma es el proveedor interno. Para ingresos de Conifarma usar CU20.

**P: ¿Puedo poner una fecha de ingreso futura?**
R: No. La fecha de ingreso debe ser hoy o una fecha anterior.

**P: ¿Que pasa si no conozco la fecha de vencimiento?**
R: El campo es opcional. Se puede dejar vacio y Control de Calidad la definira durante el analisis.

### 10.2 Sobre los Bultos

**P: ¿Que es un bulto?**
R: Es cada envase fisico individual (tambor, bolsa, caja, etc.) que contiene parte del lote.

**P: ¿Puedo tener bultos con diferentes unidades de medida?**
R: Si, siempre que las unidades sean convertibles entre si (ej: kg y g, L y mL).

**P: ¿Que pasa si la suma de los bultos no coincide con el total?**
R: El sistema rechazara el formulario. Debe corregir las cantidades.

### 10.3 Sobre el Motivo del Cambio

**P: ¿Por que es obligatorio el motivo del cambio?**
R: Es un requerimiento regulatorio (ANMAT) para trazabilidad. Debe describirse el motivo del ingreso.

**P: ¿Por que debe tener minimo 20 caracteres?**
R: Para asegurar que se proporcione informacion significativa, no solo palabras sueltas.

**P: ¿Que debo poner en el motivo?**
R: Descripcion del ingreso: numero de orden de compra, referencia al remito, motivo de la compra, etc.

### 10.4 Sobre Errores

**P: ¿Que hago si ingrese datos incorrectos?**
R: Si aun no confirmo, puede volver a editar. Si ya confirmo, puede usar CU30 (Ajuste Stock) o CU31 (Reverso Movimiento).

**P: ¿Que hago si el producto o proveedor no existe?**
R: Debe solicitar el alta antes de continuar:
- Producto: solicitar a Gerente Control Calidad
- Proveedor: solicitar a Analista de Planta o Supervisor

---

## Resumen de Puntos Clave

1. **Quien lo usa:** Analista de Planta o Administrador
2. **Para que:** Ingresar materiales comprados a proveedores externos
3. **Productos permitidos:** API, Excipiente, Acond. Primario, Acond. Secundario
4. **Estado resultante:** NUEVO / RECIBIDO
5. **Siguiente paso tipico:** CU2 (Cuarentena)
6. **Motivo del movimiento:** COMPRA
7. **Campo critico:** Motivo del Cambio/Observaciones (mínimo 20 caracteres)

---

## Relacion con Otros CUs

### Operaciones Siguientes (despues de CU1)

| CU | Nombre | Pre-condicion |
|----|--------|---------------|
| CU2 | Dictamen Cuarentena | Lote recien creado con dictamen RECIBIDO |
| CU31 | Reverso | Si se necesita anular el ingreso |

---

**Fin del Documento - CU1_ALTA_INGRESO_COMPRA v2.2 - Especificacion Funcional**
