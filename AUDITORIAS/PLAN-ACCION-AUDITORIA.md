# Plan de Accion para Auditoria ANMAT
## Evaluacion del Sistema Conitrack contra Estandares de Auditoria

> **NOTA:** Este documento ha sido SUPERSEDIDO por `PLAN-ACCION-BLINDADO.md` que proporciona
> un plan de accion mas completo con 39 entregables especificos y respuestas blindadas.
> Mantener este documento solo como referencia de analisis de gaps inicial.

**Fecha de evaluacion:** 2025-12-09
**Sistema:** Conitrack v1.0
**Evaluador:** Analisis automatizado basado en documentos de AUDITORIAS/

---

## Resumen Ejecutivo

### Estado General del Sistema

| Aspecto | Estado | Preparacion para Auditoria |
|---------|--------|---------------------------|
| **Implementacion Tecnica** | COMPLETA | LISTO |
| **Documentacion Formal** | PARCIAL | NO LISTO - Falta completar y firmar |
| **Evidencias de Ejecucion** | AUSENTE | NO LISTO - Falta ejecutar y documentar |
| **Registros Operativos** | AUSENTE | NO LISTO - Falta iniciar registros |

### Veredicto: **NO LISTO PARA AUDITORIA**

El sistema tiene una implementacion tecnica robusta pero carece de la documentacion formal, evidencias de ejecucion y registros operativos requeridos por ANMAT Anexo 6.

---

## Evaluacion por Documento de Auditoria

### 1. CONITRACK-ANMAT-GUIA-GLOBAL.md

**20 documentos minimos requeridos - Estado actual:**

| # | Documento Requerido | Estado | Gap |
|---|---------------------|--------|-----|
| 1 | Clasificacion GAMP y Criticidad | NO EXISTE | Documento formal con firmas |
| 2 | Risk Assessment | NO EXISTE | Matriz de riesgos firmada |
| 3 | Designacion de roles (System/Process Owner) | NO EXISTE | Documento de designacion formal |
| 4 | Evaluacion de proveedor interno | NO EXISTE | Documento describiendo al desarrollador |
| 5 | URS - Especificaciones de Usuario | BOCETO | Completar y firmar ERU-001 |
| 6 | Plan de Validacion (CSV Plan) | NO EXISTE | Crear documento formal |
| 7 | Matriz de trazabilidad URS → Test | NO EXISTE | Crear matriz |
| 8 | IQ - Installation Qualification | BOCETO | Completar y ejecutar IQOQ-001 |
| 9 | OQ - Operational Qualification | BOCETO | Completar y ejecutar IQOQ-001 |
| 10 | PQ - Performance Qualification | NO EXISTE | Ejecutar periodo de observacion |
| 11 | SOP de Backup y Restore | BOCETO | Completar SOP-001, firmar, ejecutar |
| 12 | Audit Trail (implementacion) | IMPLEMENTADO | OK - Solo falta revision periodica |
| 13 | SOP de Gestion de Cambios | PLANTILLA | Completar, firmar, usar |
| 14 | Registro de Cambios | NO EXISTE | Iniciar registro |
| 15 | Gestion de Incidencias | PLANTILLA | Completar, firmar, iniciar registro |
| 16 | Evaluacion Periodica | PLANTILLA | Ejecutar primera evaluacion |
| 17 | Politica de Seguridad | NO EXISTE | Crear documento formal |
| 18 | Procedimiento de Contingencia | NO EXISTE | Crear SOP |
| 19 | Analisis de archivo y legibilidad | NO EXISTE | Crear documento |
| 20 | Mapeo Anexo 6 → Controles | PLANTILLA | Completar con evidencias |

**Resultado: 1/20 completo, 7/20 en boceto, 12/20 ausente**

---

### 2. CONITRACK-ANMAT-PREGUNTAS-AUDITORIA.md

**Analisis de preguntas criticas - Capacidad de respuesta:**

