# ROL-001: Designacion de Roles del Sistema
## Sistema Conitrack - Gestion de Stock Farmaceutico

---

**Codigo documento:** ROL-001
**Version:** 1.0
**Fecha:** 2025-12-16
**Estado:** Aprobado

---

## Control de Versiones

| Version | Fecha | Autor | Descripcion |
|---------|-------|-------|-------------|
| 1.0 | 2025-12-16 | Equipo Proyecto | Version inicial |

---

## Firmas de Aprobacion

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| Elaborado por | _________________ | _________________ | ____/____/____ |
| Aprobado por (Direccion) | _________________ | _________________ | ____/____/____ |

---

## 1. Objetivo

Este documento establece la designacion formal de roles y responsabilidades para el sistema informatizado Conitrack, conforme a los requisitos de ANMAT Disposicion 4159/2023 Anexo 6, Requisito 2.1:

> "Debe existir una cooperacion estrecha entre todo el personal relevante entre los que se encuentra el propietario del proceso (process owner), el propietario del sistema (system owner), las Personas Cualificadas e informatica (IT). Todo el personal debe disponer de la cualificacion apropiada, el nivel de acceso y tener definidas sus responsabilidades para llevar a cabo las tareas asignadas."

---

## 2. Alcance

Este documento aplica al sistema Conitrack y cubre:
- Designacion de System Owner
- Designacion de Process Owner
- Designacion de responsables IT
- Designacion de responsables QA
- Cualificacion requerida para cada rol

---

## 3. Roles Designados

### 3.1 System Owner (Propietario del Sistema)

| Campo | Valor |
|-------|-------|
| **Nombre** | _________________________________________________ |
| **Cargo** | _________________________________________________ |
| **Area** | _________________________________________________ |
| **Fecha de designacion** | ____/____/____ |

**Responsabilidades:**
- Responsable de la disponibilidad, operacion y mantenimiento del sistema
- Aprobar cambios al sistema (configuracion, actualizaciones)
- Aprobar procedimientos relacionados (backup, restore, gestion de usuarios)
- Autorizar acceso de nuevos usuarios
- Garantizar que el sistema cumple con los requisitos regulatorios
- Aprobar el reporte de validacion y liberacion del sistema
- Autorizar restauracion de datos en produccion

**Cualificacion requerida:**
- Conocimiento del proceso de negocio soportado por el sistema
- Formacion en sistemas informatizados GxP
- Capacidad de toma de decisiones sobre el sistema

**Firma de aceptacion del rol:**

| Nombre | Firma | Fecha |
|--------|-------|-------|
| _________________________________ | _________________________________ | ____/____/____ |

---

### 3.2 Process Owner (Propietario del Proceso)

| Campo | Valor |
|-------|-------|
| **Nombre** | _________________________________________________ |
| **Cargo** | _________________________________________________ |
| **Area** | _________________________________________________ |
| **Fecha de designacion** | ____/____/____ |

**Responsabilidades:**
- Responsable del proceso de negocio que el sistema soporta (gestion de stock)
- Definir requerimientos funcionales del sistema
- Validar que el sistema cumple con las necesidades del proceso
- Aprobar especificaciones de requerimientos de usuario (ERU)
- Confirmar funcionamiento correcto del sistema en operacion (PQ)
- Reportar incidencias funcionales al System Owner

**Cualificacion requerida:**
- Conocimiento experto del proceso de gestion de stock farmaceutico
- Experiencia en operacion del sistema
- Formacion en buenas practicas GxP aplicables

**Firma de aceptacion del rol:**

| Nombre | Firma | Fecha |
|--------|-------|-------|
| _________________________________ | _________________________________ | ____/____/____ |

---

### 3.3 Responsable IT / Administrador del Sistema

| Campo | Valor |
|-------|-------|
| **Nombre** | _________________________________________________ |
| **Cargo** | _________________________________________________ |
| **Area** | _________________________________________________ |
| **Fecha de designacion** | ____/____/____ |

