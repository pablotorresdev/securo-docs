# GUIA-001: Proximos Pasos para Auditoria ANMAT
## Sistema Conitrack - Gestion de Stock Farmaceutico

---

**Codigo documento:** GUIA-001
**Version:** 1.0
**Fecha:** 2025-12-16
**Proposito:** Guia practica para completar la preparacion de auditoria ANMAT

---

## Indice

1. [Resumen de Documentacion Existente](#1-resumen-de-documentacion-existente)
2. [Clasificacion de Documentos](#2-clasificacion-de-documentos)
3. [Metodos de Firma](#3-metodos-de-firma)
4. [Fases de Preparacion](#4-fases-de-preparacion)
5. [Fase 1: Preparacion de Documentos](#5-fase-1-preparacion-de-documentos)
6. [Fase 2: Aprobacion de Documentos](#6-fase-2-aprobacion-de-documentos)
7. [Fase 3: Ejecucion de Validacion](#7-fase-3-ejecucion-de-validacion)
8. [Fase 4: Cierre de Validacion](#8-fase-4-cierre-de-validacion)
9. [Estructura de Carpetas para Auditoria](#9-estructura-de-carpetas-para-auditoria)
10. [Checklist Completa](#10-checklist-completa)
11. [Preguntas Frecuentes](#11-preguntas-frecuentes)
12. [Anexos](#12-anexos)

---

## 1. Resumen de Documentacion Existente

### 1.1 Documentos Formales Completados

El sistema Conitrack cuenta con 6 documentos formales completos:

| Codigo | Nombre | Lineas | Estado |
|--------|--------|--------|--------|
| ERU-001 | Especificaciones de Requerimientos de Usuario | ~1010 | Completo |
| IQOQ-001 | Protocolo de Calificacion (IQ/OQ) | ~1063 | Completo |
| SOP-001 | Procedimiento de Backup y Recuperacion | ~784 | Completo |
| MAN-001 | Manual de Usuario | ~910 | Completo |
| ROL-001 | Designacion de Roles | ~350 | Completo |
| RSK-001 | Evaluacion de Riesgos | ~500 | Completo |

### 1.2 Ubicacion de Documentos

```
docs/ANMAT/
├── ANMAT-PENDIENTES.md                  <- Estado general del proyecto
├── ANMAT-disp4159-385329-164-168.txt    <- Texto de la regulacion Anexo 6
└── documentacion-formal/
    ├── ERU-001-ESPECIFICACIONES-REQUERIMIENTOS.md
    ├── IQOQ-001-PROTOCOLO-CALIFICACION.md
    ├── SOP-001-BACKUP-RECUPERACION.md
    ├── MAN-001-MANUAL-USUARIO.md
    ├── ROL-001-DESIGNACION-ROLES.md
    ├── RSK-001-EVALUACION-RIESGOS.md
    └── GUIA-001-PROXIMOS-PASOS-AUDITORIA.md  <- Este documento
```

### 1.3 Estado Actual

- **Documentacion tecnica:** COMPLETA
- **Documentacion formal:** COMPLETA (requiere firmas)
- **Ejecucion IQ/OQ:** PENDIENTE
- **Evidencia de validacion:** PENDIENTE

---

## 2. Clasificacion de Documentos

### 2.1 Documentos que Requieren Firma Formal

Estos documentos establecen politicas, procedimientos, responsabilidades o resultados de validacion. **Deben tener firmas de aprobacion** para ser validos ante una auditoria.

| Documento | Tipo | Firmas Requeridas | Momento de Firma |
|-----------|------|-------------------|------------------|
| **ROL-001** | Designacion | Direccion + cada persona designada | Antes de todo |
| **RSK-001** | Evaluacion | System Owner, QA | Antes de IQ/OQ |
| **SOP-001** | Procedimiento | System Owner, QA | Antes de IQ/OQ |
| **ERU-001** | Especificacion | Process Owner, System Owner | Antes de IQ/OQ |
| **IQOQ-001** (pre) | Protocolo | System Owner, QA | Antes de ejecutar |
| **IQOQ-001** (post) | Resultados | Ejecutor, QA, System Owner | Despues de ejecutar |

### 2.2 Documentos de Evidencia (Se generan al ejecutar)

Estos documentos se completan DURANTE la ejecucion de pruebas. Son registros de lo que se hizo y los resultados obtenidos.

| Documento | Contenido | Cuando se Genera |
|-----------|-----------|------------------|
| Resultados IQOQ-001 | Resultados de cada test case | Durante ejecucion IQ/OQ |
| Screenshots de evidencia | Capturas de pantalla | Durante ejecucion IQ/OQ |
| Registro de backup | Log de ejecucion de backup | Al ejecutar backup |
| Registro de restore | Evidencia de prueba de restore | Al probar restore |
| Registro de desviaciones | Problemas encontrados | Si hay desviaciones |

### 2.3 Documentos Digitales (Solo referencia)

Estos documentos permanecen en formato digital para consulta. No requieren firma fisica pero deben estar disponibles.

| Documento | Proposito | Formato |
|-----------|-----------|---------|
| **MAN-001** | Referencia para usuarios | PDF o web |
| **ANMAT-PENDIENTES.md** | Control interno del estado | Markdown |
| **CLAUDE.md** | Documentacion tecnica | Markdown |
| **Codigo fuente** | Referencia tecnica | Repositorio Git |

### 2.4 Diagrama de Flujo de Documentos

```
                    DOCUMENTOS BASE
                    (Firmar primero)
                          │
    ┌─────────────────────┼─────────────────────┐
    │                     │                     │
    ▼                     ▼                     ▼
┌─────────┐         ┌─────────┐          ┌─────────┐
│ ROL-001 │         │ RSK-001 │          │ ERU-001 │
│ (Roles) │         │(Riesgos)│          │ (Reqs)  │
└────┬────┘         └────┬────┘          └────┬────┘
     │                   │                    │
     └───────────────────┼────────────────────┘
                         │
                         ▼
                   ┌─────────┐
                   │ SOP-001 │
                   │(Backup) │
                   └────┬────┘
                        │
                        ▼
              ┌─────────────────┐
              │ IQOQ-001 (pre)  │
              │ Protocolo       │
              │ aprobado        │
              └────────┬────────┘
                       │
                       ▼
              ┌─────────────────┐
              │   EJECUCION     │
              │   IQ/OQ         │
              │   + Screenshots │
              └────────┬────────┘
                       │
                       ▼
              ┌─────────────────┐
              │ IQOQ-001 (post) │
              │ Resultados      │
              │ firmados        │
              └────────┬────────┘
                       │
                       ▼
              ┌─────────────────┐
              │ CARPETA FINAL   │
              │ AUDITORIA       │
              └─────────────────┘
```

---

## 3. Metodos de Firma

### 3.1 Opciones Disponibles

Existen varias opciones para firmar documentos, ordenadas de mas a menos recomendada:

#### Opcion A: Firma Digital Certificada (Recomendada)

**Descripcion:** Firma electronica con certificado digital emitido por autoridad certificante.

**Ventajas:**
- Maxima validez legal
- No repudio garantizado
- Trazabilidad completa
- Auditores la aceptan sin objeciones

**Herramientas:**
- Token de firma digital (ej: Certificado AFIP)
- Firma digital de colegio profesional
- Servicios como Firmar.gob.ar

**Proceso:**
1. Convertir documento a PDF
2. Firmar con certificado digital
3. Verificar firma
4. Archivar PDF firmado

#### Opcion B: Firma Electronica Simple (PDF Firmable)

**Descripcion:** Firma electronica sin certificado, usando plataformas de firma.

**Ventajas:**
- Practico y rapido
- Registro de quien firmo y cuando
- Aceptado por la mayoria de auditores

**Herramientas:**
- Adobe Sign
- DocuSign
- HelloSign
- Autenti

**Proceso:**
1. Subir PDF a plataforma
2. Definir campos de firma
3. Enviar a firmantes
4. Descargar documento firmado

#### Opcion C: Firma Manuscrita en Impreso (Tradicional)

**Descripcion:** Imprimir documento, firmar fisicamente, escanear.

**Ventajas:**
- Metodo tradicional, siempre aceptado
- No requiere tecnologia especial
- Algunos auditores lo prefieren

**Desventajas:**
- Mas trabajo administrativo
- Requiere almacenamiento fisico
- Dificil de gestionar versiones

**Proceso:**
1. Convertir documento a PDF
2. Imprimir
3. Firmar manuscritamente
4. Escanear a PDF
5. Archivar fisico y digital

#### Opcion D: Firma Electronica Basica (Imagen de firma)

**Descripcion:** Insertar imagen de firma en documento PDF.

**Ventajas:**
- Simple y rapido
- No requiere herramientas especiales

**Desventajas:**
- Menor validez legal
- Algunos auditores pueden objetar
- Facil de falsificar

**Proceso:**
1. Crear imagen de firma
2. Insertar en PDF
3. Agregar fecha manualmente
4. Archivar

### 3.2 Recomendacion por Tipo de Documento

| Documento | Metodo Recomendado | Alternativa |
|-----------|-------------------|-------------|
| ROL-001 (Designacion de roles) | Firma digital o manuscrita | PDF firmable |
| RSK-001 (Evaluacion de riesgos) | PDF firmable | Manuscrita |
| SOP-001 (Procedimiento backup) | PDF firmable | Manuscrita |
| ERU-001 (Requerimientos) | PDF firmable | Manuscrita |
| IQOQ-001 (Protocolo y resultados) | Manuscrita | PDF firmable |
| Registros de backup/restore | Manuscrita | PDF firmable |

### 3.3 Consideraciones para Auditoria ANMAT

**Lo que busca el auditor:**

1. **Trazabilidad:** Saber quien aprobo cada documento y cuando
2. **Autenticidad:** Que las firmas sean de las personas indicadas
3. **Integridad:** Que el documento no fue modificado despues de firmarse
4. **Disponibilidad:** Poder acceder a los documentos facilmente

**Recomendaciones:**

- Si tienen sistema de gestion documental, usarlo
- Si no, PDF firmable es la opcion mas practica
- Mantener registro de firmas (quienes pueden firmar que)
- Conservar versiones anteriores de documentos

---

## 4. Fases de Preparacion

### 4.1 Vision General

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PREPARACION AUDITORIA ANMAT                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  FASE 1          FASE 2           FASE 3           FASE 4           │
│  Preparacion     Aprobacion       Ejecucion        Cierre           │
│  (1-2 dias)      (2-3 dias)       (1-2 dias)       (1 dia)          │
│                                                                      │
│  ┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐       │
│  │Convertir│     │ Firmar  │     │Ejecutar │     │Compilar │       │
│  │ a PDF   │────▶│documentos────▶│ IQ/OQ   │────▶│evidencia│       │
│  │         │     │         │     │         │     │         │       │
│  │Completar│     │Obtener  │     │Capturar │     │ Firmar  │       │
│  │ campos  │     │firmas   │     │screenshots    │resultados       │
│  └─────────┘     └─────────┘     └─────────┘     └─────────┘       │
│                                                                      │
│  Entregables:    Entregables:    Entregables:    Entregables:       │
│  - PDFs listos   - Docs firmados - Screenshots   - IQOQ firmado     │
│  - Campos OK     - Pre-ejecucion - Resultados    - Carpeta final    │
│                    aprobada      - Desviaciones  - Archivo          │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2 Tiempo Estimado

| Fase | Duracion | Depende de |
|------|----------|------------|
| Fase 1: Preparacion | 1-2 dias | Disponibilidad de informacion |
| Fase 2: Aprobacion | 2-3 dias | Disponibilidad de firmantes |
| Fase 3: Ejecucion | 1-2 dias | Complejidad de pruebas |
| Fase 4: Cierre | 1 dia | Cantidad de desviaciones |
| **Total** | **5-8 dias** | |

### 4.3 Roles Involucrados

| Rol | Responsabilidad Principal | Fase |
|-----|---------------------------|------|
| System Owner | Aprobar documentos, autorizar ejecucion | 2, 4 |
| Process Owner | Validar requerimientos, confirmar funcionamiento | 2, 3 |
| QA | Verificar documentacion, aprobar resultados | 2, 3, 4 |
| IT | Ejecutar pruebas tecnicas, soporte | 3 |
| Ejecutor IQ/OQ | Ejecutar pruebas, documentar resultados | 3 |

---

## 5. Fase 1: Preparacion de Documentos

### 5.1 Objetivo

Preparar todos los documentos para firma: convertir a PDF, completar campos pendientes, verificar contenido.

### 5.2 Tareas Detalladas

#### 5.2.1 Convertir Documentos a PDF

**Documentos a convertir:**

| Documento | Archivo Origen | Archivo Destino |
|-----------|----------------|-----------------|
| ERU-001 | ERU-001-ESPECIFICACIONES-REQUERIMIENTOS.md | ERU-001-ESPECIFICACIONES-REQUERIMIENTOS.pdf |
| IQOQ-001 | IQOQ-001-PROTOCOLO-CALIFICACION.md | IQOQ-001-PROTOCOLO-CALIFICACION.pdf |
| SOP-001 | SOP-001-BACKUP-RECUPERACION.md | SOP-001-BACKUP-RECUPERACION.pdf |
| MAN-001 | MAN-001-MANUAL-USUARIO.md | MAN-001-MANUAL-USUARIO.pdf |
| ROL-001 | ROL-001-DESIGNACION-ROLES.md | ROL-001-DESIGNACION-ROLES.pdf |
| RSK-001 | RSK-001-EVALUACION-RIESGOS.md | RSK-001-EVALUACION-RIESGOS.pdf |

**Herramientas para conversion:**

- **VS Code + Extension Markdown PDF:** Recomendado para documentos tecnicos
- **Pandoc:** Linea de comandos, muy flexible
- **Typora:** Editor con export a PDF
- **Online:** md2pdf.netlify.app o similar

**Comando Pandoc (ejemplo):**
```bash
pandoc ERU-001-ESPECIFICACIONES-REQUERIMIENTOS.md -o ERU-001-ESPECIFICACIONES-REQUERIMIENTOS.pdf --pdf-engine=wkhtmltopdf
```

#### 5.2.2 Completar Campos Pendientes

**ROL-001 - Designacion de Roles:**

| Campo | Seccion | Valor a Completar |
|-------|---------|-------------------|
| Nombre System Owner | 3.1 | Nombre completo de la persona |
| Cargo System Owner | 3.1 | Cargo en la organizacion |
| Area System Owner | 3.1 | Area/departamento |
| Nombre Process Owner | 3.2 | Nombre completo |
| Cargo Process Owner | 3.2 | Cargo |
| Area Process Owner | 3.2 | Area/departamento |
| Nombre IT | 3.3 | Nombre completo |
| Nombre QA | 3.4 | Nombre completo |
| Nombre Desarrollador | 3.5 | Nombre completo |
| Delegados | 7 | Nombres de delegados |

**SOP-001 - Procedimiento de Backup:**

| Campo | Seccion | Valor a Completar |
|-------|---------|-------------------|
| Contacto emergencia IT | 9.1 | Telefono y email |
| Contacto System Owner | 9.1 | Telefono y email |
| Contacto QA | 9.1 | Telefono y email |
| Ubicacion backups | 6 | Ruta especifica |

**MAN-001 - Manual de Usuario:**

| Campo | Seccion | Valor a Completar |
|-------|---------|-------------------|
| URL del sistema | 1.3 | URL de produccion |
| Email soporte | 14.1 | Email de soporte |
| Telefono soporte | 14.1 | Telefono |
| Horario soporte | 14.1 | Horario de atencion |
| Responsable funcional | 14.2 | Nombre y email |

#### 5.2.3 Verificar Contenido

**Checklist de verificacion por documento:**

**ERU-001:**
- [ ] Todos los requisitos tienen ID unico
- [ ] Matriz de trazabilidad completa
- [ ] Referencias a Anexo 6 correctas

**IQOQ-001:**
- [ ] Todos los test cases tienen ID unico
- [ ] Procedimientos claros paso a paso
- [ ] Campos de resultado vacios (se llenan al ejecutar)
- [ ] Matriz de trazabilidad a ERU-001

**SOP-001:**
- [ ] Comandos correctos para el entorno
- [ ] Rutas de archivos correctas
- [ ] Frecuencias de backup definidas

**ROL-001:**
- [ ] Todos los roles necesarios definidos
- [ ] Responsabilidades claras
- [ ] Matriz RACI completa

**RSK-001:**
- [ ] Todos los riesgos identificados
- [ ] Controles mapeados a cada riesgo
- [ ] Riesgo residual calculado

### 5.3 Entregables de Fase 1

| Entregable | Descripcion |
|------------|-------------|
| 6 archivos PDF | Documentos listos para firma |
| Campos completados | Todos los campos pendientes llenos |
| Verificacion OK | Checklist de verificacion completa |

---

## 6. Fase 2: Aprobacion de Documentos

### 6.1 Objetivo

Obtener las firmas de aprobacion de todos los documentos base antes de ejecutar la validacion.

### 6.2 Orden de Firmas

**Importante:** Respetar el orden de firmas. Algunos documentos dependen de otros.

```
Orden de Firma:

1. ROL-001 (primero - define quien puede firmar que)
   └── Firma: Direccion
   └── Firma: Cada persona designada acepta su rol

2. RSK-001 (segundo - justifica nivel de validacion)
   └── Firma: System Owner
   └── Firma: QA

3. ERU-001 (tercero - define que se va a validar)
   └── Firma: Process Owner
   └── Firma: System Owner

4. SOP-001 (cuarto - procedimiento operativo)
   └── Firma: System Owner
   └── Firma: QA

5. IQOQ-001 Pre-ejecucion (ultimo - protocolo de pruebas)
   └── Firma: System Owner
   └── Firma: QA
```

### 6.3 Proceso de Firma por Documento

#### 6.3.1 ROL-001 - Designacion de Roles

**Firmantes:**
1. Direccion (aprueba las designaciones)
2. System Owner (acepta el rol)
3. Process Owner (acepta el rol)
4. Responsable IT (acepta el rol)
5. Responsable QA (acepta el rol)
6. Desarrollador (acepta el rol)

**Ubicacion de firmas en documento:**
- Seccion 2: Firmas de Aprobacion (Direccion)
- Seccion 3.1-3.5: Firma de aceptacion de cada rol

**Verificacion:**
- [ ] Todas las personas designadas estan informadas
- [ ] Cada persona acepto formalmente su rol
- [ ] Direccion aprobo las designaciones

#### 6.3.2 RSK-001 - Evaluacion de Riesgos

**Firmantes:**
1. Elaborador (quien preparo el documento)
2. QA (revisa y verifica)
3. System Owner (aprueba)

**Ubicacion de firmas en documento:**
- Inicio: Firmas de Aprobacion
- Final: Aprobacion Final

**Verificacion:**
- [ ] Todos los riesgos fueron evaluados
- [ ] Controles son adecuados
- [ ] Riesgo residual es aceptable

#### 6.3.3 ERU-001 - Especificaciones de Requerimientos

**Firmantes:**
1. Elaborador
2. Process Owner (valida requerimientos funcionales)
3. System Owner (aprueba)

**Ubicacion de firmas en documento:**
- Inicio: Firmas de Aprobacion
- Final: Aprobacion Final

**Verificacion:**
- [ ] Requerimientos reflejan necesidades del proceso
- [ ] Trazabilidad a Anexo 6 correcta
- [ ] Requerimientos son verificables

#### 6.3.4 SOP-001 - Procedimiento de Backup

**Firmantes:**
1. Elaborador
2. QA (revisa procedimiento)
3. System Owner (aprueba)

**Ubicacion de firmas en documento:**
- Inicio: Firmas de Aprobacion
- Final: Aprobacion Final

**Verificacion:**
- [ ] Procedimiento es ejecutable
- [ ] Frecuencias son adecuadas
- [ ] Contactos de emergencia correctos

#### 6.3.5 IQOQ-001 - Protocolo (Pre-Ejecucion)

**Firmantes:**
1. Elaborador
2. QA (revisa protocolo)
3. System Owner (aprueba ejecucion)

**Ubicacion de firmas en documento:**
- Seccion "Aprobacion del Protocolo (Pre-Ejecucion)"

**Verificacion:**
- [ ] Test cases cubren todos los requerimientos criticos
- [ ] Procedimientos son claros
- [ ] Criterios de aceptacion definidos

### 6.4 Entregables de Fase 2

| Entregable | Descripcion |
|------------|-------------|
| ROL-001 firmado | Designacion de roles aprobada |
| RSK-001 firmado | Evaluacion de riesgos aprobada |
| ERU-001 firmado | Requerimientos aprobados |
| SOP-001 firmado | Procedimiento aprobado |
| IQOQ-001 pre-ejecucion firmado | Protocolo aprobado para ejecucion |

---

## 7. Fase 3: Ejecucion de Validacion

### 7.1 Objetivo

Ejecutar todas las pruebas de IQ y OQ, capturar evidencia, documentar resultados.

### 7.2 Prerrequisitos

Antes de iniciar la ejecucion, verificar:

- [ ] IQOQ-001 aprobado (firmas pre-ejecucion)
- [ ] Sistema desplegado en ambiente de pruebas/produccion
- [ ] Credenciales de prueba disponibles para todos los roles
- [ ] Herramienta de captura de pantalla disponible
- [ ] Backup reciente disponible para prueba de restore
- [ ] Tiempo reservado sin interrupciones

### 7.3 Ejecucion de IQ (Installation Qualification)

#### 7.3.1 IQ-001: Verificacion de Versiones de Software

**Objetivo:** Verificar que las versiones instaladas coinciden con las especificadas.

**Procedimiento:**

1. Acceder al servidor/contenedor de la aplicacion
2. Ejecutar comandos de verificacion:

```bash
# Version de Java
java -version

# Version de PostgreSQL
docker exec postgres-db psql -V

# Version de Spring Boot (en logs)
docker logs spring-app 2>&1 | grep "Spring Boot"
```

3. Capturar screenshot de cada comando
4. Registrar resultados en formulario IQ-001

**Resultado esperado:**
- Java: 17.x
- PostgreSQL: 17.x
- Spring Boot: 3.4.1

**Nomenclatura screenshot:** `IQ-001-versiones-java.png`, `IQ-001-versiones-postgres.png`

#### 7.3.2 IQ-002: Verificacion de Base de Datos

**Objetivo:** Verificar que todas las tablas requeridas existen.

**Procedimiento:**

1. Conectar a la base de datos
2. Listar tablas:

```bash
docker exec postgres-db psql -U postgres -d conitrack -c "\dt"
```

3. Verificar existencia de tablas criticas:
   - lote
   - movimiento
   - analisis
   - bulto
   - traza
   - users
   - roles
   - producto
   - proveedor
   - auditoria_accesos (accesos de AUDITOR)
   - revinfo + tablas *_AUD (Hibernate Envers - audit trail de cambios)
   - configuracion

4. Capturar screenshot
5. Registrar resultados

**Nomenclatura screenshot:** `IQ-002-tablas.png`

#### 7.3.3 IQ-003: Verificacion de Configuracion

**Objetivo:** Verificar configuracion de produccion.

**Procedimiento:**

1. Verificar perfil activo:

```bash
docker logs spring-app 2>&1 | grep "active profile"
```

2. Verificar configuraciones criticas:
   - Perfil: PROD
   - show-sql: false
   - ddl-auto: validate

3. Capturar screenshot
4. Registrar resultados

**Nomenclatura screenshot:** `IQ-003-configuracion.png`

#### 7.3.4 IQ-004: Verificacion de Conectividad

**Objetivo:** Verificar que el sistema responde.

**Procedimiento:**

1. Abrir navegador
2. Acceder a URL del sistema
3. Verificar:
   - Pagina de login carga
   - HTTPS activo (en produccion)
   - Tiempo de carga < 3 segundos

4. Capturar screenshot de pagina de login
5. Registrar resultados

**Nomenclatura screenshot:** `IQ-004-login.png`

#### 7.3.5 IQ-005: Verificacion de Usuarios y Roles

**Objetivo:** Verificar que existen usuarios con los roles definidos.

**Procedimiento:**

1. Acceder como ADMIN
2. Navegar a Administracion > Usuarios
3. Verificar existencia de usuarios por rol:
   - ADMIN
   - DT
   - GERENTE_GARANTIA_CALIDAD
   - GERENTE_CONTROL_CALIDAD
   - ANALISTA_CONTROL_CALIDAD
   - SUPERVISOR_PLANTA
   - ANALISTA_PLANTA
   - AUDITOR

4. Capturar screenshot de listado
5. Registrar resultados

**Nomenclatura screenshot:** `IQ-005-usuarios.png`

### 7.4 Ejecucion de OQ (Operational Qualification)

#### 7.4.1 Categoria: Control de Acceso (TC-001 a TC-005)

**TC-001: Login Exitoso**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Acceder a pagina de login | Screenshot |
| 2 | Ingresar usuario valido | - |
| 3 | Ingresar password correcta | - |
| 4 | Click en Iniciar Sesion | - |
| 5 | Verificar redireccion a pagina principal | Screenshot |
| 6 | Verificar nombre en header | Screenshot |

**Nomenclatura:** `TC-001-login-formulario.png`, `TC-001-login-exitoso.png`

**TC-002: Login Fallido**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Acceder a login | - |
| 2 | Ingresar usuario valido | - |
| 3 | Ingresar password INCORRECTA | - |
| 4 | Click en Iniciar Sesion | - |
| 5 | Verificar mensaje de error | Screenshot |

**Nomenclatura:** `TC-002-login-fallido.png`

**TC-003: Bloqueo por Intentos Fallidos**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1-5 | 5 intentos con password incorrecta | - |
| 6 | Intento 6 con password CORRECTA | Screenshot (cuenta bloqueada) |
| 7 | Esperar 15 minutos | - |
| 8 | Intento con password correcta | Screenshot (acceso OK) |

**Nomenclatura:** `TC-003-cuenta-bloqueada.png`, `TC-003-cuenta-desbloqueada.png`

**TC-004: Control de Acceso AUDITOR**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Login como AUDITOR | Screenshot |
| 2 | Verificar menu (solo consultas) | Screenshot |
| 3 | Intentar acceso a /compras/alta via URL | Screenshot (403) |

**Nomenclatura:** `TC-004-auditor-menu.png`, `TC-004-auditor-403.png`

**TC-005: Crear Usuario (ADMIN)**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Login como ADMIN | - |
| 2 | Navegar a Admin > Usuarios | - |
| 3 | Click en Nuevo Usuario | Screenshot |
| 4 | Completar datos | - |
| 5 | Guardar | Screenshot (exito) |
| 6 | Verificar en listado | Screenshot |

**Nomenclatura:** `TC-005-nuevo-usuario.png`, `TC-005-usuario-creado.png`

#### 7.4.2 Categoria: Gestion de Lotes (TC-011 a TC-013)

**TC-011: Alta de Lote por Compra**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Navegar a Compras > Alta Ingreso | Screenshot formulario |
| 2 | Completar datos del lote | - |
| 3 | Ingresar motivo (>20 chars) | - |
| 4 | Guardar | Screenshot exito |
| 5 | Verificar lote en listado | Screenshot |

**Datos de prueba:**
- Numero lote: TEST-IQOQ-001
- Cantidad: 100
- Unidad: KILOGRAMO
- Motivo: "Ingreso de mercaderia para prueba de validacion IQ/OQ del sistema"

**Nomenclatura:** `TC-011-formulario.png`, `TC-011-lote-creado.png`

**TC-012: Alta de Lote por Produccion**

Similar a TC-011 pero en Produccion > Alta Lote.

**TC-013: Consulta de Lotes**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Navegar a Consultas > Lotes | Screenshot listado |
| 2 | Filtrar por producto | Screenshot filtrado |
| 3 | Click en un lote | Screenshot detalle |

**Nomenclatura:** `TC-013-listado.png`, `TC-013-filtro.png`, `TC-013-detalle.png`

#### 7.4.3 Categoria: Audit Trail (TC-031 a TC-033)

**TC-031: Registro de Cambio en Audit Trail**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Realizar un cambio (ej: resultado analisis) | - |
| 2 | Ingresar motivo (>20 chars) | - |
| 3 | Guardar | - |
| 4 | Acceder a historial del lote | Screenshot |
| 5 | Verificar registro con: usuario, fecha, campo, antes, despues, motivo | Screenshot detalle |

**Nomenclatura:** `TC-031-historial.png`, `TC-031-detalle-cambio.png`

**TC-032: Motivo Obligatorio**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Iniciar operacion de modificacion | - |
| 2 | Dejar motivo vacio | - |
| 3 | Intentar guardar | Screenshot error |
| 4 | Ingresar motivo corto (5 chars) | - |
| 5 | Intentar guardar | Screenshot error |
| 6 | Ingresar motivo valido (>20 chars) | - |
| 7 | Guardar | Screenshot exito |

**Nomenclatura:** `TC-032-error-vacio.png`, `TC-032-error-corto.png`, `TC-032-exito.png`

**TC-033: Audit Trail No Modificable**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Acceder a historial de cambios | Screenshot |
| 2 | Verificar ausencia de botones editar/eliminar | Screenshot |

**Nomenclatura:** `TC-033-solo-lectura.png`

#### 7.4.4 Categoria: Backup y Recuperacion (TC-051 a TC-052)

**TC-051: Existencia de Backup Configurado**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Verificar configuracion de backup (cron/scheduler) | Screenshot |
| 2 | Listar archivos de backup | Screenshot |
| 3 | Verificar fecha ultimo backup | Screenshot |
| 4 | Verificar tamano (> 1 MB) | Screenshot |

**Nomenclatura:** `TC-051-config.png`, `TC-051-archivos.png`

**TC-052: Prueba de Restore**

| Paso | Accion | Capturar |
|------|--------|----------|
| 1 | Seleccionar backup de prueba | Screenshot |
| 2 | Crear BD de prueba | Screenshot comando |
| 3 | Ejecutar restore | Screenshot comando |
| 4 | Contar registros tabla lote | Screenshot |
| 5 | Contar registros tabla movimiento | Screenshot |
| 6 | Eliminar BD de prueba | Screenshot |

**Comandos:**
```bash
# Crear BD prueba
docker exec postgres-db psql -U postgres -c "CREATE DATABASE conitrack_test;"

# Restore
gunzip -c backups/conitrack_FECHA.sql.gz | docker exec -i postgres-db psql -U postgres -d conitrack_test

# Verificar
docker exec postgres-db psql -U postgres -d conitrack_test -c "SELECT COUNT(*) FROM lote;"
docker exec postgres-db psql -U postgres -d conitrack_test -c "SELECT COUNT(*) FROM movimiento;"

# Limpiar
docker exec postgres-db psql -U postgres -c "DROP DATABASE conitrack_test;"
```

**Nomenclatura:** `TC-052-backup.png`, `TC-052-restore.png`, `TC-052-verificacion.png`

### 7.5 Manejo de Desviaciones

Si algun test case FALLA:

1. **Documentar la desviacion:**
   - ID de desviacion (DEV-001, DEV-002, etc.)
   - Test case afectado
   - Descripcion del problema
   - Resultado esperado vs obtenido
   - Criticidad (Critica/Mayor/Menor)

2. **Evaluar impacto:**
   - Critica: Bloquea aprobacion, requiere correccion inmediata
   - Mayor: Requiere plan de remediacion con fecha
   - Menor: Puede documentarse y cerrarse sin correccion

3. **Registrar en IQOQ-001:**
   - Seccion 8: Registro de Desviaciones

4. **Acciones correctivas:**
   - Corregir el problema
   - Re-ejecutar el test
   - Documentar la correccion

### 7.6 Entregables de Fase 3

| Entregable | Descripcion |
|------------|-------------|
| Screenshots IQ | ~5 imagenes (IQ-001 a IQ-005) |
| Screenshots OQ | ~30-40 imagenes (TC-001 a TC-052) |
| Resultados en formularios | IQOQ-001 con resultados |
| Registro de desviaciones | Si aplica |
| Registro de backup | Formulario SOP-001 |
| Registro de restore | Formulario SOP-001 |

---

## 8. Fase 4: Cierre de Validacion

### 8.1 Objetivo

Compilar toda la evidencia, firmar resultados, archivar documentacion.

### 8.2 Tareas de Cierre

#### 8.2.1 Compilar Evidencia

**Organizar screenshots:**

```
ANEXO-SCREENSHOTS/
├── IQ/
│   ├── IQ-001-versiones-java.png
│   ├── IQ-001-versiones-postgres.png
│   ├── IQ-002-tablas.png
│   ├── IQ-003-configuracion.png
│   ├── IQ-004-login.png
│   └── IQ-005-usuarios.png
│
└── OQ/
    ├── Control-Acceso/
    │   ├── TC-001-login-formulario.png
    │   ├── TC-001-login-exitoso.png
    │   ├── TC-002-login-fallido.png
    │   └── ...
    ├── Gestion-Lotes/
    │   └── ...
    ├── Audit-Trail/
    │   └── ...
    └── Backup-Restore/
        └── ...
```

**Crear PDF de evidencia:**

Opcion A: Un PDF con todos los screenshots numerados
Opcion B: Carpeta de imagenes referenciadas en IQOQ-001

#### 8.2.2 Completar Resultados en IQOQ-001

1. Marcar PASS/FAIL en cada test case
2. Completar Seccion 9: Resumen de Resultados
3. Completar Seccion 10: Conclusion
4. Marcar checkboxes de resultado final

#### 8.2.3 Cerrar Desviaciones

Para cada desviacion:

1. Verificar que se corrigio (si aplica)
2. Documentar accion correctiva
3. Marcar como CERRADA
4. Firmar cierre

#### 8.2.4 Firmar IQOQ-001 Post-Ejecucion

**Firmantes:**
1. Ejecutor (quien ejecuto las pruebas)
2. QA (verifica ejecucion correcta)
3. System Owner (aprueba resultado final)

**Ubicacion de firmas:**
- Seccion "Aprobacion del Reporte (Post-Ejecucion)"
- Final del documento: "Aprobaciones finales"

### 8.3 Armado de Carpeta Final

#### 8.3.1 Estructura Recomendada

```
CONITRACK-VALIDACION-ANMAT-2025/
│
├── 00-INDICE.pdf
│   (Lista de todos los documentos con version y fecha)
│
├── 01-DOCUMENTOS-APROBADOS/
│   ├── ROL-001-DESIGNACION-ROLES-v1.0-FIRMADO.pdf
│   ├── RSK-001-EVALUACION-RIESGOS-v1.0-FIRMADO.pdf
│   ├── ERU-001-ESPECIFICACIONES-v1.0-FIRMADO.pdf
│   ├── SOP-001-BACKUP-v1.0-FIRMADO.pdf
│   └── IQOQ-001-PROTOCOLO-v1.0-FIRMADO.pdf (pre-ejecucion)
│
├── 02-RESULTADOS-VALIDACION/
│   ├── IQOQ-001-RESULTADOS-v1.0-FIRMADO.pdf
│   ├── ANEXO-SCREENSHOTS/
│   │   ├── IQ/
│   │   │   └── (screenshots IQ)
│   │   └── OQ/
│   │       └── (screenshots OQ)
│   └── REGISTROS/
│       ├── Registro-Backup-2025-12.pdf
│       └── Registro-Restore-Prueba-2025-12.pdf
│
├── 03-DESVIACIONES/
│   └── (si hubo desviaciones, documentos de cierre)
│
├── 04-MANUAL-USUARIO/
│   └── MAN-001-MANUAL-USUARIO-v1.0.pdf
│
└── 05-REFERENCIA/
    ├── ANMAT-Anexo6.pdf
    └── GUIA-001-PROXIMOS-PASOS.pdf
```

#### 8.3.2 Crear Indice

El archivo `00-INDICE.pdf` debe listar:

| # | Documento | Codigo | Version | Fecha | Paginas |
|---|-----------|--------|---------|-------|---------|
| 1 | Designacion de Roles | ROL-001 | 1.0 | 2025-12-XX | XX |
| 2 | Evaluacion de Riesgos | RSK-001 | 1.0 | 2025-12-XX | XX |
| 3 | Especificaciones de Requerimientos | ERU-001 | 1.0 | 2025-12-XX | XX |
| 4 | Procedimiento Backup/Restore | SOP-001 | 1.0 | 2025-12-XX | XX |
| 5 | Protocolo IQ/OQ | IQOQ-001 | 1.0 | 2025-12-XX | XX |
| 6 | Resultados IQ/OQ | IQOQ-001-R | 1.0 | 2025-12-XX | XX |
| 7 | Manual de Usuario | MAN-001 | 1.0 | 2025-12-XX | XX |

### 8.4 Entregables de Fase 4

| Entregable | Descripcion |
|------------|-------------|
| IQOQ-001 post-ejecucion firmado | Resultados aprobados |
| Carpeta de evidencia | Screenshots organizados |
| Registros firmados | Backup, restore |
| Carpeta final completa | Lista para auditoria |
| Indice de documentos | Lista maestra |

---

## 9. Estructura de Carpetas para Auditoria

### 9.1 Estructura Fisica (si imprimen)

```
CARPETA FISICA: "VALIDACION CONITRACK - ANMAT"

├── Separador 1: DOCUMENTOS BASE
│   ├── ROL-001 (original firmado)
│   ├── RSK-001 (original firmado)
│   ├── ERU-001 (original firmado)
│   └── SOP-001 (original firmado)
│
├── Separador 2: PROTOCOLO Y RESULTADOS
│   ├── IQOQ-001 Pre-ejecucion (original firmado)
│   └── IQOQ-001 Resultados (original firmado)
│
├── Separador 3: EVIDENCIA
│   ├── Screenshots impresos (o referencia a CD/USB)
│   └── Registros de backup/restore
│
├── Separador 4: DESVIACIONES
│   └── Documentos de desviacion (si aplica)
│
└── Separador 5: ANEXOS
    ├── Manual de Usuario
    └── Anexo 6 ANMAT
```

### 9.2 Estructura Digital (recomendada)

```
CONITRACK-VALIDACION-ANMAT-2025/
│
├── README.txt
│   "Contenido de esta carpeta, instrucciones de navegacion"
│
├── 01-DOCUMENTOS-BASE/
│   ├── ROL-001-DESIGNACION-ROLES-v1.0-FIRMADO.pdf
│   ├── RSK-001-EVALUACION-RIESGOS-v1.0-FIRMADO.pdf
│   ├── ERU-001-ESPECIFICACIONES-v1.0-FIRMADO.pdf
│   └── SOP-001-BACKUP-v1.0-FIRMADO.pdf
│
├── 02-VALIDACION/
│   ├── IQOQ-001-PROTOCOLO-v1.0-FIRMADO.pdf
│   ├── IQOQ-001-RESULTADOS-v1.0-FIRMADO.pdf
│   └── EVIDENCIA/
│       ├── IQ/
│       │   ├── IQ-001-versiones.png
│       │   ├── IQ-002-tablas.png
│       │   └── ...
│       └── OQ/
│           ├── TC-001-login.png
│           └── ...
│
├── 03-REGISTROS/
│   ├── Registro-Backup-2025-12.pdf
│   └── Registro-Restore-2025-12.pdf
│
├── 04-DESVIACIONES/
│   └── (vacio o con desviaciones)
│
├── 05-MANUALES/
│   └── MAN-001-MANUAL-USUARIO-v1.0.pdf
│
└── 06-REFERENCIA/
    ├── ANMAT-Anexo6.txt
    ├── GUIA-001-PROXIMOS-PASOS.pdf
    └── INDICE-DOCUMENTOS.xlsx
```

### 9.3 Respaldo de Documentacion

**Importante:** Mantener al menos 2 copias de la documentacion:

1. **Copia principal:** Carpeta fisica o servidor de documentos
2. **Copia de respaldo:** USB, disco externo, o nube

**Retencion:** Minimo 5 años (o segun politica de la empresa)

---

## 10. Checklist Completa

### 10.1 Checklist Pre-Auditoria

```
FASE 1: PREPARACION (1-2 dias)
═══════════════════════════════

□ Documentos convertidos a PDF
  □ ERU-001.pdf
  □ IQOQ-001.pdf
  □ SOP-001.pdf
  □ MAN-001.pdf
  □ ROL-001.pdf
  □ RSK-001.pdf

□ Campos completados
  □ ROL-001: Nombres de System Owner, Process Owner, IT, QA, Dev
  □ ROL-001: Delegados definidos
  □ SOP-001: Contactos de emergencia
  □ MAN-001: URL del sistema
  □ MAN-001: Contactos de soporte

□ Contenido verificado
  □ ERU-001: IDs unicos, trazabilidad
  □ IQOQ-001: Test cases completos
  □ SOP-001: Comandos correctos
  □ ROL-001: Responsabilidades claras
  □ RSK-001: Riesgos y controles


FASE 2: APROBACION (2-3 dias)
═══════════════════════════════

□ ROL-001 firmado
  □ Firma Direccion
  □ Firma System Owner (acepta rol)
  □ Firma Process Owner (acepta rol)
  □ Firma IT (acepta rol)
  □ Firma QA (acepta rol)
  □ Firma Desarrollador (acepta rol)

□ RSK-001 firmado
  □ Firma Elaborador
  □ Firma QA
  □ Firma System Owner

□ ERU-001 firmado
  □ Firma Elaborador
  □ Firma Process Owner
  □ Firma System Owner

□ SOP-001 firmado
  □ Firma Elaborador
  □ Firma QA
  □ Firma System Owner

□ IQOQ-001 Pre-ejecucion firmado
  □ Firma Elaborador
  □ Firma QA
  □ Firma System Owner


FASE 3: EJECUCION (1-2 dias)
═══════════════════════════════

□ Prerrequisitos verificados
  □ Sistema desplegado
  □ Credenciales disponibles
  □ Backup disponible para test restore
  □ Herramienta screenshots lista

□ IQ ejecutado
  □ IQ-001: Versiones verificadas
  □ IQ-002: Tablas verificadas
  □ IQ-003: Configuracion verificada
  □ IQ-004: Conectividad verificada
  □ IQ-005: Usuarios verificados

□ OQ ejecutado
  □ TC-001 a TC-005: Control de Acceso
  □ TC-011 a TC-013: Gestion de Lotes
  □ TC-021 a TC-025: Movimientos
  □ TC-031 a TC-033: Audit Trail
  □ TC-041 a TC-042: Seguridad
  □ TC-051 a TC-052: Backup/Restore

□ Evidencia capturada
  □ Screenshots IQ (~5)
  □ Screenshots OQ (~30-40)
  □ Registro de backup
  □ Registro de restore

□ Desviaciones documentadas
  □ (si aplica)


FASE 4: CIERRE (1 dia)
═══════════════════════════════

□ Resultados completados en IQOQ-001
  □ PASS/FAIL en cada test
  □ Resumen de resultados
  □ Conclusion

□ Desviaciones cerradas
  □ (si aplica)

□ IQOQ-001 Post-ejecucion firmado
  □ Firma Ejecutor
  □ Firma QA
  □ Firma System Owner

□ Carpeta final armada
  □ Estructura de carpetas creada
  □ Documentos organizados
  □ Screenshots organizados
  □ Indice creado

□ Respaldo creado
  □ Copia principal archivada
  □ Copia de respaldo creada
```

### 10.2 Checklist de Auditoria (Dia de la Auditoria)

```
PREPARACION PARA AUDITORIA
═══════════════════════════════

□ Documentacion disponible
  □ Carpeta fisica accesible
  □ Carpeta digital accesible
  □ Sistema funcionando

□ Personal disponible
  □ System Owner
  □ Process Owner
  □ QA
  □ IT (para demostraciones)

□ Sistema listo para demostracion
  □ Credenciales de prueba
  □ Datos de ejemplo
  □ Backup reciente

□ Ambiente preparado
  □ Sala de reunion
  □ Proyector/pantalla
  □ Acceso a red/internet
```

---

## 11. Preguntas Frecuentes

### 11.1 Sobre Documentacion

**P: Es necesario imprimir todos los documentos?**
R: No obligatoriamente. La documentacion digital (PDF firmado) es aceptada por la mayoria de los auditores. Consultar previamente con el auditor si tiene preferencia.

**P: Que pasa si encuentro un error en un documento ya firmado?**
R: Emitir una nueva version del documento (v1.1, v2.0), documentar el cambio en el control de versiones, y volver a firmar. Conservar la version anterior.

**P: Cuanto tiempo debo conservar la documentacion?**
R: Minimo 5 años, o segun politica de retencion de la empresa. Para productos farmaceuticos, puede ser mas (vida util + 1 año).

### 11.2 Sobre Firmas

**P: Puede firmar una sola persona todos los documentos?**
R: No es recomendable. Los roles de Elaborador, Revisor y Aprobador deben ser personas diferentes para garantizar independencia.

**P: Es valida la firma electronica simple (imagen)?**
R: Depende del auditor. Es mejor usar firma digital certificada o PDF firmable con registro. La firma manuscrita escaneada siempre es aceptada.

**P: Que hago si el System Owner no esta disponible?**
R: Usar el delegado definido en ROL-001, documentando la delegacion.

### 11.3 Sobre Ejecucion IQ/OQ

**P: Puedo ejecutar IQ/OQ en ambiente de desarrollo?**
R: Lo ideal es ejecutar en el ambiente de produccion o un ambiente identico (staging). Si se hace en desarrollo, documentar las diferencias.

**P: Que hago si un test case falla?**
R: Documentar como desviacion, evaluar criticidad, corregir si es posible, re-ejecutar, documentar la correccion.

**P: Es necesario capturar screenshot de TODO?**
R: No de todo, pero si de los puntos criticos: resultados de verificacion, mensajes de exito/error, evidencia de funcionamiento correcto.

### 11.4 Sobre la Auditoria

**P: Que pregunta tipicamente un auditor ANMAT?**
R:
- Muestreme el audit trail de este lote
- Como se hace un backup?
- Quien puede modificar datos?
- Que pasa si ingreso mal un dato?
- Como revierten un error?

**P: Que pasa si el auditor encuentra algo que no esta documentado?**
R: Sera una observacion o hallazgo. Documentar la respuesta, comprometerse a corregir con fecha, y hacer seguimiento.

**P: Es necesario que este el desarrollador en la auditoria?**
R: No es obligatorio, pero es util tener a alguien tecnico disponible para responder preguntas sobre el sistema.

---

## 12. Anexos

### 12.1 Plantilla de Control de Firmas

```
CONTROL DE FIRMAS - VALIDACION CONITRACK
=========================================

Documento: _______________________________
Version: _______
Fecha de emision: ____/____/____

FIRMAS REQUERIDAS:

| Rol | Nombre | Firma | Fecha | Estado |
|-----|--------|-------|-------|--------|
| Elaborado por | | | | [ ] Pendiente [ ] Firmado |
| Revisado por | | | | [ ] Pendiente [ ] Firmado |
| Aprobado por | | | | [ ] Pendiente [ ] Firmado |

Observaciones:
_______________________________________________
_______________________________________________
```

### 12.2 Plantilla de Registro de Desviacion

```
REGISTRO DE DESVIACION
======================

ID Desviacion: DEV-____
Fecha: ____/____/____
Test Case afectado: ____________

DESCRIPCION DEL PROBLEMA:
_______________________________________________
_______________________________________________

RESULTADO ESPERADO:
_______________________________________________

RESULTADO OBTENIDO:
_______________________________________________

CRITICIDAD: [ ] Critica [ ] Mayor [ ] Menor

IMPACTO EN VALIDACION:
[ ] Bloquea aprobacion
[ ] Requiere plan de remediacion
[ ] Puede cerrarse sin correccion

ACCION CORRECTIVA:
_______________________________________________
_______________________________________________

RESPONSABLE: _______________________________
FECHA COMPROMISO: ____/____/____

CIERRE:
Fecha cierre: ____/____/____
Verificado por: _______________________________
Resultado: [ ] Corregido [ ] Aceptado sin correccion

Firma cierre: _______________________________
```

### 12.3 Contactos Utiles

| Rol | Nombre | Email | Telefono |
|-----|--------|-------|----------|
| System Owner | _____________ | _____________ | _____________ |
| Process Owner | _____________ | _____________ | _____________ |
| QA | _____________ | _____________ | _____________ |
| IT | _____________ | _____________ | _____________ |
| Desarrollador | _____________ | _____________ | _____________ |

### 12.4 Referencias

| Documento | Descripcion |
|-----------|-------------|
| ANMAT Disposicion 4159/2023 Anexo 6 | Requisitos para sistemas informatizados |
| ISPE GAMP 5 | Guia de validacion de sistemas |
| ERU-001 | Especificaciones de requerimientos del sistema |
| IQOQ-001 | Protocolo de calificacion |
| SOP-001 | Procedimiento de backup y recuperacion |
| ROL-001 | Designacion de roles |
| RSK-001 | Evaluacion de riesgos |
| MAN-001 | Manual de usuario |

---

**Fin del documento**

---

**Control de Versiones:**

| Version | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0 | 2025-12-16 | Equipo Proyecto | Version inicial |