#### Preguntas Trampa (Seccion 1)
| Pregunta | Respuesta Actual | Riesgo |
|----------|------------------|--------|
| Reconstruir movimientos de lote de 18 meses? | SI - Audit trail implementado | BAJO |
| Documento QA aprobando validacion proporcional? | NO EXISTE | ALTO |
| Campos editables via SQL sin rastro? | NO - Hibernate Envers a nivel aplicacion | BAJO (BD solo accesible por IT autorizado) |
| Usuarios compartidos? | NO - Politica de usuarios unicos | BAJO |
| Version en produccion = version validada? | NO DOCUMENTADO | ALTO |

#### Preguntas sobre Contradicciones (Seccion 2)
| Pregunta | Respuesta Actual | Riesgo |
|----------|------------------|--------|
| Estado real: completo o pendiente? | Tecnico completo, docs pendiente | ALTO |
| Validacion formal pendiente pero cumple Anexo 6? | CONTRADICCION - No podemos afirmarlo | CRITICO |
| Gestion de cambios solo con Git? | SI - Falta SOP formal | ALTO |
| Backup probado? | NO - Solo scripts existen | ALTO |
| Mapeo Anexo 6 explicito? | PLANTILLA - No completado | ALTO |
| Evaluacion periodica realizada? | NO - Nunca se hizo | ALTO |

#### Preguntas ANMAT Especificas (Seccion 4)
| Pregunta | Respuesta Actual | Riesgo |
|----------|------------------|--------|
| Inventario de sistemas informatizados? | NO EXISTE | ALTO |
| Designacion formal System/Process Owner? | NO EXISTE | CRITICO |
| Evaluacion de proveedor hosting? | NO EXISTE | MEDIO |
| Documento que limite uso del sistema? | NO EXISTE | ALTO |
| SOP backup con verificacion restore? | BOCETO - No ejecutado | ALTO |
| Revision regular audit trail documentada? | NO EXISTE | ALTO |
| Evaluacion periodica realizada? | NO - Nunca | CRITICO |

**Resultado: Sistema NO puede responder satisfactoriamente 15+ preguntas criticas**

---

### 3. CONITRACK-ANMAT-CLASIFICACION-Y-RIESGOS.md

**Matriz de Riesgos - Estado de Controles:**

| Riesgo | Control Requerido | Implementado | Evidenciado |
|--------|-------------------|--------------|-------------|
| Modificacion no autorizada stock | Control acceso + Audit trail | SI | NO |
| Eliminacion fisica registros | Soft deletes | SI | NO |
| Datos invalidos | Validaciones | SI | NO |
| Acceso no autorizado | Login + Bloqueo | SI | NO |
| Rol excesivo | Matriz roles + SOP usuarios | SI | NO |
| Caida sin backup | Backup automatizado | PARCIAL | NO |
| Sistema fuera de servicio | Procedimiento contingencia | NO | NO |
| Uso indebido del sistema | SOP de uso | NO | NO |
| Falta entrenamiento | Manual + Registros | PARCIAL | NO |
| Cambio sin evaluacion | SOP cambios | NO | NO |

**Resultado: Controles tecnicos implementados, pero sin evidencia documental**

---

### 4. CONITRACK-ANMAT-PLAN-VALIDACION.md

**Actividades de Validacion - Estado:**

| Actividad | Estado | Evidencia |
|-----------|--------|-----------|
| URS definidas y aprobadas | BOCETO | Sin firmas |
| Risk Assessment completado y firmado | NO EXISTE | - |
| IQ realizado | NO | - |
| OQ ejecutado | NO | - |
| PQ completado | NO | - |
| Prueba restore documentada | NO | - |
| Audit trail verificado | IMPLEMENTADO | Sin revision formal |
| Reporte de validacion | NO EXISTE | - |

**Resultado: 0/8 criterios de aceptacion cumplidos**

---

### 5. CONITRACK-ANMAT-MAPEO-ANEXO6.md

**Mapeo de Requisitos Anexo 6 - Cumplimiento:**

