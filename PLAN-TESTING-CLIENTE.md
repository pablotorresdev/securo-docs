# PLAN DE TESTING COMPLETO - SISTEMA CONITRACK
## Version 2.0 - Validacion Integral para Cliente

**Version:** 2.0
**Fecha:** 2025-12-16
**Sistema:** CONITRACK - Gestion de Stock Farmaceutico
**Cobertura:** 22 Casos de Uso + Seguridad + Backup + Audit Trail

---

# SECCION A: INFORMACION GENERAL

## A.1 Objetivo del Plan

Este plan de testing cubre **TODOS** los casos de uso del sistema Conitrack para garantizar que el cliente pueda validar completamente el sistema antes de su uso en produccion.

## A.2 Resumen de Cobertura

| Categoria | CUs Cubiertos | Tests |
|-----------|---------------|-------|
| Produccion/Compras | CU1-CU11 | 15 |
| Ventas | CU20-CU25 | 8 |
| Contingencias | CU30-CU31 | 4 |
| ABM Maestros | CU40-CU43 | 6 |
| Seguridad | - | 8 |
| Audit Trail | - | 4 |
| Backup/Restore | - | 2 |
| **TOTAL** | **22 CUs** | **47 tests** |

## A.3 Personal Requerido

### Minimo de Personas: 5

| # | Rol | Responsabilidad | Puede Ser |
|---|-----|-----------------|-----------|
| 1 | **Director Tecnico (DT)** | Liberar lotes (CU22), validar final | Farmaceutico matriculado |
| 2 | **Gerente Garantia Calidad (GGC)** | Devoluciones, recall, trazado | Profesional QA senior |
| 3 | **Gerente Control Calidad (GCC)** | Aprobar/rechazar analisis | Profesional QC |
| 4 | **Operador Planta** | Ingresos, consumos, ventas | Puede ser 1 persona con acceso a ANALISTA_PLANTA + SUPERVISOR_PLANTA |
| 5 | **Administrador** | Configuracion, usuarios, backup | Personal IT |

### Opcional (para testing paralelo): +2 personas
- Analista Control Calidad (para CU2, CU3)
- QA Jr para ejecutar tests y documentar

## A.4 Tiempo Estimado Total

| Fase | Duracion |
|------|----------|
| Preparacion | 1 hora |
| Ejecucion tests | 6-8 horas |
| Documentacion | 1 hora |
| **Total** | **8-10 horas** (1-2 dias) |

## A.5 Leyenda de Simbolos

| Simbolo | Significado |
|---------|-------------|
| **[DT]** | Requiere Director Tecnico |
| **[GGC]** | Requiere Gerente Garantia Calidad |
| **[GCC]** | Requiere Gerente Control Calidad |
| **[ACC]** | Requiere Analista Control Calidad |
| **[SP]** | Requiere Supervisor Planta |
| **[AP]** | Requiere Analista Planta |
| **[ADMIN]** | Requiere Administrador |
| :red_circle: | Test CRITICO - Obligatorio para aprobar |
| :orange_circle: | Test IMPORTANTE - Recomendado |
| :green_circle: | Test ESTANDAR |

---

# SECCION B: PREPARACION

## B.1 Checklist Pre-Testing

### Acceso al Sistema
- [ ] URL del sistema: ___________________________
- [ ] Sistema accesible desde red del cliente
- [ ] Navegador compatible (Chrome/Firefox/Edge)

### Credenciales (solicitar a ADMIN)

| Rol | Username | Password | Verificado |
|-----|----------|----------|------------|
| ADMIN | _______________ | _______________ | [ ] |
| DT | _______________ | _______________ | [ ] |
| GERENTE_GARANTIA_CALIDAD | _______________ | _______________ | [ ] |
| GERENTE_CONTROL_CALIDAD | _______________ | _______________ | [ ] |
| ANALISTA_CONTROL_CALIDAD | _______________ | _______________ | [ ] |
| SUPERVISOR_PLANTA | _______________ | _______________ | [ ] |
| ANALISTA_PLANTA | _______________ | _______________ | [ ] |
| AUDITOR | _______________ | _______________ | [ ] |

### Datos Maestros Necesarios
- [ ] Al menos 2 productos tipo API o Excipiente
- [ ] Al menos 1 producto tipo Unidad de Venta (UV)
- [ ] Al menos 2 proveedores (ninguno "Conifarma")
- [ ] Todos los usuarios de prueba activos

### Herramientas
- [ ] Herramienta de captura de pantalla
- [ ] Este documento impreso o en segunda pantalla
- [ ] Reloj visible para registrar tiempos

---

# SECCION C: TESTS DE SEGURIDAD Y ACCESO

**Ejecutor:** Cualquier usuario
**Validador:** [ADMIN] + [QA]

---

## TEST S-01: Login Exitoso :green_circle:

**Rol:** Cualquier usuario

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Ir a URL del sistema | Pagina de login visible | [ ] |
| 2 | Ingresar usuario valido | Campo acepta texto | [ ] |
| 3 | Ingresar password correcta | Caracteres ocultos | [ ] |
| 4 | Click "Iniciar Sesion" | Redirige a pagina principal | [ ] |
| 5 | Verificar header | Nombre de usuario visible | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST S-02: Login Fallido :green_circle:

**Rol:** Cualquier usuario

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Ingresar usuario valido | Campo acepta | [ ] |
| 2 | Ingresar password INCORRECTA | Campo acepta | [ ] |
| 3 | Click "Iniciar Sesion" | Mensaje error GENERICO | [ ] |
| 4 | Verificar mensaje | NO revela si usuario existe | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST S-03: Bloqueo por Intentos Fallidos :red_circle:

**Rol:** Usuario de prueba (que se pueda desbloquear)

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1-5 | 5 intentos con password incorrecta | Error en cada intento | [ ] |
| 6 | Intento 6 con password CORRECTA | **BLOQUEADO** - Acceso denegado | [ ] |
| 7 | Esperar 15 minutos | - | [ ] |
| 8 | Intentar con password correcta | Acceso exitoso | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST S-04: Politica de Contrasenas :red_circle:

**Rol:** Usuario con acceso a cambio de password

**Requisitos de contraseña:**
- Minimo 8 caracteres
- Al menos 1 mayuscula
- Al menos 1 minuscula
- Al menos 1 numero
- Al menos 1 caracter especial (!@#$%^&*()_+-=[]{}|;:,.<>?)

| Password de Prueba | Resultado Esperado | OK |
|-------------------|-------------------|-----|
| "abc" | Rechazada - muy corta | [ ] |
| "abcdefgh" | Rechazada - sin mayuscula/numero/especial | [ ] |
| "ABCDEFGH" | Rechazada - sin minuscula/numero/especial | [ ] |
| "Abcdefgh" | Rechazada - sin numero/especial | [ ] |
| "Abcdefg1" | Rechazada - sin caracter especial | [ ] |
| "Abcdefg1!" | **Aceptada** | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST S-05: AUDITOR Solo Lectura :red_circle:

**Rol:** AUDITOR

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Login como AUDITOR | Acceso exitoso | [ ] |
| 2 | Navegar a lotes | Lista visible (lectura) | [ ] |
| 3 | Buscar boton "Nuevo" o "Alta" | **NO EXISTE** | [ ] |
| 4 | Intentar URL /compras/alta | Error 403 o redireccion | [ ] |
| 5 | Revisar todo el menu | Solo opciones de consulta | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST S-06: Control de Acceso por Rol :orange_circle:

**Objetivo:** Verificar que cada rol solo ve sus opciones

| Rol | Menu Esperado | Verificado |
|-----|---------------|------------|
| ANALISTA_PLANTA | Compras, Ventas, Proveedores | [ ] |
| ANALISTA_CONTROL_CALIDAD | Cuarentena, Muestreo | [ ] |
| SUPERVISOR_PLANTA | Produccion, Consumo | [ ] |
| GERENTE_CONTROL_CALIDAD | Resultado Analisis, Reanalisis | [ ] |
| GERENTE_GARANTIA_CALIDAD | Liberacion, Devolucion, Recall | [ ] |
| DT | Liberacion, Trazado | [ ] |
| ADMIN | Todo + Administracion | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST S-07: Cierre de Sesion :green_circle:

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Estar logueado | Sesion activa | [ ] |
| 2 | Click en cerrar sesion | Redirige a login | [ ] |
| 3 | Presionar "Atras" en navegador | No permite acceso | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST S-08: Timeout de Sesion :orange_circle:

**Nota:** En PROD timeout es 30 min. Puede marcarse N/A si no es practico esperar.

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Login al sistema | Sesion activa | [ ] |
| 2 | Esperar tiempo de timeout | - | [ ] |
| 3 | Intentar navegar | Redirige a login | [ ] |

**Resultado:** [ ] PASO [ ] FALLO [ ] N/A

---

## RESUMEN SECCION C: SEGURIDAD

| Test | Criticidad | Resultado |
|------|------------|-----------|
| S-01 | :green_circle: | [ ] PASO [ ] FALLO |
| S-02 | :green_circle: | [ ] PASO [ ] FALLO |
| S-03 | :red_circle: | [ ] PASO [ ] FALLO |
| S-04 | :red_circle: | [ ] PASO [ ] FALLO |
| S-05 | :red_circle: | [ ] PASO [ ] FALLO |
| S-06 | :orange_circle: | [ ] PASO [ ] FALLO |
| S-07 | :green_circle: | [ ] PASO [ ] FALLO |
| S-08 | :orange_circle: | [ ] PASO [ ] FALLO [ ] N/A |

**Firma Validador:** _________________________ **Fecha:** ____/____/____

---

# SECCION D: CICLO DE COMPRAS (CU1-CU4)

**Flujo:** Ingreso → Cuarentena → Muestreo → Analisis o Devolucion

---

## TEST D-01: Alta Ingreso Compra (CU1) :red_circle:

**Ejecutor:** [AP] Analista Planta

**Datos de Prueba:**
```
Producto: (API o Excipiente existente)
Nro Lote: TEST-COMP-001
Cantidad: 100
Unidad: KILOGRAMO
Proveedor: (NO Conifarma)
Fecha Vencimiento: 31/12/2026
Motivo: "Ingreso de materia prima segun OC-TEST-001 para pruebas de validacion del sistema"
```

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Compras > Alta Ingreso | Formulario visible | [ ] |
| 2 | Seleccionar producto | Producto cargado | [ ] |
| 3 | Ingresar Nro Lote: TEST-COMP-001 | Campo acepta | [ ] |
| 4 | Ingresar Cantidad: 100 | Campo acepta | [ ] |
| 5 | Seleccionar Unidad | Unidad seleccionada | [ ] |
| 6 | Seleccionar Proveedor | Proveedor seleccionado | [ ] |
| 7 | Ingresar Fecha Vencimiento | Fecha aceptada | [ ] |
| 8 | Ingresar Motivo (>20 chars) | Texto aceptado | [ ] |
| 9 | Click Guardar | Mensaje exito | [ ] |
| 10 | Buscar lote en listado | Estado: NUEVO, Dictamen: RECIBIDO | [ ] |

**Codigo lote generado:** _______________

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST D-02: Validacion Motivo Minimo 20 Caracteres :orange_circle:

**Ejecutor:** [AP] Analista Planta

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Iniciar alta de lote | Formulario visible | [ ] |
| 2 | Completar todos los campos | Datos ingresados | [ ] |
| 3 | Motivo: "Prueba" (6 chars) | Texto aceptado | [ ] |
| 4 | Click Guardar | **ERROR: Motivo muy corto** | [ ] |
| 5 | Motivo: "Prueba del sistema de ingreso" | Texto aceptado | [ ] |
| 6 | Click Guardar | Operacion exitosa | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST D-03: Dictamen Cuarentena (CU2) :red_circle:

**Ejecutor:** [ACC] Analista Control Calidad

**Prerrequisito:** Lote de TEST D-01 con dictamen RECIBIDO

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Calidad > Dictamen Cuarentena | Lista de lotes RECIBIDO | [ ] |
| 2 | Buscar lote TEST-COMP-001 | Lote visible | [ ] |
| 3 | Seleccionar lote | Formulario abierto | [ ] |
| 4 | Ingresar Nro Analisis: AN-TEST-001 | Campo acepta | [ ] |
| 5 | Ingresar Motivo | Texto aceptado | [ ] |
| 6 | Click Guardar | Mensaje exito | [ ] |
| 7 | Verificar lote | Dictamen: CUARENTENA | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST D-04: Muestreo (CU3) :orange_circle:

**Ejecutor:** [ACC] Analista Control Calidad

**Prerrequisito:** Lote en CUARENTENA

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Calidad > Muestreo | Lista de lotes en cuarentena | [ ] |
| 2 | Seleccionar lote TEST-COMP-001 | Formulario visible | [ ] |
| 3 | Cantidad muestra: 1 | Campo acepta | [ ] |
| 4 | Motivo: "Extraccion muestra para analisis" | Texto aceptado | [ ] |
| 5 | Click Guardar | Mensaje exito | [ ] |
| 6 | Verificar cantidad lote | Cantidad reducida (99) | [ ] |

**Cantidad antes:** 100 **Cantidad despues:** ______

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST D-05: Devolucion Compra (CU4) :orange_circle:

**Ejecutor:** [AP] Analista Planta

**Prerrequisito:** Crear un nuevo lote para devolver (no usar el de analisis)

**Datos:** Crear lote TEST-DEV-001

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Crear lote TEST-DEV-001 (repetir D-01) | Lote creado | [ ] |
| 2 | Menu > Compras > Devolucion | Lista de lotes disponibles | [ ] |
| 3 | Seleccionar TEST-DEV-001 | Formulario visible | [ ] |
| 4 | Motivo: "Devolucion al proveedor por no cumplir especificaciones de calidad" | Texto aceptado | [ ] |
| 5 | Click Devolver | Mensaje exito | [ ] |
| 6 | Verificar lote | Estado: DEVUELTO | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## RESUMEN SECCION D: COMPRAS

| Test | CU | Ejecutor | Criticidad | Resultado |
|------|----|---------|-----------| ----------|
| D-01 | CU1 | [AP] | :red_circle: | [ ] PASO [ ] FALLO |
| D-02 | - | [AP] | :orange_circle: | [ ] PASO [ ] FALLO |
| D-03 | CU2 | [ACC] | :red_circle: | [ ] PASO [ ] FALLO |
| D-04 | CU3 | [ACC] | :orange_circle: | [ ] PASO [ ] FALLO |
| D-05 | CU4 | [AP] | :orange_circle: | [ ] PASO [ ] FALLO |

**Firma Validador [GCC]:** _________________________ **Fecha:** ____/____/____

---

# SECCION E: CONTROL DE CALIDAD (CU5-CU11)

**Flujo:** Resultado Analisis → Aprobado/Rechazado → Reanalisis si necesario

---

## TEST E-01: Resultado Analisis APROBADO (CU5) :red_circle:

**Ejecutor:** [GCC] Gerente Control Calidad

**Prerrequisito:** Lote TEST-COMP-001 en CUARENTENA con muestra tomada

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Calidad > Resultado Analisis | Lista lotes en cuarentena | [ ] |
| 2 | Seleccionar lote TEST-COMP-001 | Formulario visible | [ ] |
| 3 | Resultado: APROBADO | Seleccionado | [ ] |
| 4 | Fecha Analisis: (hoy) | Fecha aceptada | [ ] |
| 5 | Fecha Reanalisis: (hoy + 1 año) | Fecha aceptada | [ ] |
| 6 | Motivo: "Analisis fisicoquimico completo, todos parametros dentro de especificacion" | Texto aceptado | [ ] |
| 7 | Click Guardar | Mensaje exito | [ ] |
| 8 | Verificar lote | Dictamen: APROBADO | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST E-02: Resultado Analisis RECHAZADO (CU6) :orange_circle:

**Ejecutor:** [GCC] Gerente Control Calidad

**Prerrequisito:** Crear nuevo lote y llevarlo a CUARENTENA

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Crear lote TEST-RECH-001 y llevar a cuarentena | Lote en CUARENTENA | [ ] |
| 2 | Menu > Calidad > Resultado Analisis | Formulario visible | [ ] |
| 3 | Resultado: RECHAZADO | Seleccionado | [ ] |
| 4 | Motivo: "Parametro X fuera de especificacion - no cumple criterios de aceptacion" | Texto aceptado | [ ] |
| 5 | Click Guardar | Mensaje exito | [ ] |
| 6 | Verificar lote | Dictamen: RECHAZADO | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST E-03: Reanalisis de Lote (CU8) :orange_circle:

**Ejecutor:** [GCC] Gerente Control Calidad

**Prerrequisito:** Lote APROBADO (TEST-COMP-001)

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Calidad > Reanalisis | Lista lotes aprobados | [ ] |
| 2 | Seleccionar lote APROBADO | Formulario visible | [ ] |
| 3 | Nro Analisis: AN-REAN-001 | Campo acepta | [ ] |
| 4 | Motivo: "Reanalisis preventivo antes de fecha de vencimiento de analisis" | Texto aceptado | [ ] |
| 5 | Click Guardar | Mensaje exito | [ ] |
| 6 | Verificar | Nuevo analisis creado, lote sigue APROBADO | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST E-04: Anulacion de Analisis (CU11) :orange_circle:

**Ejecutor:** [GCC] Gerente Control Calidad

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Calidad > Anular Analisis | Lista disponible | [ ] |
| 2 | Seleccionar lote con analisis | Formulario visible | [ ] |
| 3 | Motivo: "Anulacion por error en identificacion de muestra" | Texto aceptado | [ ] |
| 4 | Click Anular | Mensaje exito | [ ] |
| 5 | Verificar lote | Estado anterior restaurado | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST E-05: Procesos Automaticos - Verificacion (CU9/CU10) :orange_circle:

**Ejecutor:** [ADMIN]

**Nota:** CU9 (Analisis Expirado) y CU10 (Vencimiento) son automaticos a las 5:00 AM.
Este test verifica que los lotes afectados cambian de estado.

| Verificacion | Descripcion | OK |
|--------------|-------------|-----|
| 1 | Existe proceso programado para 5:00 AM | [ ] |
| 2 | Lotes con fecha reanalisis vencida → ANALISIS_EXPIRADO | [ ] |
| 3 | Lotes con fecha vencimiento pasada → VENCIDO | [ ] |
| 4 | Lotes VENCIDO no permiten operaciones | [ ] |

**Resultado:** [ ] PASO [ ] FALLO [ ] N/A (verificar con ADMIN)

---

## RESUMEN SECCION E: CONTROL CALIDAD

| Test | CU | Ejecutor | Criticidad | Resultado |
|------|----|---------|-----------| ----------|
| E-01 | CU5 | [GCC] | :red_circle: | [ ] PASO [ ] FALLO |
| E-02 | CU6 | [GCC] | :orange_circle: | [ ] PASO [ ] FALLO |
| E-03 | CU8 | [GCC] | :orange_circle: | [ ] PASO [ ] FALLO |
| E-04 | CU11 | [GCC] | :orange_circle: | [ ] PASO [ ] FALLO |
| E-05 | CU9/10 | [ADMIN] | :orange_circle: | [ ] PASO [ ] FALLO [ ] N/A |

**Firma Validador [GGC] o [DT]:** _________________________ **Fecha:** ____/____/____

---

# SECCION F: PRODUCCION (CU7, CU20)

---

## TEST F-01: Ingreso de Produccion (CU20) :red_circle:

**Ejecutor:** [SP] Supervisor Planta

**Datos de Prueba:**
```
Producto: (Unidad de Venta existente)
Nro Lote: 25010001 (formato AAMM####)
Cantidad: 50
Motivo: "Ingreso de produccion segun orden OP-TEST-001"
```

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Produccion > Ingreso | Formulario visible | [ ] |
| 2 | Seleccionar producto UV | Producto cargado | [ ] |
| 3 | Nro Lote: 25010001 | Formato validado | [ ] |
| 4 | Cantidad: 50 | Campo acepta | [ ] |
| 5 | Motivo (>20 chars) | Texto aceptado | [ ] |
| 6 | Click Guardar | Mensaje exito | [ ] |
| 7 | Verificar lote | Estado: NUEVO, Dictamen: RECIBIDO | [ ] |

**Codigo lote:** _______________

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST F-02: Consumo de Produccion (CU7) :red_circle:

**Ejecutor:** [SP] Supervisor Planta

**Prerrequisito:** Lote de materia prima APROBADO con stock

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Produccion > Consumo | Lista lotes aprobados | [ ] |
| 2 | Seleccionar lote MP aprobado | Formulario visible | [ ] |
| 3 | Cantidad a consumir: 10 | Campo acepta | [ ] |
| 4 | Orden de produccion: OP-TEST-001 | Campo acepta | [ ] |
| 5 | Motivo: "Consumo para fabricacion de lote 25010001" | Texto aceptado | [ ] |
| 6 | Click Guardar | Mensaje exito | [ ] |
| 7 | Verificar cantidad lote | Cantidad reducida | [ ] |

**Cantidad antes:** ______ **Cantidad despues:** ______

**Resultado:** [ ] PASO [ ] FALLO

---

## RESUMEN SECCION F: PRODUCCION

| Test | CU | Ejecutor | Criticidad | Resultado |
|------|----|---------|-----------| ----------|
| F-01 | CU20 | [SP] | :red_circle: | [ ] PASO [ ] FALLO |
| F-02 | CU7 | [SP] | :red_circle: | [ ] PASO [ ] FALLO |

**Firma Validador [GGC]:** _________________________ **Fecha:** ____/____/____

---

# SECCION G: VENTAS (CU21-CU25)

**IMPORTANTE - Orden de Operaciones:**
El flujo correcto para lotes de Unidad de Venta (UV) es:
1. **Aprobar** el lote (CU5)
2. **Trazar** el lote - asignar numeros de traza (CU21)
3. **Liberar** el lote para venta (CU22)
4. **Vender** el producto (CU23)

> **Nota:** Una vez que un lote es LIBERADO sin trazas asignadas, ya NO puede ser trazado posteriormente.
> Por ello, el trazado debe realizarse ANTES de la liberacion.

---

## TEST G-01: Trazado de Lote (CU21) :orange_circle:

**Ejecutor:** [DT] o [GGC]

**Prerrequisito:** Lote UV APROBADO (llevar lote 25010001 por cuarentena → aprobacion CU2 y CU5)

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Llevar lote UV por CU2 y CU5 | Dictamen: APROBADO | [ ] |
| 2 | Menu > Ventas > Trazado | Lista lotes aprobados | [ ] |
| 3 | Seleccionar lote 25010001 | Formulario visible | [ ] |
| 4 | Cantidad a trazar: 20 | Campo acepta | [ ] |
| 5 | Motivo: "Asignacion trazas para comercializacion" | Texto aceptado | [ ] |
| 6 | Click Guardar | Mensaje exito + rango trazas | [ ] |
| 7 | Verificar lote | Trazas asignadas | [ ] |

**Rango trazas:** _______________ a _______________

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST G-02: Liberacion de Lote (CU22) :red_circle:

**Ejecutor:** [DT] Director Tecnico o [GGC]

**IMPORTANTE:** Este test DEBE ser ejecutado o validado por el Director Tecnico.

**Prerrequisito:** Lote UV APROBADO con trazas asignadas (G-01 completado)

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Ventas > Liberar Lote | Lista lotes UV aprobados con trazas | [ ] |
| 2 | Seleccionar lote 25010001 | Formulario visible | [ ] |
| 3 | Verificar datos | Informacion correcta, trazas visibles | [ ] |
| 4 | Motivo: "Liberacion de lote segun revision de expediente completo" | Texto aceptado | [ ] |
| 5 | Click Liberar | Mensaje exito | [ ] |
| 6 | Verificar lote | Dictamen: LIBERADO | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

**Firma Director Tecnico:** _________________________

---

## TEST G-03: Venta de Producto (CU23) :red_circle:

**Ejecutor:** [AP] Analista Planta

**Prerrequisito:** Lote LIBERADO (con o sin trazas)

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Ventas > Egreso | Formulario visible | [ ] |
| 2 | Seleccionar lote LIBERADO | Lote cargado | [ ] |
| 3 | Cantidad: 5 | Campo acepta | [ ] |
| 4 | Motivo: "Venta a cliente ABC segun pedido PED-001" | Texto aceptado | [ ] |
| 5 | Click Guardar | Mensaje exito | [ ] |
| 6 | Verificar cantidad | Stock reducido | [ ] |

**Cantidad antes:** ______ **Cantidad despues:** ______

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST G-04: Devolucion de Venta (CU24) :orange_circle:

**Ejecutor:** [GGC] Gerente Garantia Calidad

**Prerrequisito:** Venta registrada (G-03)

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Ventas > Devolucion Venta | Formulario visible | [ ] |
| 2 | Seleccionar lote vendido | Lote cargado | [ ] |
| 3 | Cantidad devuelta: 2 | Campo acepta | [ ] |
| 4 | Motivo: "Devolucion cliente por exceso pedido" | Texto aceptado | [ ] |
| 5 | Click Guardar | Mensaje exito | [ ] |
| 6 | Verificar | Existe lote derivado _D_1 | [ ] |

**Lote derivado:** _______________

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST G-05: Retiro de Mercado - Recall (CU25) :red_circle:

**Ejecutor:** [GGC] Gerente Garantia Calidad

**ADVERTENCIA:** Usar lote de prueba exclusivo para este test.

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Crear lote UV de prueba para recall | Lote LIBERADO/VENDIDO | [ ] |
| 2 | Menu > Contingencias > Retiro Mercado | Formulario visible | [ ] |
| 3 | Seleccionar lote de prueba | Lote seleccionado | [ ] |
| 4 | Motivo: "Retiro mercado por alerta calidad - PRUEBA" | Texto aceptado | [ ] |
| 5 | Click Aplicar Recall | Mensaje exito | [ ] |
| 6 | Verificar dictamen | RETIRO_MERCADO | [ ] |
| 7 | Intentar vender este lote | **BLOQUEADO** | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## RESUMEN SECCION G: VENTAS

| Test | CU | Ejecutor | Criticidad | Resultado |
|------|----|---------|-----------| ----------|
| G-01 | CU21 | [DT]/[GGC] | :orange_circle: | [ ] PASO [ ] FALLO |
| G-02 | CU22 | **[DT]** | :red_circle: | [ ] PASO [ ] FALLO |
| G-03 | CU23 | [AP] | :red_circle: | [ ] PASO [ ] FALLO |
| G-04 | CU24 | [GGC] | :orange_circle: | [ ] PASO [ ] FALLO |
| G-05 | CU25 | [GGC] | :red_circle: | [ ] PASO [ ] FALLO |

> **Orden requerido:** G-01 (Trazado) → G-02 (Liberacion) → G-03 (Venta)

**Firma [DT]:** _________________________ **Fecha:** ____/____/____

---

# SECCION H: CONTINGENCIAS (CU30-CU31)

---

## TEST H-01: Ajuste de Stock (CU30) :orange_circle:

**Ejecutor:** [SP] o [AP]

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Contingencias > Ajuste Stock | Lista lotes | [ ] |
| 2 | Seleccionar lote con stock | Formulario visible | [ ] |
| 3 | Nueva cantidad (ajuste) | Campo acepta | [ ] |
| 4 | Motivo: "Ajuste por diferencia inventario fisico" | Texto aceptado | [ ] |
| 5 | Click Guardar | Mensaje exito | [ ] |
| 6 | Verificar cantidad | Cantidad actualizada | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST H-02: Reverso de Movimiento (CU31) :red_circle:

**Ejecutor:** Usuario que creo el movimiento

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Realizar cualquier operacion | Movimiento creado | [ ] |
| 2 | Menu > Contingencias > Reverso | Lista movimientos | [ ] |
| 3 | Buscar movimiento reciente | Movimiento visible | [ ] |
| 4 | Click Revertir | Formulario visible | [ ] |
| 5 | Motivo: "Reverso por error en datos ingresados" | Texto aceptado | [ ] |
| 6 | Click Confirmar | Mensaje exito | [ ] |
| 7 | Verificar lote | Estado anterior restaurado | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST H-03: Reverso - Validacion Jerarquia :orange_circle:

**Objetivo:** Usuario inferior NO puede revertir movimiento de superior

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Login como GERENTE | Sesion activa | [ ] |
| 2 | Crear movimiento | Movimiento registrado | [ ] |
| 3 | Logout, Login como ANALISTA | Sesion activa | [ ] |
| 4 | Intentar revertir movimiento | **ERROR - No autorizado** | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## RESUMEN SECCION H: CONTINGENCIAS

| Test | CU | Ejecutor | Criticidad | Resultado |
|------|----|---------|-----------| ----------|
| H-01 | CU30 | [SP]/[AP] | :orange_circle: | [ ] PASO [ ] FALLO |
| H-02 | CU31 | * | :red_circle: | [ ] PASO [ ] FALLO |
| H-03 | CU31 | * | :orange_circle: | [ ] PASO [ ] FALLO |

**Firma Validador [GGC]:** _________________________ **Fecha:** ____/____/____

---

# SECCION I: AUDIT TRAIL

---

## TEST I-01: Registro en Audit Trail :red_circle:

**Ejecutor:** [QA]

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Realizar cualquier operacion | Operacion exitosa | [ ] |
| 2 | Ir a detalle del lote > Historial | Lista cambios visible | [ ] |
| 3 | Verificar registro | Cambio documentado | [ ] |

**Checklist campos del registro:**
- [ ] Usuario que realizo cambio
- [ ] Fecha y hora
- [ ] Tipo de operacion
- [ ] Valor anterior (si aplica)
- [ ] Valor nuevo
- [ ] Motivo del cambio

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST I-02: Audit Trail No Modificable :red_circle:

**Ejecutor:** [QA]

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Ir a historial de cualquier lote | Lista visible | [ ] |
| 2 | Buscar boton "Editar" | **NO EXISTE** | [ ] |
| 3 | Buscar boton "Eliminar" | **NO EXISTE** | [ ] |
| 4 | Confirmar interfaz solo lectura | Sin controles edicion | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST I-03: Registro de Accesos :orange_circle:

**Ejecutor:** [ADMIN]

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Admin > Log Accesos | Lista visible | [ ] |
| 2 | Buscar login reciente | Registro encontrado | [ ] |
| 3 | Verificar datos | Usuario, fecha, hora, IP | [ ] |

**Resultado:** [ ] PASO [ ] FALLO [ ] N/A

---

## RESUMEN SECCION I: AUDIT TRAIL

| Test | Criticidad | Resultado |
|------|------------|-----------|
| I-01 | :red_circle: | [ ] PASO [ ] FALLO |
| I-02 | :red_circle: | [ ] PASO [ ] FALLO |
| I-03 | :orange_circle: | [ ] PASO [ ] FALLO [ ] N/A |

**Firma [GGC]:** _________________________ **Fecha:** ____/____/____

---

# SECCION J: ADMINISTRACION (CU40-CU43)

---

## TEST J-01: ABM Usuarios (CU43) :red_circle:

**Ejecutor:** [ADMIN]

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Admin > Usuarios | Lista visible | [ ] |
| 2 | Click Nuevo Usuario | Formulario visible | [ ] |
| 3 | Completar datos | Campos aceptan | [ ] |
| 4 | Guardar | Usuario creado | [ ] |
| 5 | Editar usuario | Cambios guardados | [ ] |
| 6 | Desbloquear usuario bloqueado | Usuario desbloqueado | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST J-02: ABM Productos (CU41) :orange_circle:

**Ejecutor:** [GCC] o [ADMIN]

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Admin > Productos | Lista visible | [ ] |
| 2 | Nuevo Producto | Formulario visible | [ ] |
| 3 | Completar datos | Campos aceptan | [ ] |
| 4 | Guardar | Producto creado | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST J-03: ABM Proveedores (CU40) :orange_circle:

**Ejecutor:** [AP] o [ADMIN]

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Admin > Proveedores | Lista visible | [ ] |
| 2 | Nuevo Proveedor | Formulario visible | [ ] |
| 3 | Completar datos | Campos aceptan | [ ] |
| 4 | Guardar | Proveedor creado | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST J-04: Configuracion Sistema (CU42) :orange_circle:

**Ejecutor:** [ADMIN]

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Menu > Admin > Configuracion | Lista parametros | [ ] |
| 2 | Ver categorias | Seguridad, Lotes, UI, etc. | [ ] |
| 3 | Modificar un parametro | Cambio guardado | [ ] |
| 4 | Verificar en Audit Trail | Cambio registrado | [ ] |

**Resultado:** [ ] PASO [ ] FALLO

---

## RESUMEN SECCION J: ADMINISTRACION

| Test | CU | Ejecutor | Criticidad | Resultado |
|------|----|---------|-----------| ----------|
| J-01 | CU43 | [ADMIN] | :red_circle: | [ ] PASO [ ] FALLO |
| J-02 | CU41 | [GCC] | :orange_circle: | [ ] PASO [ ] FALLO |
| J-03 | CU40 | [AP] | :orange_circle: | [ ] PASO [ ] FALLO |
| J-04 | CU42 | [ADMIN] | :orange_circle: | [ ] PASO [ ] FALLO |

**Firma [ADMIN]:** _________________________ **Fecha:** ____/____/____

---

# SECCION K: BACKUP Y RESTORE

---

## TEST K-01: Verificar Backup Configurado :red_circle:

**Ejecutor:** [ADMIN]

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Verificar configuracion backup | Job/cron configurado | [ ] |
| 2 | Listar archivos backup | Al menos 1 backup existe | [ ] |
| 3 | Verificar fecha ultimo backup | < 7 dias | [ ] |
| 4 | Verificar tamano | > 1 MB | [ ] |

**Archivo backup:** _______________
**Fecha:** _______________
**Tamano:** _______________ MB

**Resultado:** [ ] PASO [ ] FALLO

---

## TEST K-02: Prueba de Restore :red_circle:

**Ejecutor:** [ADMIN]

**IMPORTANTE:** Ejecutar en ambiente de prueba, NO en produccion.

| Paso | Accion | Resultado Esperado | OK |
|------|--------|-------------------|-----|
| 1 | Seleccionar backup | Archivo identificado | [ ] |
| 2 | Crear BD de prueba | BD creada | [ ] |
| 3 | Ejecutar restore | Sin errores | [ ] |
| 4 | Verificar tabla lotes | Registros > 0 | [ ] |
| 5 | Verificar tabla users | Registros > 0 | [ ] |
| 6 | Eliminar BD de prueba | Limpieza OK | [ ] |

**Tiempo de restore:** _______________ minutos

**Resultado:** [ ] PASO [ ] FALLO

---

## RESUMEN SECCION K: BACKUP

| Test | Criticidad | Resultado |
|------|------------|-----------|
| K-01 | :red_circle: | [ ] PASO [ ] FALLO |
| K-02 | :red_circle: | [ ] PASO [ ] FALLO |

**Firma [ADMIN]:** _________________________ **Fecha:** ____/____/____

---

# SECCION L: RESUMEN EJECUTIVO

## L.1 Resultados por Seccion

| Seccion | Tests | Pasaron | Fallaron | % |
|---------|-------|---------|----------|---|
| C. Seguridad | 8 | ___ | ___ | ___% |
| D. Compras | 5 | ___ | ___ | ___% |
| E. Control Calidad | 5 | ___ | ___ | ___% |
| F. Produccion | 2 | ___ | ___ | ___% |
| G. Ventas | 5 | ___ | ___ | ___% |
| H. Contingencias | 3 | ___ | ___ | ___% |
| I. Audit Trail | 3 | ___ | ___ | ___% |
| J. Administracion | 4 | ___ | ___ | ___% |
| K. Backup | 2 | ___ | ___ | ___% |
| **TOTAL** | **37** | ___ | ___ | ___% |

## L.2 Tests Criticos (:red_circle:) - OBLIGATORIOS

| Test | Descripcion | Resultado |
|------|-------------|-----------|
| S-03 | Bloqueo intentos fallidos | [ ] PASO [ ] FALLO |
| S-04 | Politica contrasenas | [ ] PASO [ ] FALLO |
| S-05 | AUDITOR solo lectura | [ ] PASO [ ] FALLO |
| D-01 | Alta ingreso compra (CU1) | [ ] PASO [ ] FALLO |
| D-03 | Dictamen cuarentena (CU2) | [ ] PASO [ ] FALLO |
| E-01 | Resultado aprobado (CU5) | [ ] PASO [ ] FALLO |
| F-01 | Ingreso produccion (CU20) | [ ] PASO [ ] FALLO |
| F-02 | Consumo produccion (CU7) | [ ] PASO [ ] FALLO |
| G-02 | Liberacion lote (CU22) **[DT]** | [ ] PASO [ ] FALLO |
| G-03 | Venta producto (CU23) | [ ] PASO [ ] FALLO |
| G-05 | Recall (CU25) | [ ] PASO [ ] FALLO |
| H-02 | Reverso movimiento (CU31) | [ ] PASO [ ] FALLO |
| I-01 | Registro audit trail | [ ] PASO [ ] FALLO |
| I-02 | Audit trail no modificable | [ ] PASO [ ] FALLO |
| J-01 | ABM usuarios (CU43) | [ ] PASO [ ] FALLO |
| K-01 | Backup configurado | [ ] PASO [ ] FALLO |
| K-02 | Prueba restore | [ ] PASO [ ] FALLO |

**Total criticos:** 17 | **Pasaron:** ___ | **Fallaron:** ___

## L.3 Criterios de Aceptacion

Para aprobar el sistema:

- [ ] **100% de tests criticos PASARON** (17/17)
- [ ] **85% o mas de tests totales PASARON** (32/37 minimo)
- [ ] **Todas las firmas de validacion obtenidas**
- [ ] **Sin desviaciones criticas abiertas**

## L.4 Desviaciones

| # | Test | Descripcion | Criticidad | Estado |
|---|------|-------------|------------|--------|
| 1 | | | [ ] Alta [ ] Media [ ] Baja | [ ] Abierto [ ] Cerrado |
| 2 | | | [ ] Alta [ ] Media [ ] Baja | [ ] Abierto [ ] Cerrado |
| 3 | | | [ ] Alta [ ] Media [ ] Baja | [ ] Abierto [ ] Cerrado |

---

## L.5 Conclusion Final

### Resultado:

- [ ] **APROBADO** - Sistema cumple todos los criterios
- [ ] **APROBADO CON OBSERVACIONES** - Desviaciones menores documentadas
- [ ] **NO APROBADO** - Requiere correcciones

### Comentarios:

___________________________________________________________________________

___________________________________________________________________________

---

## L.6 Firmas de Aprobacion Final

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| **Director Tecnico** | _________________ | _________________ | ____/____/____ |
| **Gerente Garantia Calidad** | _________________ | _________________ | ____/____/____ |
| **Gerente Control Calidad** | _________________ | _________________ | ____/____/____ |
| **System Owner** | _________________ | _________________ | ____/____/____ |
| **Ejecutor Principal** | _________________ | _________________ | ____/____/____ |

---

**FIN DEL DOCUMENTO**

---

*Plan de Testing v2.0 - Cobertura completa 22 CUs*
*Sistema CONITRACK - Diciembre 2025*
