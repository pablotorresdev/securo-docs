# RSK-001: Evaluacion de Riesgos del Sistema
## Sistema Conitrack - Gestion de Stock Farmaceutico

---

**Codigo documento:** RSK-001
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
| Revisado por (QA) | _________________ | _________________ | ____/____/____ |
| Aprobado por (System Owner) | _________________ | _________________ | ____/____/____ |

---

## Indice

1. [Objetivo](#1-objetivo)
2. [Alcance](#2-alcance)
3. [Metodologia](#3-metodologia)
4. [Clasificacion del Sistema](#4-clasificacion-del-sistema)
5. [Identificacion de Riesgos](#5-identificacion-de-riesgos)
6. [Matriz de Riesgos](#6-matriz-de-riesgos)
7. [Controles Implementados](#7-controles-implementados)
8. [Riesgo Residual](#8-riesgo-residual)
9. [Conclusion](#9-conclusion)
10. [Anexos](#10-anexos)

---

## 1. Objetivo

Este documento establece la evaluacion de riesgos del sistema informatizado Conitrack, conforme a los requisitos de ANMAT Disposicion 4159/2023 Anexo 6, Requisito 1.1:

> "La gestion de riesgos debe aplicarse durante el ciclo de vida del sistema informatizado teniendo en cuenta la seguridad del paciente, la integridad de datos y la calidad del producto. Como parte del sistema de gestion de riesgos, las decisiones sobre la extension de la validacion y de los controles de la integridad de datos deben basarse en una evaluacion de riesgos del sistema informatizado justificada y documentada."

---

## 2. Alcance

**Sistema evaluado:** Conitrack v1.0
**Fecha de evaluacion:** 2025-12-16
**Proxima revision:** Anual o ante cambios significativos

**Incluye:**
- Riesgos de integridad de datos
- Riesgos de disponibilidad del sistema
- Riesgos de seguridad
- Riesgos de cumplimiento regulatorio
- Riesgos operacionales

**Excluye:**
- Riesgos de infraestructura fisica (data center, red)
- Riesgos de sistemas externos no integrados

---

## 3. Metodologia

### 3.1 Criterios de Evaluacion

**Probabilidad (P):**

| Nivel | Valor | Descripcion |
|-------|-------|-------------|
| Rara | 1 | Evento improbable (<1 vez al año) |
| Ocasional | 2 | Evento posible (1-3 veces al año) |
| Frecuente | 3 | Evento probable (>3 veces al año) |

**Impacto (I):**

| Nivel | Valor | Descripcion |
|-------|-------|-------------|
| Bajo | 1 | Sin impacto en calidad, seguridad o cumplimiento |
| Medio | 2 | Impacto menor, recuperable sin perdida significativa |
| Alto | 3 | Impacto mayor en integridad de datos o cumplimiento |

**Riesgo = Probabilidad x Impacto**

| Nivel de Riesgo | Rango | Accion Requerida |
|-----------------|-------|------------------|
| Bajo | 1-2 | Monitoreo, controles basicos |
| Medio | 3-4 | Controles especificos, seguimiento |
| Alto | 6-9 | Controles criticos, mitigacion inmediata |

### 3.2 Equipo Evaluador

| Rol | Nombre | Area |
|-----|--------|------|
| System Owner | _________________ | _________________ |
| Process Owner | _________________ | _________________ |
| QA | _________________ | _________________ |
| IT | _________________ | _________________ |

---

## 4. Clasificacion del Sistema

### 4.1 Categoria GAMP 5

| Aspecto | Clasificacion | Justificacion |
|---------|---------------|---------------|
| Categoria GAMP | 5 | Software custom desarrollado internamente |
| Complejidad | Baja-Media | Sistema de soporte, no critico |
| Impacto GxP | Indirecto | No libera lotes, no ejecuta analisis |

### 4.2 Impacto en GxP

| Area de Impacto | Nivel | Justificacion |
|-----------------|-------|---------------|
| Seguridad del paciente | Bajo | Sistema no controla produccion ni libera lotes |
| Integridad de datos | Medio | Gestiona datos de trazabilidad de lotes |
| Calidad del producto | Bajo | Soporte a gestion de stock, no a QC |

### 4.3 Funciones del Sistema

| Funcion | Impacto GxP | Criticidad |
|---------|-------------|------------|
| Registro de lotes | Indirecto | Media |
| Movimientos de stock | Indirecto | Media |
| Registro de analisis | Indirecto | Media |
| Trazabilidad | Directo | Alta |
| Audit trail | Directo | Alta |
| Control de acceso | Directo | Alta |

---

## 5. Identificacion de Riesgos

### 5.1 Riesgos de Integridad de Datos

| ID | Riesgo | Causa Potencial | Consecuencia |
|----|--------|-----------------|--------------|
| R-ID-001 | Perdida de datos de lotes | Falla de hardware, error de BD | Perdida de trazabilidad |
| R-ID-002 | Modificacion no autorizada | Acceso indebido, error de usuario | Datos incorrectos |
| R-ID-003 | Eliminacion de registros | Error de usuario, falla de sistema | Perdida de historial |
| R-ID-004 | Datos inconsistentes | Error de validacion, bug de codigo | Informacion erronea |
| R-ID-005 | Audit trail incompleto | Falla de registro, bypass | Perdida de trazabilidad de cambios |

### 5.2 Riesgos de Disponibilidad

| ID | Riesgo | Causa Potencial | Consecuencia |
|----|--------|-----------------|--------------|
| R-DI-001 | Sistema no disponible | Falla de servidor, red, BD | Interrupcion de operaciones |
| R-DI-002 | Perdida de backup | Falla de proceso, storage | Imposibilidad de recuperacion |
| R-DI-003 | Tiempo de recuperacion excesivo | Proceso complejo, falta capacitacion | Indisponibilidad prolongada |

### 5.3 Riesgos de Seguridad

| ID | Riesgo | Causa Potencial | Consecuencia |
|----|--------|-----------------|--------------|
| R-SE-001 | Acceso no autorizado | Credenciales comprometidas | Modificacion indebida de datos |
| R-SE-002 | Escalacion de privilegios | Falla de control de roles | Acciones no permitidas |
| R-SE-003 | Ataque de fuerza bruta | Sin rate limiting | Acceso indebido |
| R-SE-004 | Sesion no cerrada | Falta de timeout | Acceso por terceros |

### 5.4 Riesgos de Cumplimiento

| ID | Riesgo | Causa Potencial | Consecuencia |
|----|--------|-----------------|--------------|
| R-CU-001 | Incumplimiento de Anexo 6 | Controles insuficientes | Hallazgo en auditoria |
| R-CU-002 | Documentacion incompleta | Falta de procedimientos | Hallazgo en auditoria |
| R-CU-003 | Falta de evidencia de validacion | No ejecucion de IQ/OQ | Observacion regulatoria |

### 5.5 Riesgos Operacionales

| ID | Riesgo | Causa Potencial | Consecuencia |
|----|--------|-----------------|--------------|
| R-OP-001 | Error de usuario en registro | Falta de validacion, capacitacion | Datos incorrectos |
| R-OP-002 | Operacion incorrecta irreversible | Falta de controles | Perdida de integridad |
| R-OP-003 | Dependencia de personal clave | Unico desarrollador/admin | Riesgo de continuidad |

---

## 6. Matriz de Riesgos

### 6.1 Evaluacion Inicial (Sin Controles)

| ID | Riesgo | P | I | Riesgo Inicial | Nivel |
|----|--------|---|---|----------------|-------|
| R-ID-001 | Perdida de datos | 2 | 3 | 6 | Alto |
| R-ID-002 | Modificacion no autorizada | 2 | 3 | 6 | Alto |
| R-ID-003 | Eliminacion de registros | 2 | 3 | 6 | Alto |
| R-ID-004 | Datos inconsistentes | 2 | 2 | 4 | Medio |
| R-ID-005 | Audit trail incompleto | 2 | 3 | 6 | Alto |
| R-DI-001 | Sistema no disponible | 2 | 2 | 4 | Medio |
| R-DI-002 | Perdida de backup | 2 | 3 | 6 | Alto |
| R-DI-003 | Tiempo recuperacion excesivo | 2 | 2 | 4 | Medio |
| R-SE-001 | Acceso no autorizado | 2 | 3 | 6 | Alto |
| R-SE-002 | Escalacion de privilegios | 2 | 2 | 4 | Medio |
| R-SE-003 | Ataque fuerza bruta | 3 | 2 | 6 | Alto |
| R-SE-004 | Sesion no cerrada | 2 | 2 | 4 | Medio |
| R-CU-001 | Incumplimiento Anexo 6 | 2 | 3 | 6 | Alto |
| R-CU-002 | Documentacion incompleta | 2 | 2 | 4 | Medio |
| R-CU-003 | Falta evidencia validacion | 2 | 2 | 4 | Medio |
| R-OP-001 | Error de usuario | 3 | 2 | 6 | Alto |
| R-OP-002 | Operacion irreversible | 2 | 2 | 4 | Medio |
| R-OP-003 | Dependencia personal clave | 2 | 2 | 4 | Medio |

### 6.2 Mapa de Calor (Riesgo Inicial)

```
        IMPACTO
        Bajo(1)  Medio(2)  Alto(3)
       +--------+--------+--------+
Frec(3)|   3    |   6    |   9    |  <- R-SE-003, R-OP-001
       +--------+--------+--------+
Ocas(2)|   2    |   4    |   6    |  <- Mayoria de riesgos
       +--------+--------+--------+
Rara(1)|   1    |   2    |   3    |
       +--------+--------+--------+

Leyenda: Verde(1-2), Amarillo(3-4), Rojo(6-9)
```

---

## 7. Controles Implementados

### 7.1 Controles de Integridad de Datos

| ID Riesgo | Control | Tipo | Evidencia |
|-----------|---------|------|-----------|
| R-ID-001 | Backup automatico semanal | Preventivo | SOP-001, logs backup |
| R-ID-001 | Prueba de restore trimestral | Detectivo | Registros restore |
| R-ID-002 | Control de acceso por roles | Preventivo | 8 roles definidos |
| R-ID-002 | Audit trail de cambios | Detectivo | Tabla auditoria_cambios |
| R-ID-003 | Soft delete (eliminacion logica) | Preventivo | @SQLDelete en entidades |
| R-ID-003 | Sin opcion de DELETE fisico | Preventivo | No hay UI de eliminacion |
| R-ID-004 | Validacion frontend y backend | Preventivo | Validators en DTOs |
| R-ID-004 | Constraints en base de datos | Preventivo | Schema PostgreSQL |
| R-ID-005 | Motivo obligatorio (min 20 chars) | Preventivo | Validacion en servicios |
| R-ID-005 | Registro automatico en todas las operaciones | Preventivo | AuditoriaCambios entity |

### 7.2 Controles de Disponibilidad

| ID Riesgo | Control | Tipo | Evidencia |
|-----------|---------|------|-----------|
| R-DI-001 | Infraestructura Docker | Preventivo | docker-compose.yml |
| R-DI-001 | Monitoreo de salud (/actuator/health) | Detectivo | Spring Actuator |
| R-DI-002 | Backup automatico semanal | Preventivo | SOP-001 |
| R-DI-002 | Retencion 30 dias | Preventivo | Script backup |
| R-DI-003 | Procedimiento documentado | Preventivo | SOP-001 |
| R-DI-003 | RTO definido (4 horas) | Preventivo | SOP-001 |

### 7.3 Controles de Seguridad

| ID Riesgo | Control | Tipo | Evidencia |
|-----------|---------|------|-----------|
| R-SE-001 | Autenticacion obligatoria | Preventivo | Spring Security |
| R-SE-001 | Contrasenas encriptadas (BCrypt) | Preventivo | Configuracion seguridad |
| R-SE-002 | 8 roles con permisos definidos | Preventivo | RoleEnum, PermisosCasoUsoEnum |
| R-SE-002 | Jerarquia por areas | Preventivo | RoleEnum.getArea() |
| R-SE-003 | Rate limiting (5 intentos) | Preventivo | LoginAttemptService |
| R-SE-003 | Bloqueo 15 minutos | Preventivo | LoginBlockFilter |
| R-SE-004 | Timeout de sesion (30 min PROD) | Preventivo | application-PROD.yml |
| R-SE-004 | Logout explicito | Preventivo | UI, Spring Security |

### 7.4 Controles de Cumplimiento

| ID Riesgo | Control | Tipo | Evidencia |
|-----------|---------|------|-----------|
| R-CU-001 | Documentacion formal ANMAT | Preventivo | ERU-001, IQOQ-001, SOP-001, MAN-001 |
| R-CU-001 | Mapeo a Anexo 6 | Preventivo | RSK-001 (este documento) |
| R-CU-002 | SOPs documentados | Preventivo | SOP-001 |
| R-CU-002 | Manual de usuario | Preventivo | MAN-001 |
| R-CU-003 | Protocolo IQ/OQ | Preventivo | IQOQ-001 |
| R-CU-003 | Matriz de trazabilidad | Preventivo | ERU-001, IQOQ-001 |

### 7.5 Controles Operacionales

| ID Riesgo | Control | Tipo | Evidencia |
|-----------|---------|------|-----------|
| R-OP-001 | Validacion de campos obligatorios | Preventivo | Validators |
| R-OP-001 | Mensajes de error claros | Correctivo | UI Thymeleaf |
| R-OP-002 | Funcion de reverso de movimiento | Correctivo | CU-ReversoMovimiento |
| R-OP-002 | Autorizacion por jerarquia para reversos | Preventivo | ReversoAuthorizationService |
| R-OP-003 | Documentacion tecnica (CLAUDE.md) | Preventivo | Repositorio Git |
| R-OP-003 | Documentacion de usuario | Preventivo | MAN-001 |

---

## 8. Riesgo Residual

### 8.1 Evaluacion Post-Controles

| ID | Riesgo | P | I | Riesgo Residual | Nivel | Aceptable |
|----|--------|---|---|-----------------|-------|-----------|
| R-ID-001 | Perdida de datos | 1 | 2 | 2 | Bajo | Si |
| R-ID-002 | Modificacion no autorizada | 1 | 2 | 2 | Bajo | Si |
| R-ID-003 | Eliminacion de registros | 1 | 1 | 1 | Bajo | Si |
| R-ID-004 | Datos inconsistentes | 1 | 2 | 2 | Bajo | Si |
| R-ID-005 | Audit trail incompleto | 1 | 2 | 2 | Bajo | Si |
| R-DI-001 | Sistema no disponible | 2 | 2 | 4 | Medio | Si* |
| R-DI-002 | Perdida de backup | 1 | 2 | 2 | Bajo | Si |
| R-DI-003 | Tiempo recuperacion excesivo | 1 | 2 | 2 | Bajo | Si |
| R-SE-001 | Acceso no autorizado | 1 | 2 | 2 | Bajo | Si |
| R-SE-002 | Escalacion de privilegios | 1 | 2 | 2 | Bajo | Si |
| R-SE-003 | Ataque fuerza bruta | 1 | 2 | 2 | Bajo | Si |
| R-SE-004 | Sesion no cerrada | 1 | 2 | 2 | Bajo | Si |
| R-CU-001 | Incumplimiento Anexo 6 | 1 | 2 | 2 | Bajo | Si |
| R-CU-002 | Documentacion incompleta | 1 | 2 | 2 | Bajo | Si |
| R-CU-003 | Falta evidencia validacion | 1 | 2 | 2 | Bajo | Si |
| R-OP-001 | Error de usuario | 2 | 2 | 4 | Medio | Si* |
| R-OP-002 | Operacion irreversible | 1 | 2 | 2 | Bajo | Si |
| R-OP-003 | Dependencia personal clave | 2 | 2 | 4 | Medio | Si* |

**Notas:**
- (*) Riesgos medios aceptados con monitoreo continuo

### 8.2 Mapa de Calor (Riesgo Residual)

```
        IMPACTO
        Bajo(1)  Medio(2)  Alto(3)
       +--------+--------+--------+
Frec(3)|        |        |        |
       +--------+--------+--------+
Ocas(2)|        | R-DI-001|        |
       |        | R-OP-001|        |
       |        | R-OP-003|        |
       +--------+--------+--------+
Rara(1)| R-ID-003| Resto  |        |
       +--------+--------+--------+

Todos los riesgos en zona Verde/Amarilla
```

### 8.3 Riesgos Aceptados con Condiciones

| ID | Riesgo | Condicion de Aceptacion |
|----|--------|------------------------|
| R-DI-001 | Sistema no disponible | Monitoreo activo, RTO 4h |
| R-OP-001 | Error de usuario | Capacitacion, manual disponible |
| R-OP-003 | Dependencia personal clave | Documentacion completa |

---

## 9. Conclusion

### 9.1 Resumen de Evaluacion

| Aspecto | Resultado |
|---------|-----------|
| Total de riesgos identificados | 18 |
| Riesgos iniciales ALTOS | 10 (56%) |
| Riesgos iniciales MEDIOS | 8 (44%) |
| Riesgos residuales ALTOS | 0 (0%) |
| Riesgos residuales MEDIOS | 3 (17%) |
| Riesgos residuales BAJOS | 15 (83%) |

### 9.2 Determinacion Final

Basado en la evaluacion de riesgos:

- [x] Los controles implementados reducen los riesgos a niveles aceptables
- [x] El nivel de validacion es proporcional al riesgo del sistema
- [x] El sistema es apto para uso en produccion con los controles actuales

### 9.3 Recomendaciones

1. **Ejecutar IQ/OQ** y documentar evidencia antes de auditoria
2. **Realizar prueba de restore** trimestral y documentar resultados
3. **Revisar** esta evaluacion anualmente o ante cambios significativos
4. **Monitorear** riesgos medios residuales (R-DI-001, R-OP-001, R-OP-003)

---

## 10. Anexos

### 10.1 Trazabilidad a Anexo 6

| Requisito ANMAT | Riesgo Relacionado | Control |
|-----------------|-------------------|---------|
| 1.1 Gestion de riesgos | Todos | Este documento (RSK-001) |
| 7.2 Backup de datos | R-ID-001, R-DI-002 | SOP-001 |
| 9 Audit trail | R-ID-002, R-ID-005 | AuditoriaCambios |
| 12 Seguridad | R-SE-001 a R-SE-004 | Spring Security, LoginAttemptService |

### 10.2 Documentos Relacionados

| Documento | Codigo | Relacion |
|-----------|--------|----------|
| Especificaciones de Requerimientos | ERU-001 | Define requerimientos de control |
| Protocolo IQ/OQ | IQOQ-001 | Verifica controles |
| SOP Backup | SOP-001 | Implementa controles R-ID-001, R-DI-002 |
| Designacion de Roles | ROL-001 | Define responsables |

### 10.3 Proxima Revision

| Aspecto | Valor |
|---------|-------|
| Fecha proxima revision | ____/____/____ (1 año) |
| Responsable | System Owner |
| Trigger de revision anticipada | Cambio mayor al sistema, incidente critico |

---

**Fin del documento**

---

**Aprobacion Final:**

| | Firma | Fecha |
|---|-------|-------|
| **Elaborado por:** | _________________ | ____/____/____ |
| **Revisado por (QA):** | _________________ | ____/____/____ |
| **Aprobado por (System Owner):** | _________________ | ____/____/____ |