| Punto Anexo 6 | Requisito | Implementado | Documentado | Evidenciado |
|---------------|-----------|--------------|-------------|-------------|
| 1.1 | Gestion de riesgos | NO | NO | NO |
| 2.1 | Personal (roles) | NO | NO | NO |
| 3.x | Proveedores | NO | NO | NO |
| 4.x | Validacion | PARCIAL | BOCETO | NO |
| 5-6 | Datos y exactitud | SI | NO | NO |
| 7.x | Backup | PARCIAL | BOCETO | NO |
| 8 | Impresiones | SI | NO | NO |
| 9 | Audit trail | SI | NO | NO |
| 10 | Gestion cambios | NO | PLANTILLA | NO |
| 11 | Evaluacion periodica | NO | PLANTILLA | NO |
| 12.x | Seguridad | SI | NO | NO |
| 13 | Incidencias | NO | PLANTILLA | NO |
| 14-15 | Firma/Liberacion | N/A | NO | NO |
| 16 | Continuidad | NO | NO | NO |
| 17 | Archivo | NO | NO | NO |

**Resultado: Implementacion tecnica buena, documentacion casi inexistente**

---

### 6-10. SOPs y Evaluacion Periodica

**Estado de SOPs:**

| SOP | Existe en AUDITORIAS/ | Firmado | Ejecutado | Registros |
|-----|----------------------|---------|-----------|-----------|
| Backup y Restore | SI (plantilla) | NO | NO | NO |
| Gestion Usuarios | SI (plantilla) | NO | NO | NO |
| Gestion Incidencias | SI (plantilla) | NO | NO | NO |
| Gestion Cambios | SI (plantilla) | NO | NO | NO |
| Evaluacion Periodica | SI (plantilla) | NO | NO | NO |

**Resultado: 5 SOPs como plantilla, 0 firmados, 0 ejecutados, 0 con registros**

---

## Plan de Accion Consolidado

### Prioridad CRITICA (Bloqueante para auditoria)

| ID | Accion | Responsable | Plazo | Entregable |
|----|--------|-------------|-------|------------|
| C1 | Designar formalmente System Owner y Process Owner | Direccion | 1 semana | Documento firmado |
| C2 | Crear documento de Clasificacion GAMP con firmas | System Owner | 1 semana | CTK-GAMP-001 firmado |
| C3 | Completar y firmar Risk Assessment | System Owner + QA | 2 semanas | CTK-RA-001 firmado |
| C4 | Completar y aprobar ERU (URS) | System Owner + Process Owner + QA | 2 semanas | ERU-001 firmado |
| C5 | Crear y aprobar Plan de Validacion | System Owner + QA | 1 semana | CTK-VP-001 firmado |
| C6 | Ejecutar IQ/OQ con evidencias | System Owner | 2 semanas | IQOQ-001 ejecutado |
| C7 | Ejecutar prueba de restore documentada | IT | 1 semana | Informe de restore |
| C8 | Firmar y activar SOP Backup | System Owner + QA | 1 semana | SOP-BAK-001 firmado |
| C9 | Firmar y activar SOP Gestion Cambios | System Owner + QA | 1 semana | SOP-CHG-001 firmado |
| C10 | Ejecutar primera Evaluacion Periodica | System Owner + Process Owner + QA | 2 semanas | CTK-REV-001 firmado |

### Prioridad ALTA (Necesario para auditoria robusta)

| ID | Accion | Responsable | Plazo | Entregable |
|----|--------|-------------|-------|------------|
| A1 | Crear documento de Evaluacion Proveedor Interno | QA | 1 semana | CTK-PROV-001 |
| A2 | Firmar y activar SOP Gestion Usuarios | System Owner | 1 semana | SOP-USR-001 firmado |
| A3 | Firmar y activar SOP Gestion Incidencias | System Owner | 1 semana | SOP-INC-001 firmado |
| A4 | Iniciar Registro de Cambios | System Owner | Inmediato | Planilla activa |
| A5 | Iniciar Registro de Incidencias | System Owner | Inmediato | Planilla activa |
| A6 | Crear Politica de Seguridad formal | System Owner + IT | 2 semanas | CTK-SEC-001 |
| A7 | Crear Procedimiento de Contingencia | System Owner + Process Owner | 2 semanas | SOP-CONT-001 |
| A8 | Crear Matriz de Trazabilidad URS → Test | System Owner | 1 semana | Matriz completa |
| A9 | Completar Mapeo Anexo 6 con evidencias | System Owner + QA | 2 semanas | Mapeo completo |
| A10 | Documentar revision periodica de audit trail | QA | 1 semana | Procedimiento + 1ra revision |

### Prioridad MEDIA (Recomendado para auditoria excelente)

