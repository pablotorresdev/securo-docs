# MAN-001: Manual de Usuario
## Sistema Conitrack - Gestion de Stock Farmaceutico

---

**Codigo documento:** MAN-001
**Version:** 1.0
**Fecha:** 2025-12-16
**Estado:** Aprobado

---

## Control de Versiones

| Version | Fecha | Autor | Descripcion |
|---------|-------|-------|-------------|
| 1.0 | 2025-12-16 | Equipo Desarrollo | Version inicial aprobada |

---

## Indice de Contenidos

1. [Introduccion](#1-introduccion)
2. [Acceso al Sistema](#2-acceso-al-sistema)
3. [Interfaz Principal](#3-interfaz-principal)
4. [Gestion de Lotes](#4-gestion-de-lotes)
5. [Compras - Ingreso de Mercaderia](#5-compras---ingreso-de-mercaderia)
6. [Produccion](#6-produccion)
7. [Ventas - Egreso de Mercaderia](#7-ventas---egreso-de-mercaderia)
8. [Calidad - Analisis](#8-calidad---analisis)
9. [Contingencias](#9-contingencias)
10. [Consultas e Informes](#10-consultas-e-informes)
11. [Administracion](#11-administracion)
12. [Auditoria y Trazabilidad](#12-auditoria-y-trazabilidad)
13. [Preguntas Frecuentes](#13-preguntas-frecuentes)
14. [Contacto y Soporte](#14-contacto-y-soporte)

---

## 1. Introduccion

### 1.1 Proposito del Sistema

Conitrack es un sistema de gestion de stock farmaceutico diseñado para:

- Registrar y controlar lotes de productos (materias primas, insumos, productos terminados)
- Gestionar movimientos de stock (ingresos, egresos, ajustes)
- Registrar resultados de analisis de calidad
- Mantener trazabilidad completa de lotes
- Cumplir con requisitos regulatorios de ANMAT

### 1.2 Destinatarios del Manual

Este manual esta dirigido a:

- Personal de planta (produccion y almacen)
- Personal de control de calidad
- Personal de garantia de calidad
- Supervisores y gerentes
- Auditores

### 1.3 Requisitos de Acceso

**Navegadores soportados:**
- Google Chrome (ultimas 2 versiones)
- Mozilla Firefox (ultimas 2 versiones)
- Microsoft Edge (ultimas 2 versiones)

**Resolucion recomendada:** 1280x720 pixeles o superior

**Conectividad:** Conexion a internet estable

### 1.4 Convenciones del Manual

| Icono/Formato | Significado |
|---------------|-------------|
| **Negrita** | Nombres de botones, campos o menus |
| `Codigo` | Comandos o valores exactos a ingresar |
| > | Indica navegacion en menus (Menu > Submenu) |

---

## 2. Acceso al Sistema

### 2.1 Ingresar al Sistema (Login)

1. Abra su navegador web
2. Ingrese la URL del sistema proporcionada por su administrador
3. Se mostrara la pantalla de inicio de sesion

**Pantalla de Login:**

La pantalla muestra:
- Campo **Usuario**: Ingrese su nombre de usuario asignado
- Campo **Contrasena**: Ingrese su contrasena (los caracteres se ocultan)
- Boton **Iniciar Sesion**: Presione para acceder

**Procedimiento:**

1. Ingrese su nombre de usuario en el campo **Usuario**
2. Ingrese su contrasena en el campo **Contrasena**
3. Haga clic en el boton **Iniciar Sesion**
4. Si las credenciales son correctas, sera redirigido a la pantalla principal
5. Si las credenciales son incorrectas, aparecera un mensaje de error

### 2.2 Primer Inicio de Sesion

Si es su primer acceso al sistema o su contrasena fue reseteada:

1. Ingrese con las credenciales temporales proporcionadas
2. El sistema le solicitara cambiar la contrasena
3. Ingrese una nueva contrasena que cumpla con los requisitos:
   - Minimo 8 caracteres
   - Al menos 1 letra mayuscula
   - Al menos 1 letra minuscula
   - Al menos 1 numero
4. Confirme la nueva contrasena
5. Haga clic en **Guardar**

### 2.3 Contrasena Expirada

Las contrasenas expiran cada 180 dias. Cuando expire:

1. Al intentar ingresar, el sistema le notificara que debe cambiar su contrasena
2. Siga los pasos indicados en la seccion 2.2

### 2.4 Cuenta Bloqueada

Si ingresa credenciales incorrectas 5 veces consecutivas:

1. Su cuenta sera bloqueada temporalmente por 15 minutos
2. Espere el tiempo indicado antes de intentar nuevamente
3. Si necesita acceso urgente, contacte al administrador del sistema

### 2.5 Cerrar Sesion

Para cerrar su sesion de forma segura:

1. Haga clic en su nombre de usuario en la esquina superior derecha
2. Seleccione la opcion **Cerrar Sesion**
3. Sera redirigido a la pantalla de login

**Importante:** Siempre cierre sesion al terminar de trabajar, especialmente en equipos compartidos.

### 2.6 Timeout de Sesion

Por seguridad, si no hay actividad durante 30 minutos, la sesion se cierra automaticamente. Al intentar realizar cualquier accion, sera redirigido a la pantalla de login.

---

## 3. Interfaz Principal

### 3.1 Estructura de la Pantalla

Al ingresar al sistema, vera:

**Barra Superior:**
- Logo del sistema
- Nombre del usuario logueado
- Rol asignado
- Boton de cerrar sesion

**Menu Lateral:**
- Acceso rapido a todas las funciones del sistema
- Organizado por categorias (Compras, Produccion, Ventas, Calidad, etc.)
- Se puede colapsar para mayor espacio de trabajo

**Area Principal:**
- Contenido de la funcion seleccionada
- Formularios, listados, detalles

**Mensajes:**
- Mensajes de exito (verde): Operacion completada correctamente
- Mensajes de error (rojo): Error que debe corregirse
- Mensajes de advertencia (amarillo): Informacion importante

### 3.2 Menu Lateral por Categoria

Dependiendo de su rol, vera las siguientes opciones:

**Inicio**
- Dashboard / Pagina principal

**Compras**
- Alta Ingreso Compra
- Devolucion a Proveedor

**Produccion**
- Alta Lote Produccion
- Consumo Produccion
- Trazado de Lotes

**Ventas**
- Egreso Venta
- Devolucion Cliente
- Confirmar Liberacion

**Calidad**
- Resultado Analisis
- Muestreo Bultos
- Anulacion Analisis
- Reanalisis

**Contingencias**
- Reverso Movimiento
- Retiro de Mercado (Recall)
- Ajuste de Stock

**Consultas**
- Listado de Lotes
- Listado de Movimientos
- Historial de Cambios

**Administracion** (solo ADMIN)
- Usuarios
- Productos
- Proveedores
- Configuracion

### 3.3 Elementos Comunes

**Tablas/Listados:**
- Columnas ordenables (clic en encabezado)
- Filtros por columna
- Paginacion en parte inferior
- Boton para ver detalle en cada fila

**Formularios:**
- Campos obligatorios marcados con asterisco (*)
- Validacion en tiempo real
- Boton **Guardar** para confirmar
- Boton **Cancelar** para descartar

**Campo Motivo:**
- Obligatorio en todas las operaciones
- Minimo 20 caracteres
- Describe la razon del cambio

---

## 4. Gestion de Lotes

### 4.1 Que es un Lote

Un lote representa un conjunto de unidades de un producto con las mismas caracteristicas, producido o recibido en un mismo momento. Cada lote tiene:

- **Codigo interno**: Generado automaticamente por el sistema
- **Numero de lote**: Asignado por el proveedor o por produccion
- **Producto**: El producto al que pertenece
- **Cantidad**: Cantidad actual en stock
- **Unidad de medida**: Kilogramos, litros, unidades, etc.
- **Proveedor**: Para lotes de compra
- **Fecha de vencimiento**: Cuando aplica
- **Estado**: Condicion segun cantidad (NUEVO, DISPONIBLE, EN_USO, CONSUMIDO, VENDIDO, DEVUELTO, RECALL, DESCARTADO)
- **Dictamen**: Resultado de calidad (RECIBIDO, CUARENTENA, APROBADO, RECHAZADO, LIBERADO, RETIRO_MERCADO, etc.)

### 4.2 Estados y Dictamenes

**IMPORTANTE:** El sistema distingue entre dos conceptos:
- **Estado (EstadoEnum):** Refleja la condicion del lote segun su cantidad
- **Dictamen (DictamenEnum):** Refleja el resultado de la evaluacion de calidad

**Estados del lote (segun cantidad):**

| Estado | Significado | Cuando se aplica |
|--------|-------------|------------------|
| NUEVO | Lote recien ingresado, cantidad inicial = actual | Alta de compra o produccion |
| DISPONIBLE | Traza disponible, cantidad inicial = actual | Para registros de traza |
| EN_USO | Cantidad parcialmente consumida (inicial > actual > 0) | Despues de bajas parciales |
| CONSUMIDO | Cantidad agotada por uso en produccion | Baja total por produccion |
| VENDIDO | Cantidad agotada por venta | Baja total por venta |
| DEVUELTO | Cantidad agotada por devolucion a proveedor | Devolucion total |
| RECALL | Cantidad agotada por retiro de mercado | Recall completo |
| DESCARTADO | Cantidad agotada por destruccion/rechazo | Destruccion de rechazados |

**Dictamenes (resultado de calidad):**

| Dictamen | Significado | Operaciones permitidas |
|----------|-------------|------------------------|
| RECIBIDO | Lote recien ingresado, sin analisis | Muestreo, pase a cuarentena |
| CUARENTENA | En espera de resultado de analisis | Solo consultas, registro de analisis |
| APROBADO | Analisis aprobado, apto para uso | Produccion, trazado |
| LIBERADO | Liberado para venta | Ventas, devoluciones cliente |
| RECHAZADO | Analisis rechazado, no apto | Devolucion proveedor, destruccion |
| ANULADO | Analisis anulado | Nuevo analisis |
| VENCIDO | Producto vencido | Destruccion |
| DEVOLUCION_CLIENTES | Devuelto por cliente | Reanalisis, destruccion |
| RETIRO_MERCADO | Retirado del mercado (recall) | Solo consultas |

### 4.3 Consultar Listado de Lotes

1. Navegue a **Consultas > Listado de Lotes**
2. Se muestra la lista de lotes activos
3. Use los filtros para buscar:
   - Por producto
   - Por numero de lote
   - Por estado
   - Por dictamen
   - Por proveedor
   - Por rango de fechas
4. Haga clic en un lote para ver su detalle

### 4.4 Ver Detalle de un Lote

La pantalla de detalle muestra:

**Datos Generales:**
- Codigo, producto, numero de lote
- Cantidades (inicial y actual)
- Estado y dictamen
- Fechas (creacion, vencimiento)
- Proveedor/fabricante

**Pestanas:**

- **Movimientos**: Lista de todos los movimientos del lote
- **Analisis**: Resultados de analisis realizados
- **Bultos**: Detalle de bultos/paquetes
- **Trazas**: Lotes relacionados (origen o derivados)
- **Historial**: Registro de cambios (audit trail)

### 4.5 Gestion de Bultos

Un lote puede tener multiples bultos (paquetes, cajas, tambores). Para gestionarlos:

1. Acceda al detalle del lote
2. Seleccione la pestana **Bultos**
3. Vera la lista de bultos con:
   - Numero de bulto
   - Cantidad
   - Unidad de medida
4. Desde esta pantalla puede agregar o modificar bultos

---

## 5. Compras - Ingreso de Mercaderia

### 5.1 Alta de Lote por Compra

Cuando recibe mercaderia de un proveedor:

1. Navegue a **Compras > Alta Ingreso Compra**
2. Complete el formulario:

**Campos obligatorios:**
- **Producto**: Seleccione de la lista desplegable
- **Numero de lote**: Ingrese el numero del lote del proveedor
- **Cantidad**: Cantidad recibida
- **Unidad de medida**: Seleccione (KG, L, UNIDAD, etc.)
- **Proveedor**: Seleccione de la lista
- **Motivo**: Describa la operacion (ej: "Recepcion de OC-2025-001")

**Campos opcionales:**
- **Fabricante**: Si es diferente del proveedor
- **Fecha de vencimiento**: Fecha de expiracion del producto
- **Fecha de fabricacion**: Fecha de produccion
- **Pais de origen**: Pais de procedencia

**Bultos (opcional):**
- Agregue detalle de bultos si aplica
- Numero, cantidad y unidad por cada bulto

3. Haga clic en **Guardar**
4. El sistema:
   - Crea el lote con estado NUEVO y dictamen RECIBIDO
   - Genera movimiento tipo ALTA con motivo COMPRA
   - Registra el cambio en audit trail

### 5.2 Devolucion a Proveedor

Para devolver mercaderia a un proveedor:

1. Navegue a **Compras > Devolucion Proveedor**
2. Seleccione el lote a devolver
3. Ingrese la cantidad a devolver
4. Indique el motivo de la devolucion
5. Haga clic en **Guardar**
6. El sistema descuenta la cantidad del lote

---

## 6. Produccion

### 6.1 Alta de Lote de Produccion

Para registrar un lote producido internamente:

1. Navegue a **Produccion > Alta Lote Produccion**
2. Complete el formulario:

**Campos obligatorios:**
- **Producto**: Producto terminado producido
- **Numero de lote**: Numero asignado internamente
- **Cantidad**: Cantidad producida
- **Unidad de medida**: Unidad del producto
- **Motivo**: Referencia a orden de produccion

**Campos opcionales:**
- **Fecha de fabricacion**: Fecha de produccion
- **Fecha de vencimiento**: Calculada o ingresada
- **Lote de origen**: Si deriva de otro lote

3. Haga clic en **Guardar**
4. El lote se crea con estado NUEVO y dictamen RECIBIDO

### 6.2 Consumo de Produccion

Para registrar consumo de materia prima en produccion:

1. Navegue a **Produccion > Consumo Produccion**
2. Seleccione el lote de materia prima a consumir
3. Ingrese la cantidad consumida
4. Opcionalmente, vincule al lote producido (para traza)
5. Ingrese el motivo
6. Haga clic en **Guardar**

**Requisito:** El lote de MP debe tener dictamen APROBADO o LIBERADO

### 6.3 Trazado de Lotes

Para vincular materias primas con producto terminado:

1. Navegue a **Produccion > Trazado de Lotes**
2. Seleccione el lote producido (destino)
3. Agregue los lotes de materia prima utilizados:
   - Seleccione el lote origen
   - Ingrese la cantidad utilizada
4. Ingrese el motivo
5. Haga clic en **Guardar**

Esta operacion permite:
- Rastrear que materias primas componen un producto
- En caso de recall, identificar todos los productos afectados

---

## 7. Ventas - Egreso de Mercaderia

### 7.1 Egreso por Venta

Para registrar salida de mercaderia por venta:

1. Navegue a **Ventas > Egreso Venta**
2. Seleccione el lote a egresar:
   - Solo se muestran lotes con dictamen LIBERADO
   - Se muestra la cantidad disponible
3. Ingrese la cantidad a egresar
4. Ingrese el motivo (ej: "Venta a cliente ABC, pedido PED-001")
5. Haga clic en **Guardar**

**Validaciones:**
- El lote debe tener dictamen LIBERADO
- La cantidad no puede superar lo disponible
- Motivo minimo 20 caracteres

### 7.2 Devolucion de Cliente

Para registrar devolucion de mercaderia de un cliente:

1. Navegue a **Ventas > Devolucion Cliente**
2. Seleccione el movimiento de venta original
3. Ingrese la cantidad devuelta
4. Indique el motivo de la devolucion
5. Haga clic en **Guardar**

**Resultado:**
- Se incrementa la cantidad del lote
- Dictamen del lote cambia a DEVOLUCION_CLIENTES para reinspeccion

### 7.3 Confirmar Liberacion

Para confirmar que un lote esta liberado para venta:

1. Navegue a **Ventas > Confirmar Liberacion**
2. Seleccione el lote con dictamen APROBADO a liberar
3. Ingrese el motivo
4. Haga clic en **Confirmar**

Esta operacion cambia el dictamen del lote a LIBERADO.

---

## 8. Calidad - Analisis

### 8.1 Registrar Resultado de Analisis

Cuando el laboratorio completa el analisis de un lote:

1. Navegue a **Calidad > Resultado Analisis**
2. Seleccione el lote con dictamen CUARENTENA
3. Complete los datos del analisis:
   - **Numero de analisis**: Numero de protocolo
   - **Fecha de analisis**: Fecha de realizacion
   - **Resultado**: APROBADO o RECHAZADO
4. Ingrese el motivo detallado
5. Haga clic en **Guardar**

**Efectos segun resultado:**
- APROBADO: Dictamen del lote pasa a APROBADO
- RECHAZADO: Dictamen del lote pasa a RECHAZADO

### 8.2 Muestreo de Bultos

Para registrar extraccion de muestras:

1. Navegue a **Calidad > Muestreo Bultos**
2. Seleccione el lote y bulto
3. Ingrese la cantidad de muestra
4. Ingrese el motivo
5. Haga clic en **Guardar**

La cantidad se descuenta del bulto seleccionado.

### 8.3 Anulacion de Analisis

Si un analisis fue registrado incorrectamente:

1. Navegue a **Calidad > Anulacion Analisis**
2. Seleccione el analisis a anular
3. Ingrese el motivo de anulacion (detallado)
4. Haga clic en **Anular**

**Nota:** El analisis se marca como anulado pero no se elimina (cumplimiento de audit trail).

### 8.4 Reanalisis

Para solicitar nuevo analisis de un lote aprobado:

1. Navegue a **Calidad > Reanalisis**
2. Seleccione el lote APROBADO a reanalizar
3. Ingrese la fecha del reanalisis
4. Ingrese el numero de reanalisis con formato:
   - **Prefijo**: Seleccione MP, ME, MPD, PTD, SE o PT
   - **Digitos**: Ingrese 5 digitos en formato N-NNNN (ej: 1-0001)
   - El sistema combinara automaticamente (ej: MP-1-0001)
5. Ingrese el motivo del reanalisis (minimo 20 caracteres)
6. Haga clic en **Vista Previa**
7. Revise los datos en la pantalla de confirmacion
8. Haga clic en **Confirmar e Iniciar Reanalisis** para completar la operacion
   - O haga clic en **Volver a Editar** si necesita corregir algun dato

**Nota:** El lote permanece APROBADO y disponible para uso mientras el reanalisis esta en curso.

### 8.5 Dictamen de Cuarentena

Para poner un lote en cuarentena por sospecha:

1. Navegue a **Calidad > Dictamen Cuarentena**
2. Seleccione el lote activo
3. Ingrese el motivo (detallado)
4. Haga clic en **Aplicar Cuarentena**

El lote queda bloqueado hasta nuevo analisis.

---

## 9. Contingencias

### 9.1 Reverso de Movimiento

Para corregir un movimiento registrado por error:

1. Navegue a **Contingencias > Reverso Movimiento**
2. Se muestra la lista de movimientos reversibles
3. Seleccione el movimiento a revertir
4. Ingrese el motivo del reverso
5. Haga clic en **Revertir**

**Autorizacion:**
- Puede revertir sus propios movimientos
- Supervisores pueden revertir movimientos de su area
- Solo ADMIN puede revertir movimientos legacy

**Resultado:**
- Se crea movimiento inverso
- Cantidades se ajustan automaticamente
- Ambos movimientos quedan vinculados

### 9.2 Retiro de Mercado (Recall)

Para registrar un retiro de mercado:

1. Navegue a **Contingencias > Retiro de Mercado**
2. Seleccione el/los lote(s) afectados
3. Ingrese el motivo detallado del retiro
4. Haga clic en **Aplicar Recall**

**Resultado:**
- Dictamen de los lotes cambia a RETIRO_MERCADO
- Estado cambia a RECALL si la cantidad se reduce a cero
- Operaciones de venta bloqueadas
- Se registra movimiento especial

### 9.3 Ajuste de Stock

Para corregir diferencias de inventario:

1. Navegue a **Contingencias > Ajuste Stock**
2. Seleccione el lote a ajustar
3. Ingrese la nueva cantidad (actual)
4. Ingrese el motivo detallado del ajuste
5. Haga clic en **Guardar**

**Importante:** Use esta funcion solo para ajustes de inventario documentados, nunca para ocultar discrepancias.

---

## 10. Consultas e Informes

### 10.1 Listado de Lotes

1. Navegue a **Consultas > Listado de Lotes**
2. Use los filtros disponibles:
   - Producto
   - Numero de lote
   - Estado / Dictamen
   - Proveedor
   - Rango de fechas de creacion
   - Rango de fechas de vencimiento
3. Haga clic en **Buscar**
4. Exporte resultados si es necesario

### 10.2 Listado de Movimientos

1. Navegue a **Consultas > Listado de Movimientos**
2. Filtre por:
   - Tipo de movimiento (ALTA, BAJA, MODIFICACION)
   - Motivo (COMPRA, VENTA, PRODUCCION, etc.)
   - Lote
   - Usuario
   - Rango de fechas
3. Consulte detalle de cada movimiento

### 10.3 Historial de Cambios (Audit Trail)

Para revisar el historial de cambios de un lote:

1. Acceda al detalle del lote
2. Seleccione la pestana **Historial**
3. Vera la lista cronologica de cambios con:
   - Fecha y hora
   - Usuario
   - Campo modificado
   - Valor anterior
   - Valor nuevo
   - Motivo del cambio

**Nota:** Este historial es de solo lectura y no puede modificarse ni eliminarse.

---

## 11. Administracion

**Nota:** Las funciones de administracion solo estan disponibles para usuarios con rol ADMIN.

### 11.1 Gestion de Usuarios

**Crear nuevo usuario:**

1. Navegue a **Administracion > Usuarios**
2. Haga clic en **Nuevo Usuario**
3. Complete los datos:
   - Username (nombre de usuario)
   - Nombre completo
   - Email
   - Rol
   - Contrasena temporal
4. Haga clic en **Guardar**

El usuario debera cambiar la contrasena en su primer login.

**Modificar usuario:**

1. Seleccione el usuario de la lista
2. Haga clic en **Editar**
3. Modifique los campos necesarios
4. Haga clic en **Guardar**

**Desactivar usuario:**

1. Seleccione el usuario
2. Haga clic en **Desactivar**
3. Confirme la operacion

El usuario no se elimina, solo se desactiva.

**Resetear contrasena:**

1. Seleccione el usuario
2. Haga clic en **Resetear Contrasena**
3. Proporcione la nueva contrasena temporal al usuario

**Desbloquear cuenta:**

1. Seleccione el usuario bloqueado
2. Haga clic en **Desbloquear**
3. El usuario podra intentar login nuevamente

### 11.2 Gestion de Productos

**Crear producto:**

1. Navegue a **Administracion > Productos**
2. Haga clic en **Nuevo Producto**
3. Complete:
   - Codigo
   - Nombre
   - Tipo (materia prima, insumo, producto terminado)
   - Descripcion
   - Unidad de medida predeterminada
4. Haga clic en **Guardar**

**Modificar/Desactivar:**
Similar a la gestion de usuarios.

### 11.3 Gestion de Proveedores

**Crear proveedor:**

1. Navegue a **Administracion > Proveedores**
2. Haga clic en **Nuevo Proveedor**
3. Complete:
   - Codigo
   - Razon social
   - CUIT
   - Contacto
   - Direccion
4. Haga clic en **Guardar**

### 11.4 Configuracion del Sistema

1. Navegue a **Administracion > Configuracion**
2. Se muestra la lista de parametros configurables
3. Modifique los valores segun necesidad
4. Algunos parametros requieren reinicio del sistema

**Parametros configurables:**
- Intentos de login antes de bloqueo
- Tiempo de bloqueo (minutos)
- Dias de expiracion de contrasena
- Longitud minima de contrasena
- Longitud minima de motivo

---

## 12. Auditoria y Trazabilidad

### 12.1 Audit Trail

El sistema registra automaticamente todos los cambios realizados en datos criticos. Cada registro incluye:

- **Quien:** Usuario que realizo el cambio
- **Cuando:** Fecha y hora exacta
- **Que:** Campo modificado
- **Antes:** Valor anterior
- **Despues:** Valor nuevo
- **Por que:** Motivo obligatorio ingresado

**Caracteristicas:**
- El audit trail es inmutable (no puede modificarse ni eliminarse)
- Se conserva por 365 dias minimo
- Disponible para revision en detalle de cada lote

### 12.2 Rol de Auditor

Los usuarios con rol AUDITOR tienen acceso especial:

- Acceso de **solo lectura** a toda la informacion del sistema
- **No pueden** crear, modificar ni eliminar datos
- Todos sus accesos quedan registrados en log especial
- Util para auditorias externas o inspecciones

### 12.3 Trazabilidad de Lotes

El sistema permite trazar:

**Hacia atras (origen):**
- De que lotes de materia prima se produjo un producto
- Que proveedor suministro cada lote

**Hacia adelante (destino):**
- A que productos se destino una materia prima
- Que ventas se realizaron de cada lote

**Casos de uso:**
- Recall: Identificar todos los productos afectados por un lote de MP defectuoso
- Reclamos: Rastrear el origen de un producto reportado

---

## 13. Preguntas Frecuentes

### 13.1 Acceso y Seguridad

**P: Olvide mi contrasena. Que hago?**
R: Contacte al administrador del sistema para que resetee su contrasena.

**P: Mi cuenta esta bloqueada. Cuanto debo esperar?**
R: La cuenta se desbloquea automaticamente despues de 15 minutos, o puede contactar al administrador para desbloqueo inmediato.

**P: Por que me pide cambiar la contrasena?**
R: Las contrasenas expiran cada 180 dias por seguridad. Tambien se requiere cambio en el primer login.

### 13.2 Operaciones de Lotes

**P: No puedo egresar un lote. Por que?**
R: Verifique que el lote este APROBADO y tenga cantidad disponible suficiente.

**P: Por que debo ingresar un motivo tan largo?**
R: El motivo minimo de 20 caracteres es requerido por ANMAT para audit trail. Describa brevemente la razon de la operacion.

**P: Me equivoque en una operacion. Como la corrijo?**
R: Use la funcion de Reverso de Movimiento en el menu Contingencias.

### 13.3 Calidad

**P: Un lote fue rechazado pero era correcto. Que hago?**
R: Use la funcion de Reanalisis para solicitar un nuevo analisis.

**P: Como pongo un lote en cuarentena sin anular el analisis?**
R: Use la funcion Dictamen de Cuarentena en el menu Calidad.

### 13.4 Administracion

**P: Como agrego un nuevo usuario?**
R: Solo el rol ADMIN puede crear usuarios. Navegue a Administracion > Usuarios > Nuevo.

**P: Como desactivo un usuario que ya no trabaja?**
R: Desactivelo desde Administracion > Usuarios. No elimine usuarios para preservar el historial.

---

## 14. Contacto y Soporte

### 14.1 Soporte Tecnico

Para problemas tecnicos con el sistema:

- **Email:** ________________________________
- **Telefono:** ________________________________
- **Horario:** ________________________________

**Informacion a proporcionar:**
1. Nombre de usuario
2. Descripcion del problema
3. Mensaje de error (si aplica)
4. Pasos para reproducir el problema
5. Screenshot si es posible

### 14.2 Consultas Funcionales

Para dudas sobre el uso del sistema o procedimientos:

- **Responsable:** ________________________________
- **Email:** ________________________________

### 14.3 Reportar Errores

Si detecta un error o comportamiento inesperado:

1. No intente corregirlo directamente en la base de datos
2. Documente el error con capturas de pantalla
3. Reporte inmediatamente al soporte tecnico
4. Si afecta datos criticos, notifique tambien a QA

---

## Anexos

### Anexo A: Roles del Sistema

| Rol | Area | Descripcion | Accesos principales |
|-----|------|-------------|---------------------|
| ADMIN | GLOBAL | Administrador | Todo el sistema |
| DT | GLOBAL | Director Tecnico | Aprobaciones finales |
| GERENTE_GARANTIA_CALIDAD | CALIDAD | Gerente QA | Liberaciones, recalls |
| GERENTE_CONTROL_CALIDAD | CALIDAD | Gerente QC | Analisis, muestreo |
| ANALISTA_CONTROL_CALIDAD | CALIDAD | Analista QC | Registro analisis |
| SUPERVISOR_PLANTA | PLANTA | Supervisor | Produccion, compras |
| ANALISTA_PLANTA | PLANTA | Analista | Movimientos basicos |
| AUDITOR | GLOBAL | Auditor | Solo lectura |

### Anexo B: Unidades de Medida

| Codigo | Nombre | Factor conversion |
|--------|--------|-------------------|
| KG | Kilogramo | 1 |
| G | Gramo | 0.001 |
| L | Litro | 1 |
| ML | Mililitro | 0.001 |
| U | Unidad | 1 |

### Anexo C: Estados y Dictamenes

**Flujo tipico de dictamen de un lote:**

```
Ingreso
   |
   v
RECIBIDO (estado NUEVO)
   |
   v
CUARENTENA (en analisis)
   |
   v
[Resultado Analisis]
   |
   +---> APROBADO ---> LIBERADO ---> [Venta/Uso]
   |
   +---> RECHAZADO ---> [Devolucion proveedor / Destruccion]
```

**Nota:** El Dictamen (calidad) y Estado (cantidad) son conceptos separados.
Ver seccion 4.2 para la tabla completa de estados y dictamenes.

### Anexo D: Documentos Relacionados

| Documento | Codigo | Descripcion |
|-----------|--------|-------------|
| Especificaciones de Requerimientos | ERU-001 | Requerimientos funcionales y no funcionales |
| Protocolo de Calificacion | IQOQ-001 | Protocolo IQ/OQ |
| Procedimiento de Backup | SOP-001 | Backup y recuperacion |

---

**Fin del documento**

---

| | Firma | Fecha |
|---|-------|-------|
| **Elaborado por:** | _________________ | ____/____/____ |
| **Revisado por (QA):** | _________________ | ____/____/____ |
| **Aprobado por:** | _________________ | ____/____/____ |