**Responsabilidades:**
- Administracion tecnica del sistema (servidores, base de datos, red)
- Ejecucion de backups y restores
- Mantenimiento de infraestructura
- Gestion de accesos tecnicos
- Soporte tecnico a usuarios
- Implementacion de cambios aprobados
- Monitoreo de seguridad y disponibilidad

**Cualificacion requerida:**
- Formacion tecnica en administracion de sistemas
- Conocimiento de PostgreSQL, Docker, Spring Boot
- Conocimiento de procedimientos de backup/restore
- Formacion basica en sistemas GxP

**Firma de aceptacion del rol:**

| Nombre | Firma | Fecha |
|--------|-------|-------|
| _________________________________ | _________________________________ | ____/____/____ |

---

### 3.4 Responsable QA (Aseguramiento de Calidad)

| Campo | Valor |
|-------|-------|
| **Nombre** | _________________________________________________ |
| **Cargo** | _________________________________________________ |
| **Area** | _________________________________________________ |
| **Fecha de designacion** | ____/____/____ |

**Responsabilidades:**
- Verificar cumplimiento de procedimientos documentados
- Revisar y aprobar documentacion de validacion
- Participar en pruebas de calificacion (IQ/OQ)
- Verificar ejecucion de pruebas de restore
- Auditar registros de backup y cambios
- Gestionar desviaciones relacionadas al sistema
- Verificar cumplimiento regulatorio (ANMAT Anexo 6)

**Cualificacion requerida:**
- Formacion en aseguramiento de calidad farmaceutica
- Conocimiento de ANMAT Disposicion 4159 Anexo 6
- Conocimiento de GAMP 5
- Experiencia en validacion de sistemas informatizados

**Firma de aceptacion del rol:**

| Nombre | Firma | Fecha |
|--------|-------|-------|
| _________________________________ | _________________________________ | ____/____/____ |

---

### 3.5 Desarrollador / Proveedor Interno

| Campo | Valor |
|-------|-------|
| **Nombre** | _________________________________________________ |
| **Cargo** | _________________________________________________ |
| **Area** | _________________________________________________ |
| **Fecha de designacion** | ____/____/____ |

**Responsabilidades:**
- Desarrollo y mantenimiento del codigo fuente
- Implementacion de cambios solicitados
- Correccion de defectos
- Documentacion tecnica del sistema
- Soporte tecnico de segundo nivel
- Control de versiones (Git)
- Ejecucion de pruebas unitarias

**Cualificacion requerida:**
- Formacion en desarrollo de software
- Experiencia en Java, Spring Boot, PostgreSQL
- Conocimiento de buenas practicas de desarrollo
- Formacion basica en sistemas GxP (deseable)

**Firma de aceptacion del rol:**

| Nombre | Firma | Fecha |
|--------|-------|-------|
| _________________________________ | _________________________________ | ____/____/____ |

---

## 4. Matriz de Responsabilidades (RACI)

| Actividad | System Owner | Process Owner | IT | QA | Desarrollador |
|-----------|--------------|---------------|----|----|---------------|
| Aprobar requerimientos | A | R | C | C | I |
| Aprobar cambios sistema | A | C | R | C | I |
| Ejecutar backup | I | I | R | C | I |
| Aprobar restore produccion | A | C | R | C | I |
| Aprobar usuarios | A | C | R | I | I |
| Validar funcionamiento | C | A | I | R | I |
| Gestionar incidencias | I | R | C | A | C |
| Aprobar documentacion | A | C | I | R | I |
| Desarrollar cambios | I | C | I | I | R |
| Auditar cumplimiento | I | I | I | R | I |

**Leyenda:**
- **R** = Responsable (ejecuta la tarea)
- **A** = Aprobador (autoriza la tarea)
- **C** = Consultado (proporciona informacion)
- **I** = Informado (recibe informacion)