| ID | Accion | Responsable | Plazo | Entregable |
|----|--------|-------------|-------|------------|
| M1 | Ejecutar PQ (periodo observacion 2-4 semanas) | Process Owner | 4 semanas | Informe PQ |
| M2 | Crear Manual de Usuario con screenshots | System Owner | 2 semanas | MAN-001 completo |
| M3 | Crear Inventario de Sistemas Informatizados | QA | 1 semana | Inventario |
| M4 | Automatizar backup con cron | IT | 1 semana | Cron activo + logs |
| M5 | Crear documento de Archivo y Legibilidad | System Owner | 1 semana | CTK-ARCH-001 |
| M6 | Registrar al menos 1 incidencia de ejemplo | System Owner | 1 semana | INC-001 completo |
| M7 | Registrar al menos 1 cambio de ejemplo | System Owner | 1 semana | CHG-001 completo |
| M8 | Documentar entrenamiento de usuarios | Process Owner | 2 semanas | Registros de capacitacion |
| M9 | Crear documento limitando uso del sistema | Process Owner | 1 semana | SOP de uso |
| M10 | Preparar respuestas a preguntas de auditoria | System Owner + QA | 2 semanas | Guia de respuestas |

---

## Cronograma Sugerido

### Semana 1-2: Fundamentos
- C1: Designar roles
- C2: Clasificacion GAMP
- C5: Plan de Validacion
- C8: SOP Backup (firmar)
- C9: SOP Cambios (firmar)
- A2, A3: SOPs usuarios/incidencias (firmar)
- A4, A5: Iniciar registros

### Semana 3-4: Documentacion Core
- C3: Risk Assessment
- C4: ERU completo
- A1: Evaluacion proveedor
- A6: Politica seguridad
- A7: Procedimiento contingencia
- A8: Matriz trazabilidad

### Semana 5-6: Validacion
- C6: Ejecutar IQ/OQ
- C7: Prueba restore
- A9: Mapeo Anexo 6
- A10: Revision audit trail
- M4: Automatizar backup

### Semana 7-8: Cierre
- C10: Evaluacion Periodica
- M1: Iniciar PQ (continua 4 semanas mas)
- M2: Manual usuario
- M6, M7: Ejemplos incidencia/cambio
- M10: Guia respuestas auditoria

### Semana 9-12: Maduracion
- Completar PQ
- M8: Entrenamientos
- Revision final de documentacion
- Mock audit interna

---

## Recursos Necesarios

| Recurso | Dedicacion | Semanas | Total Horas |
|---------|------------|---------|-------------|
| System Owner | 50% | 8 | 160 h |
| Process Owner | 25% | 8 | 80 h |
| QA | 25% | 8 | 80 h |
| IT | 10% | 4 | 16 h |
| **Total** | | | **336 h** |

---

## Metricas de Seguimiento

| Metrica | Semana 2 | Semana 4 | Semana 6 | Semana 8 |
|---------|----------|----------|----------|----------|
| Docs CRITICOS firmados | 4/10 | 8/10 | 10/10 | 10/10 |
| Docs ALTOS completados | 2/10 | 6/10 | 9/10 | 10/10 |
| SOPs activos | 4/5 | 5/5 | 5/5 | 5/5 |
| Registros iniciados | 2/2 | 2/2 | 2/2 | 2/2 |
| IQ/OQ ejecutado | 0% | 50% | 100% | 100% |
| Restore probado | NO | SI | SI | SI |

---

## Criterios de Exito

El sistema estara **LISTO PARA AUDITORIA** cuando:

1. [ ] 10 acciones CRITICAS completadas
2. [ ] 10 acciones ALTAS completadas
3. [ ] 5 SOPs firmados y con al menos 1 registro cada uno
4. [ ] IQ/OQ ejecutado con evidencias
5. [ ] Restore probado y documentado
6. [ ] Evaluacion Periodica completada
7. [ ] Mapeo Anexo 6 con evidencias
8. [ ] Mock audit interna pasada

---

## Aprobaciones

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| System Owner | | | |
| Process Owner | | | |
| QA | | | |

---

**Documento generado:** 2025-12-09
**Proxima revision:** Al completar Semana 4