---

## 5. Organigrama del Sistema

```
                    +-------------------+
                    |   DIRECCION       |
                    | (Aprueba roles)   |
                    +--------+----------+
                             |
              +--------------+--------------+
              |                             |
     +--------+--------+           +--------+--------+
     |  SYSTEM OWNER   |           |  PROCESS OWNER  |
     | (Propietario    |           | (Propietario    |
     |  del Sistema)   |           |  del Proceso)   |
     +--------+--------+           +--------+--------+
              |                             |
              |                             |
     +--------+--------+                    |
     |                 |                    |
+----+----+      +-----+-----+        +-----+-----+
|   IT    |      |    QA     |        | USUARIOS  |
| Admin   |      | Calidad   |        | Finales   |
+---------+      +-----------+        +-----------+
     |
+----+----+
| DESARR. |
| Interno |
+---------+
```

---

## 6. Comunicacion entre Roles

### 6.1 Canales de Comunicacion

| De | A | Canal | Frecuencia |
|----|---|-------|------------|
| Process Owner | System Owner | Email/Reunion | Segun necesidad |
| IT | System Owner | Email/Ticket | Inmediato (incidencias) |
| QA | System Owner | Email/Reunion | Mensual (auditorias) |
| Usuarios | IT | Ticket/Email | Segun necesidad |

### 6.2 Reuniones Periodicas

| Reunion | Participantes | Frecuencia | Objetivo |
|---------|---------------|------------|----------|
| Revision del Sistema | System Owner, Process Owner, QA | Trimestral | Evaluacion periodica |
| Seguimiento Operativo | System Owner, IT | Mensual | Estado del sistema |
| Revision de Incidencias | Todos los roles | Segun necesidad | Incidencias criticas |

---

## 7. Delegacion de Responsabilidades

En caso de ausencia temporal de un rol designado:

| Rol | Delegado | Limitaciones |
|-----|----------|--------------|
| System Owner | _________________________ | No puede aprobar cambios mayores |
| Process Owner | _________________________ | No puede aprobar ERU |
| IT | _________________________ | Sin limitaciones |
| QA | _________________________ | No puede aprobar validacion |

---

## 8. Vigencia y Revision

- **Vigencia:** Este documento es valido desde su fecha de aprobacion
- **Revision:** Anual o ante cambios de personal
- **Proxima revision:** ____/____/____

---

## 9. Historial de Designaciones

| Fecha | Rol | Persona Anterior | Persona Nueva | Motivo |
|-------|-----|------------------|---------------|--------|
| | | | | |
| | | | | |
| | | | | |

---

## 10. Anexos

### 10.1 Registros de Entrenamiento

Los registros de entrenamiento de cada persona designada deben mantenerse actualizados y disponibles para auditoria.

| Rol | Entrenamiento Requerido | Frecuencia |
|-----|------------------------|------------|
| System Owner | Sistemas GxP, ANMAT Anexo 6 | Inicial + cada 2 años |
| Process Owner | GMP, Sistema Conitrack | Inicial + anual |
| IT | Backup/Restore, Seguridad | Inicial + anual |
| QA | ANMAT Anexo 6, GAMP 5 | Inicial + cada 2 años |
| Desarrollador | Buenas practicas desarrollo | Inicial + continuo |

### 10.2 Referencias

| Documento | Codigo |
|-----------|--------|
| ANMAT Disposicion 4159/2023 Anexo 6 | Requisito 2.1 |
| ERU - Especificaciones de Requerimientos | ERU-001 |
| Protocolo IQ/OQ | IQOQ-001 |
| SOP Backup y Recuperacion | SOP-001 |

---

**Fin del documento**

---

**Aprobacion Final:**

| | Firma | Fecha |
|---|-------|-------|
| **Elaborado por:** | _________________ | ____/____/____ |
| **Aprobado por (Direccion):** | _________________ | ____/____/____ |
