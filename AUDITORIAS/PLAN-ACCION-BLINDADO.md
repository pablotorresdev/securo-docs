# PLAN DE ACCION BLINDADO PARA AUDITORIA ANMAT
## Garantia de Aprobacion - Sistema Conitrack

**Fecha:** 2025-12-09
**Objetivo:** Preparar el sistema para que NO EXISTA FORMA de que la auditoria no pase
**Principio rector:** "Si no esta documentado, firmado y evidenciado, NO EXISTE"

---

## PRINCIPIOS FUNDAMENTALES

### Las 5 Reglas de Oro para Aprobar

1. **Todo documento debe tener firmas** - Sin firma de System Owner, Process Owner y QA, el documento no vale
2. **Toda afirmacion debe tener evidencia** - Si dices que algo existe, debes poder mostrarlo
3. **Todo requisito del Anexo 6 debe estar mapeado** - Cada punto debe tener control, SOP y evidencia
4. **Toda pregunta del auditor debe tener respuesta documental** - Nunca responder "de memoria"
5. **Todo registro debe existir antes de la auditoria** - No se pueden crear registros retroactivos

---

## INVENTARIO COMPLETO DE ENTREGABLES

### A. DOCUMENTOS DE GOBIERNO (11 documentos)

| # | Documento | Codigo | Firmas Requeridas | Estado |
|---|-----------|--------|-------------------|--------|
| A1 | Inventario de Sistemas Informatizados | CTK-INV-001 | QA Director | [ ] |
| A2 | Clasificacion GAMP y Criticidad | CTK-GAMP-001 | SO + PO + QA | [ ] |
| A3 | Designacion System Owner | CTK-ROL-001 | Direccion General | [ ] |
| A4 | Designacion Process Owner | CTK-ROL-002 | Direccion General | [ ] |
| A5 | Evaluacion Proveedor Interno | CTK-PROV-001 | QA + Direccion | [ ] |
| A6 | Politica de Seguridad | CTK-SEC-001 | SO + IT + QA | [ ] |
| A7 | Politica de Archivo y Retencion | CTK-ARCH-001 | SO + QA | [ ] |
| A8 | Documento de Alcance y Limitaciones | CTK-ALC-001 | SO + PO + QA | [ ] |
| A9 | Analisis Transicion Excel a Conitrack | CTK-TRANS-001 | SO + PO + QA | [ ] |
| A10 | Evaluacion Herramientas de Testing | CTK-TOOL-001 | SO + QA | [ ] |
| A11 | Declaracion Sistema Standalone | CTK-STAND-001 | SO + IT + QA | [ ] |

### B. DOCUMENTOS DE VALIDACION (8 documentos)

| # | Documento | Codigo | Firmas Requeridas | Estado |
|---|-----------|--------|-------------------|--------|
| B1 | Plan de Validacion (CSV Plan) | CTK-VP-001 | SO + PO + QA | [ ] |
| B2 | Risk Assessment | CTK-RA-001 | SO + PO + QA | [ ] |
| B3 | URS - Especificaciones Usuario | CTK-URS-001 | SO + PO + QA | [ ] |
| B4 | Matriz Trazabilidad URS-Test | CTK-MTZ-001 | SO + QA | [ ] |
| B5 | Protocolo IQ (ejecutado) | CTK-IQ-001 | SO + QA | [ ] |
| B6 | Protocolo OQ (ejecutado) | CTK-OQ-001 | SO + PO + QA | [ ] |
| B7 | Protocolo PQ (ejecutado) | CTK-PQ-001 | SO + PO + QA | [ ] |
| B8 | Reporte de Validacion Final | CTK-VAL-001 | SO + PO + QA | [ ] |

### C. PROCEDIMIENTOS OPERATIVOS (7 SOPs)

| # | Documento | Codigo | Firmas Requeridas | Estado |
|---|-----------|--------|-------------------|--------|
| C1 | SOP Backup y Restore | SOP-CTK-BAK-001 | SO + IT + QA | [ ] |
| C2 | SOP Gestion de Usuarios | SOP-CTK-USR-001 | SO + QA | [ ] |
| C3 | SOP Gestion de Cambios | SOP-CTK-CHG-001 | SO + QA | [ ] |
| C4 | SOP Gestion de Incidencias | SOP-CTK-INC-001 | SO + QA | [ ] |
| C5 | SOP Contingencia | SOP-CTK-CON-001 | SO + PO + QA | [ ] |
| C6 | SOP Revision Audit Trail | SOP-CTK-AUD-001 | SO + QA | [ ] |
| C7 | SOP Uso del Sistema Conitrack | SOP-CTK-USO-001 | SO + PO + QA | [ ] |

### D. REGISTROS OPERATIVOS (7 registros)

| # | Registro | Codigo | Requisito | Estado |
|---|----------|--------|-----------|--------|
| D1 | Registro de Usuarios | REG-USR-001 | Lista actualizada con altas/bajas | [ ] |
| D2 | Registro de Cambios | REG-CHG-001 | Minimo 1 cambio documentado | [ ] |
| D3 | Registro de Incidencias | REG-INC-001 | Minimo 1 incidencia documentada | [ ] |
| D4 | Registro de Backups | REG-BAK-001 | Log de backups ejecutados | [ ] |
| D5 | Registro de Restore | REG-RST-001 | Prueba de restore ejecutada | [ ] |
| D6 | Registro Revision Audit Trail | REG-AUD-001 | Minimo 1 revision documentada | [ ] |
| D7 | Registro de Entrenamiento | REG-TRN-001 | Capacitacion de usuarios | [ ] |

### E. EVALUACIONES (2 documentos)

| # | Documento | Codigo | Firmas Requeridas | Estado |
|---|-----------|--------|-------------------|--------|
| E1 | Evaluacion Periodica Anual | CTK-REV-001 | SO + PO + QA | [ ] |
| E2 | Mapeo Anexo 6 Completo | CTK-MAP-001 | SO + QA | [ ] |

### F. DOCUMENTACION DE SOPORTE (3 documentos)

| # | Documento | Codigo | Proposito | Estado |
|---|-----------|--------|-----------|--------|
| F1 | Manual de Usuario | CTK-MAN-001 | Uso del sistema | [ ] |
| F2 | Guia de Respuestas Auditoria | CTK-AUD-RESP | Preparacion equipo | [ ] |
| F3 | Checklist Pre-Auditoria | CTK-CHK-001 | Verificacion final | [ ] |

**TOTAL: 39 entregables** (11 Gobierno + 8 Validacion + 7 SOPs + 7 Registros + 2 Evaluaciones + 4 Soporte)

---

## RESPUESTAS BLINDADAS A PREGUNTAS CRITICAS

### Seccion 1: Preguntas Trampa

| Pregunta | Respuesta Blindada | Documento Soporte |
|----------|-------------------|-------------------|
| "¿Garantiza que ningun dato se usa para decision GxP?" | "El alcance del sistema esta definido en CTK-ALC-001 donde se explicita que Conitrack NO se utiliza para liberacion de lotes. Cualquier uso fuera de este alcance requiere re-evaluacion segun SOP-CTK-CHG-001" | CTK-ALC-001 |
| "¿Puede reconstruir movimientos de 18 meses?" | "Si. El audit trail esta implementado con Hibernate Envers (tablas *_AUD + revinfo) con retencion indefinida. Ver evidencia en CTK-OQ-001 caso TC-031" | CTK-OQ-001, logs |
| "¿QA aprobo la validacion proporcional?" | "Si. Ver firma de QA en CTK-VP-001 Plan de Validacion y CTK-VAL-001 Reporte de Validacion" | CTK-VP-001, CTK-VAL-001 |
| "¿Hay campos editables via SQL sin rastro?" | "El sistema implementa audit trail a nivel aplicacion. El acceso directo a BD esta restringido segun CTK-SEC-001. Solo IT con autorizacion documentada puede acceder" | CTK-SEC-001 |
| "¿Existen usuarios compartidos?" | "No. La politica CTK-SEC-001 prohibe usuarios compartidos. Ver REG-USR-001 con lista de usuarios unicos" | CTK-SEC-001, REG-USR-001 |
| "¿Version en produccion = version validada?" | "Si. Ver CTK-VAL-001 seccion 'Version Validada' que coincide con tag Git en produccion" | CTK-VAL-001, Git tag |
| "¿Donde dice que no libera lotes?" | "CTK-ALC-001 seccion 2.3 'Limitaciones del Sistema' y SOP-CTK-USO-001 seccion 'Usos Prohibidos'" | CTK-ALC-001, SOP-CTK-USO-001 |
| "¿Conitrack decide que lote entregar?" | "No. SOP-CTK-USO-001 seccion 3 explicita que la decision de despacho es externa. Conitrack solo registra movimientos" | SOP-CTK-USO-001 |
| "¿El Excel anterior era aceptable GxP?" | "Se realizo analisis de transicion en CTK-TRANS-001 donde se documenta estado anterior, riesgos identificados, y mejoras con Conitrack" | CTK-TRANS-001 |
| "¿Algun bug obligo correccion manual?" | "Todo incidente queda en REG-INC-001. La seccion 5 de CTK-TRANS-001 documenta correcciones historicas si existieron" | REG-INC-001, CTK-TRANS-001 |
| "¿Escenario de stock falso positivo?" | "CTK-RA-001 riesgo R-07 analiza escenarios de discrepancia de stock con controles y mitigacion" | CTK-RA-001 |

### Seccion 2: Preguntas sobre Contradicciones

| Pregunta | Respuesta Blindada | Documento Soporte |
|----------|-------------------|-------------------|
| "¿Estado real: completo o pendiente?" | "El sistema esta VALIDADO. Ver CTK-VAL-001 Reporte de Validacion firmado" | CTK-VAL-001 |
| "¿Cumple Anexo 6?" | "Si. Ver CTK-MAP-001 mapeo completo punto por punto con evidencias" | CTK-MAP-001 |
| "¿Git es gestion de cambios?" | "Git es la herramienta tecnica. El proceso formal esta en SOP-CTK-CHG-001. Ver REG-CHG-001 con cambios documentados" | SOP-CTK-CHG-001, REG-CHG-001 |
| "¿Backup probado?" | "Si. Ver REG-RST-001 con fecha, procedimiento y resultado de restore exitoso" | REG-RST-001 |
| "¿Evaluacion periodica realizada?" | "Si. Ver CTK-REV-001 firmado por SO, PO y QA" | CTK-REV-001 |
| "¿Analisis comparativo Excel vs Conitrack?" | "Si. CTK-TRANS-001 seccion 4 compara riesgos del proceso manual vs sistema informatizado" | CTK-TRANS-001 |
| "¿Reportes GxP de tests, no solo coverage?" | "Si. CTK-OQ-001 contiene casos de prueba con formato GxP (procedimiento, resultado esperado, obtenido, firma). Adicionalmente CTK-TOOL-001 evalua JUnit/JaCoCo como herramientas idoneas" | CTK-OQ-001, CTK-TOOL-001 |
| "¿Por que no firma electronica para movimientos?" | "CTK-ALC-001 seccion 3 declara que el sistema NO se usa para liberacion de lotes ni decisiones criticas de calidad. El punto 14 del Anexo 6 aplica solo a registros equivalentes a firma manuscrita para liberacion. Ver CTK-GAMP-001 que justifica criticidad baja" | CTK-ALC-001, CTK-GAMP-001 |

### Seccion 3: Preguntas ANMAT Especificas

| Pregunta | Respuesta Blindada | Documento Soporte |
|----------|-------------------|-------------------|
| "¿Inventario de sistemas?" | "Si. CTK-INV-001 donde Conitrack figura como sistema de soporte GxP indirecto" | CTK-INV-001 |
| "¿Designacion System/Process Owner?" | "CTK-ROL-001 y CTK-ROL-002 firmados por Direccion General" | CTK-ROL-001, CTK-ROL-002 |
| "¿Evaluacion proveedor hosting?" | "CTK-PROV-001 incluye evaluacion de infraestructura (Railway/CloudFlare R2)" | CTK-PROV-001 |
| "¿SOP backup con verificacion restore?" | "SOP-CTK-BAK-001 seccion 7 define procedimiento. REG-RST-001 evidencia ejecucion" | SOP-CTK-BAK-001, REG-RST-001 |
| "¿Revision regular audit trail?" | "SOP-CTK-AUD-001 define revision trimestral. REG-AUD-001 muestra revisiones ejecutadas" | SOP-CTK-AUD-001, REG-AUD-001 |
| "¿Registro cambios con impacto GxP?" | "REG-CHG-001 incluye columna 'Evaluacion Impacto GxP' para cada cambio" | REG-CHG-001 |
| "¿Herramientas de testing evaluadas?" | "Si. CTK-TOOL-001 documenta evaluacion de JUnit 5, Mockito, JaCoCo como herramientas idoneas segun punto 4.7" | CTK-TOOL-001 |
| "¿Controles intercambio datos con otros sistemas?" | "CTK-STAND-001 declara que Conitrack es sistema standalone sin interfaces con otros sistemas. No hay intercambio de datos automatizado" | CTK-STAND-001 |

### Seccion 4: Preguntas Mock Audit (Repregunta Destructiva)

| Pregunta P1 | Repregunta P2 | Respuesta Blindada | Documento |
|-------------|---------------|-------------------|-----------|
| "Rol de Conitrack en stock?" | "Si refleja stock real, tiene impacto GxP" | "El alcance esta limitado en CTK-ALC-001 y SOP-CTK-USO-001. Las decisiones de despacho son externas al sistema" | CTK-ALC-001, SOP-CTK-USO-001 |
| "Reemplazo de Excel?" | "Replica riesgo historico sin control?" | "CTK-TRANS-001 documenta analisis de riesgos del proceso anterior y mejoras implementadas" | CTK-TRANS-001 |
| "Seguridad robusta?" | "Por que no igual rigor en validacion?" | "La validacion es proporcional al riesgo (GAMP 5, criticidad baja). Ver CTK-GAMP-001 y CTK-VP-001 aprobados por QA" | CTK-GAMP-001, CTK-VP-001 |
| "Usuarios probaron y funciona?" | "Donde estan resultados en OQ/PQ?" | "CTK-OQ-001 contiene 25+ casos ejecutados con evidencia. Pruebas de usuario documentadas como aceptacion" | CTK-OQ-001 |
| "Script de backup?" | "Backup no probado garantiza integridad?" | "REG-RST-001 documenta prueba de restore exitosa con fecha, procedimiento y resultado" | REG-RST-001 |
| "Audit trail registra accesos?" | "Como reconstruyo valor anterior y motivo?" | "Hibernate Envers (tablas *_AUD + revinfo) guarda: revision_type, timestamp, usuario, motivo. Los movimientos tienen motivoCambio (min 20 chars). Ver CTK-OQ-001 caso TC-031" | CTK-OQ-001, DB schema |
| "Git para cambios?" | "Donde esta procedimiento formal?" | "SOP-CTK-CHG-001 define proceso. Git es herramienta tecnica. REG-CHG-001 tiene registros formales" | SOP-CTK-CHG-001, REG-CHG-001 |
| "Sin incidentes criticos?" | "Sin registro de menores, como sabe?" | "REG-INC-001 registra TODOS los incidentes (criticos y menores). Si no hay registros, es porque no hubo incidentes" | REG-INC-001 |
| "Revision anual planeada?" | "Por que no existe evaluacion aun?" | "CTK-REV-001 es la primera evaluacion periodica documentada y firmada" | CTK-REV-001 |
| "Conoce bien el sistema?" | "Documentacion suficiente sin usted?" | "38 documentos formales permiten mantenimiento por cualquier persona calificada. Ver carpeta completa de auditoria" | Carpeta auditoria |

---

## CONTENIDO MINIMO DE CADA DOCUMENTO

### A1. Inventario de Sistemas (CTK-INV-001)

```
CONTENIDO OBLIGATORIO:
- Lista de todos los sistemas informatizados de la empresa
- Conitrack incluido con: nombre, version, criticidad, System Owner
- Clasificacion GxP de cada sistema
- Fecha de ultima validacion
- Firma: QA Director
```

### A2. Clasificacion GAMP (CTK-GAMP-001)

```
CONTENIDO OBLIGATORIO:
1. Identificacion del sistema
2. Categoria GAMP: 5 (software custom)
3. Impacto GxP: Indirecto
4. Criticidad: Baja
5. Justificacion detallada (minimo 1 pagina)
6. Conclusion
7. Firmas: System Owner, Process Owner, QA
```

### A3/A4. Designacion de Roles (CTK-ROL-001/002)

```
CONTENIDO OBLIGATORIO:
- Nombre completo de la persona designada
- Cargo
- Rol asignado (System Owner / Process Owner)
- Responsabilidades especificas (lista de 5-10 items)
- Fecha de designacion
- Firma: Direccion General
- Firma: Persona designada (aceptacion)
```

### A5. Evaluacion Proveedor Interno (CTK-PROV-001)

```
CONTENIDO OBLIGATORIO:
1. Identificacion del proveedor (socio fundador)
2. Experiencia en desarrollo (anos, proyectos)
3. Practicas de desarrollo:
   - Control de versiones (Git)
   - Metodologia de testing
   - Gestion de codigo
4. Capacidad de soporte
5. Evaluacion de infraestructura/hosting
6. Conclusion: Proveedor APROBADO
7. Firmas: QA, Direccion
```

### A6. Politica de Seguridad (CTK-SEC-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo
2. Alcance
3. Roles y permisos (matriz completa)
4. Politica de contrasenas:
   - Longitud minima: 8 caracteres
   - Complejidad: mayusculas, minusculas, numeros
   - Expiracion: configurable
5. Bloqueo de cuenta: 5 intentos, 15 minutos
6. Timeout de sesion
7. Acceso a base de datos (restringido a IT)
8. Prohibicion de usuarios compartidos
9. Firmas: SO, IT Manager, QA
```

### A7. Politica de Archivo (CTK-ARCH-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo
2. Tipos de datos a retener
3. Periodos de retencion (minimo segun regulacion)
4. Formato de almacenamiento
5. Verificacion periodica de legibilidad
6. Procedimiento ante cambio de tecnologia
7. Firmas: SO, QA
```

### A8. Alcance y Limitaciones (CTK-ALC-001)

```
CONTENIDO OBLIGATORIO:
1. Proposito del sistema
2. Funcionalidades incluidas (lista)
3. LIMITACIONES EXPLICITAS:
   - NO se usa para liberacion de lotes
   - NO reemplaza registros de QC
   - NO toma decisiones criticas de calidad
4. Proceso de liberacion de lotes (externo)
5. Firmas: SO, PO, QA
```

### A9. Analisis Transicion Excel a Conitrack (CTK-TRANS-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo del documento
2. Descripcion del proceso anterior (Excel/manual)
3. Evaluacion de riesgos del proceso anterior:
   - Riesgos identificados en Excel
   - Controles existentes (o ausentes)
   - Incidentes historicos documentados (si existieron)
4. Comparativo de riesgos Excel vs Conitrack:
   | Aspecto | Excel | Conitrack | Mejora |
   |---------|-------|-----------|--------|
   | Audit trail | NO | SI | Control total |
   | Acceso | Compartido | Individual | Trazabilidad |
   | Backup | Manual | Automatico | Disponibilidad |
5. Bugs/correcciones historicas (seccion vacia si no hubo)
6. Conclusion: Conitrack reduce riesgos del proceso anterior
7. Firmas: SO, PO, QA
```

### A10. Evaluacion Herramientas Testing (CTK-TOOL-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo
2. Herramientas evaluadas:
   - JUnit 5 (testing unitario)
   - Mockito (mocking)
   - JaCoCo (cobertura)
   - Spring Boot Test (integracion)
3. Para cada herramienta:
   - Version utilizada
   - Proposito
   - Madurez (anos en mercado, adopcion)
   - Idoneidad para uso en testing GxP
   - Referencias (documentacion oficial)
4. Conclusion: Herramientas idoneas segun punto 4.7 Anexo 6
5. Firmas: SO, QA
```

### A11. Declaracion Sistema Standalone (CTK-STAND-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo
2. Arquitectura del sistema (diagrama simple)
3. Declaracion explicita:
   - Conitrack NO tiene interfaces automaticas con otros sistemas
   - NO hay intercambio de datos electronico
   - Datos se ingresan manualmente
   - Exportaciones son archivos descargados manualmente
4. Implicancias:
   - Punto 5-6 Anexo 6 no aplica (comprobaciones intrinsecas de intercambio)
   - Sin riesgo de corrupcion por sistemas externos
5. Firmas: SO, IT, QA
```

### B1. Plan de Validacion (CTK-VP-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo
2. Alcance
3. Base normativa (ANMAT Anexo 6, GAMP 5)
4. Clasificacion del sistema
5. Roles y responsabilidades
6. Estrategia de validacion (basada en riesgo)
7. Documentos asociados (lista)
8. Actividades de validacion:
   - Risk Assessment
   - URS
   - IQ/OQ
9. Criterios de aceptacion
10. Firmas: SO, PO, QA
```

### B2. Risk Assessment (CTK-RA-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo
2. Alcance
3. Metodologia (P x S = Riesgo)
4. Matriz de riesgos (minimo 10 riesgos):
   - Modificacion no autorizada de stock
   - Eliminacion de registros
   - Datos invalidos
   - Acceso no autorizado
   - Rol excesivo
   - Caida sin backup
   - Sistema fuera de servicio
   - Uso indebido
   - Falta entrenamiento
   - Cambio sin evaluacion
5. Para cada riesgo:
   - Probabilidad
   - Severidad
   - Controles
   - Evidencia
   - Riesgo residual
6. Conclusion
7. Firmas: SO, PO, QA
```

### B3. URS (CTK-URS-001)

```
CONTENIDO OBLIGATORIO:
1. Introduccion
2. Alcance
3. Requerimientos funcionales (minimo 20):
   URS-001 a URS-020
   Cada uno con: ID, descripcion, criticidad, impacto GxP
4. Requerimientos no funcionales (minimo 10):
   URS-021 a URS-030
5. Firmas: SO, PO, QA
```

### B4. Matriz Trazabilidad (CTK-MTZ-001)

```
CONTENIDO OBLIGATORIO:
| URS | Test Case | Tipo | Resultado | Evidencia |
|-----|-----------|------|-----------|-----------|
| URS-001 | TC-001 | OQ | PASS | Screenshot |
...para CADA URS
```

### B5/B6. IQ/OQ Ejecutados (CTK-IQ-001, CTK-OQ-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo
2. Alcance
3. Prerrequisitos verificados
4. Casos de prueba (IQ: 5-10, OQ: 20-30)
5. Para cada caso:
   - ID
   - Objetivo
   - Procedimiento
   - Resultado esperado
   - Resultado obtenido
   - PASS/FAIL
   - Evidencia (screenshot)
   - Ejecutado por / Fecha
6. Resumen de resultados
7. Desviaciones (si las hay)
8. Conclusion
9. Firmas: SO, (PO para OQ), QA
```

### B7. PQ - Performance Qualification (CTK-PQ-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo
2. Alcance
3. Periodo de observacion: 2-4 semanas minimo
4. Criterios de aceptacion:
   - Sistema estable sin caidas
   - Usuarios operando sin incidentes criticos
   - Datos consistentes con proceso anterior
5. Registro de observaciones diarias/semanales
6. Incidentes durante periodo (si hubo)
7. Confirmacion del Process Owner que sistema funciona segun URS
8. Conclusion: Sistema APTO para uso en produccion
9. Firmas: SO, PO, QA
```

### B8. Reporte Validacion (CTK-VAL-001)

```
CONTENIDO OBLIGATORIO:
1. Resumen ejecutivo
2. Sistema validado: Conitrack v[X.Y.Z]
3. Documentos de validacion completados (lista con fechas)
4. Resultados de IQ: X/X PASS
5. Resultados de OQ: X/X PASS
6. Desviaciones cerradas
7. Conclusion: Sistema VALIDADO
8. Version en produccion (tag Git)
9. Firmas: SO, PO, QA
```

### C1-C6. SOPs (General)

```
CONTENIDO OBLIGATORIO CADA SOP:
1. Objetivo
2. Alcance
3. Responsabilidades
4. Definiciones
5. Procedimiento (pasos numerados)
6. Registros generados
7. Anexos (si aplica)
8. Firmas: SO, (otros segun SOP), QA
```

### C7. SOP Uso del Sistema (SOP-CTK-USO-001)

```
CONTENIDO OBLIGATORIO:
1. Objetivo
2. Alcance
3. USOS PERMITIDOS:
   - Registro de movimientos de stock
   - Consulta de lotes y trazabilidad
   - Generacion de reportes operativos
   - Gestion de analisis de calidad
4. USOS PROHIBIDOS (seccion critica):
   - NO usar para decidir que lote entregar/despachar
   - NO usar como unico criterio de liberacion
   - NO usar para registros regulatorios definitivos
   - Decisiones de despacho son EXTERNAS al sistema
5. Procedimiento de uso tipico (flujo de trabajo)
6. Responsabilidades del usuario
7. Firmas: SO, PO, QA
```

### D1-D7. Registros

```
CONTENIDO OBLIGATORIO CADA REGISTRO:
- Encabezado con codigo y version
- Tabla con columnas especificas segun tipo
- MINIMO 1 ENTRADA REAL (no puede estar vacio)
- Fechas reales
- Nombres reales
- Firmas donde corresponda
```

### E1. Evaluacion Periodica (CTK-REV-001)

```
CONTENIDO OBLIGATORIO:
1. Periodo evaluado
2. Version actual del sistema
3. Cambios en el periodo (referencia a REG-CHG)
4. Incidencias en el periodo (referencia a REG-INC)
5. Estado de backups (referencia a REG-BAK)
6. Revision de audit trail (referencia a REG-AUD)
7. Revision de usuarios (referencia a REG-USR)
8. Estado de validacion
9. Conclusion
10. Acciones recomendadas
11. Firmas: SO, PO, QA
```

### E2. Mapeo Anexo 6 (CTK-MAP-001)

```
CONTENIDO OBLIGATORIO:
Para CADA punto del Anexo 6:
| Punto | Requisito | Control Conitrack | SOP | Evidencia | Cumple |
|-------|-----------|-------------------|-----|-----------|--------|
| 1.1 | Gestion riesgos | CTK-RA-001 | - | Firmado | SI |
| 2.1 | Personal | CTK-ROL-001/002 | SOP-USR | Firmados | SI |
...TODOS los puntos
```

---

## CRONOGRAMA DE EJECUCION

### FASE 1: SEMANA 1-2 (Fundamentos)

| Dia | Actividad | Entregable | Responsable |
|-----|-----------|------------|-------------|
| 1 | Reunion kick-off, asignar roles | Acta reunion | Todos |
| 1 | Redactar CTK-ROL-001 (System Owner) | Borrador | Direccion |
| 1 | Redactar CTK-ROL-002 (Process Owner) | Borrador | Direccion |
| 2 | Firmar designaciones de roles | CTK-ROL-001/002 firmados | Direccion |
| 2 | Redactar CTK-INV-001 (Inventario) | Borrador | QA |
| 3 | Redactar CTK-GAMP-001 (Clasificacion) | Borrador | SO |
| 3 | Redactar CTK-ALC-001 (Alcance) | Borrador | SO + PO |
| 4 | Revisar y firmar CTK-INV-001 | Firmado | QA Director |
| 4 | Revisar y firmar CTK-GAMP-001 | Firmado | SO + PO + QA |
| 5 | Revisar y firmar CTK-ALC-001 | Firmado | SO + PO + QA |
| 5 | Redactar CTK-PROV-001 | Borrador | QA |
| 6 | Redactar CTK-SEC-001 | Borrador | SO + IT |
| 7 | Revisar y firmar CTK-PROV-001 | Firmado | QA + Direccion |
| 7 | Revisar y firmar CTK-SEC-001 | Firmado | SO + IT + QA |
| 8 | Redactar CTK-ARCH-001 | Borrador | SO |
| 8 | Redactar CTK-VP-001 (Plan Validacion) | Borrador | SO |
| 9 | Revisar y firmar CTK-ARCH-001 | Firmado | SO + QA |
| 9 | Revisar y firmar CTK-VP-001 | Firmado | SO + PO + QA |
| 10 | Buffer / ajustes | - | - |

**Checkpoint Semana 2:** 9 documentos de gobierno firmados

### FASE 2: SEMANA 3-4 (Validacion)

| Dia | Actividad | Entregable | Responsable |
|-----|-----------|------------|-------------|
| 11 | Redactar CTK-RA-001 (Risk Assessment) | Borrador | SO |
| 12 | Revisar CTK-RA-001 | Revisado | PO + QA |
| 13 | Firmar CTK-RA-001 | Firmado | SO + PO + QA |
| 13 | Redactar CTK-URS-001 (30 requerimientos) | Borrador | SO + PO |
| 14 | Revisar CTK-URS-001 | Revisado | QA |
| 15 | Firmar CTK-URS-001 | Firmado | SO + PO + QA |
| 16 | Redactar CTK-IQ-001 (protocolo) | Borrador | SO |
| 16 | Redactar CTK-OQ-001 (protocolo, 25 casos) | Borrador | SO |
| 17 | Revisar protocolos IQ/OQ | Revisado | QA |
| 18 | Ejecutar IQ (5-10 verificaciones) | Evidencias | SO |
| 19 | Ejecutar OQ parte 1 (casos 1-15) | Evidencias | SO |
| 20 | Ejecutar OQ parte 2 (casos 16-25) | Evidencias | SO |

**Checkpoint Semana 4:** Risk Assessment + URS + IQ/OQ ejecutados

### FASE 3: SEMANA 5-6 (SOPs y Registros)

| Dia | Actividad | Entregable | Responsable |
|-----|-----------|------------|-------------|
| 21 | Firmar CTK-IQ-001 ejecutado | Firmado | SO + QA |
| 21 | Firmar CTK-OQ-001 ejecutado | Firmado | SO + PO + QA |
| 22 | Redactar CTK-MTZ-001 (matriz) | Completo | SO |
| 22 | Firmar CTK-MTZ-001 | Firmado | SO + QA |
| 23 | Completar SOP-CTK-BAK-001 | Borrador | SO + IT |
| 23 | Completar SOP-CTK-USR-001 | Borrador | SO |
| 24 | Completar SOP-CTK-CHG-001 | Borrador | SO |
| 24 | Completar SOP-CTK-INC-001 | Borrador | SO |
| 25 | Completar SOP-CTK-CON-001 | Borrador | SO + PO |
| 25 | Completar SOP-CTK-AUD-001 | Borrador | SO |
| 26 | Revisar todos los SOPs | Revisados | QA |
| 27 | Firmar todos los SOPs (6) | Firmados | SO + QA |
| 28 | EJECUTAR PRUEBA DE RESTORE | REG-RST-001 | IT |
| 29 | Iniciar REG-USR-001 (lista usuarios) | Activo | SO |
| 30 | Documentar 1 cambio en REG-CHG-001 | 1 entrada | SO |

**Checkpoint Semana 6:** 6 SOPs firmados + Restore probado + Registros iniciados

### FASE 4: SEMANA 7-8 (Cierre y Evaluacion)

| Dia | Actividad | Entregable | Responsable |
|-----|-----------|------------|-------------|
| 31 | Documentar 1 incidencia en REG-INC-001 | 1 entrada | SO |
| 31 | Ejecutar revision audit trail | REG-AUD-001 | SO + QA |
| 32 | Documentar entrenamientos | REG-TRN-001 | PO |
| 32 | Verificar backups ejecutados | REG-BAK-001 | IT |
| 33 | Redactar CTK-REV-001 (Eval Periodica) | Borrador | SO |
| 34 | Revisar CTK-REV-001 | Revisado | PO + QA |
| 35 | Firmar CTK-REV-001 | Firmado | SO + PO + QA |
| 36 | Completar CTK-MAP-001 (Mapeo Anexo 6) | Borrador | SO |
| 37 | Revisar y firmar CTK-MAP-001 | Firmado | SO + QA |
| 38 | Redactar CTK-VAL-001 (Reporte final) | Borrador | SO |
| 39 | Revisar CTK-VAL-001 | Revisado | PO + QA |
| 40 | Firmar CTK-VAL-001 | Firmado | SO + PO + QA |

**Checkpoint Semana 8:** TODO COMPLETO

### FASE 5: SEMANA 9-10 (Preparacion Auditoria)

| Dia | Actividad | Entregable | Responsable |
|-----|-----------|------------|-------------|
| 41-42 | Compilar carpeta de auditoria | Carpeta fisica/digital | SO |
| 43 | Redactar CTK-AUD-RESP (Guia respuestas) | Documento | SO + QA |
| 44 | Completar CTK-MAN-001 (Manual usuario) | Documento | SO |
| 45 | Completar CTK-CHK-001 (Checklist) | Documento | QA |
| 46-47 | Mock audit interna | Informe | QA + Externo |
| 48-49 | Corregir hallazgos de mock audit | Documentos actualizados | SO |
| 50 | Verificacion final pre-auditoria | Checklist 100% | QA |

---

## CHECKLIST PRE-AUDITORIA (CTK-CHK-001)

### Documentos de Gobierno (11)
- [ ] CTK-INV-001 firmado y actualizado
- [ ] CTK-GAMP-001 firmado
- [ ] CTK-ROL-001 firmado (System Owner)
- [ ] CTK-ROL-002 firmado (Process Owner)
- [ ] CTK-PROV-001 firmado
- [ ] CTK-SEC-001 firmado
- [ ] CTK-ARCH-001 firmado
- [ ] CTK-ALC-001 firmado
- [ ] CTK-TRANS-001 firmado (Transicion Excel)
- [ ] CTK-TOOL-001 firmado (Herramientas Testing)
- [ ] CTK-STAND-001 firmado (Sistema Standalone)

### Documentos de Validacion (8)
- [ ] CTK-VP-001 firmado
- [ ] CTK-RA-001 firmado con 10+ riesgos
- [ ] CTK-URS-001 firmado con 30+ requerimientos
- [ ] CTK-MTZ-001 firmado con trazabilidad completa
- [ ] CTK-IQ-001 ejecutado y firmado
- [ ] CTK-OQ-001 ejecutado y firmado con 25+ casos PASS
- [ ] CTK-PQ-001 ejecutado y firmado (periodo observacion)
- [ ] CTK-VAL-001 firmado con conclusion VALIDADO

### SOPs (7)
- [ ] SOP-CTK-BAK-001 firmado
- [ ] SOP-CTK-USR-001 firmado
- [ ] SOP-CTK-CHG-001 firmado
- [ ] SOP-CTK-INC-001 firmado
- [ ] SOP-CTK-CON-001 firmado
- [ ] SOP-CTK-AUD-001 firmado
- [ ] SOP-CTK-USO-001 firmado (Uso del Sistema)

### Registros
- [ ] REG-USR-001 con lista actualizada de usuarios
- [ ] REG-CHG-001 con minimo 1 cambio documentado
- [ ] REG-INC-001 con minimo 1 incidencia documentada
- [ ] REG-BAK-001 con logs de backups
- [ ] REG-RST-001 con prueba de restore EXITOSA
- [ ] REG-AUD-001 con minimo 1 revision ejecutada
- [ ] REG-TRN-001 con entrenamientos documentados

### Evaluaciones
- [ ] CTK-REV-001 (Evaluacion Periodica) firmada
- [ ] CTK-MAP-001 (Mapeo Anexo 6) firmado y completo

### Preparacion Auditoria
- [ ] Carpeta de auditoria organizada
- [ ] CTK-AUD-RESP (Guia respuestas) preparada
- [ ] CTK-MAN-001 (Manual usuario) completo
- [ ] Mock audit ejecutada sin hallazgos criticos
- [ ] Equipo entrenado en respuestas

---

## CRITERIOS DE EXITO ABSOLUTOS

### Para declarar "LISTO PARA AUDITORIA":

1. **39/39 entregables completos** - Sin excepciones
2. **Todas las firmas obtenidas** - SO, PO, QA donde corresponda
3. **Todos los registros con minimo 1 entrada** - No pueden estar vacios
4. **Prueba de restore documentada y EXITOSA** - Obligatorio
5. **Mock audit pasada** - Sin hallazgos criticos abiertos
6. **Mapeo Anexo 6 100% cubierto** - Cada punto con control y evidencia
7. **Equipo puede responder CUALQUIER pregunta** con documento de soporte

### Metricas de Verificacion

| Metrica | Valor Requerido | Verificacion |
|---------|-----------------|--------------|
| Documentos firmados | 39/39 | Checklist |
| Registros con entradas | 7/7 | Revision |
| Puntos Anexo 6 cubiertos | 17/17 | CTK-MAP-001 |
| Test cases OQ PASS | 100% | CTK-OQ-001 |
| Restore probado | SI | REG-RST-001 |
| Mock audit | APROBADA | Informe |

---

## CARPETA DE AUDITORIA

### Estructura Fisica/Digital

```
CARPETA_AUDITORIA_CONITRACK/
├── 01_GOBIERNO/
│   ├── CTK-INV-001_Inventario_Sistemas.pdf
│   ├── CTK-GAMP-001_Clasificacion.pdf
│   ├── CTK-ROL-001_System_Owner.pdf
│   ├── CTK-ROL-002_Process_Owner.pdf
│   ├── CTK-PROV-001_Evaluacion_Proveedor.pdf
│   ├── CTK-SEC-001_Politica_Seguridad.pdf
│   ├── CTK-ARCH-001_Politica_Archivo.pdf
│   ├── CTK-ALC-001_Alcance_Limitaciones.pdf
│   ├── CTK-TRANS-001_Transicion_Excel.pdf
│   ├── CTK-TOOL-001_Herramientas_Testing.pdf
│   └── CTK-STAND-001_Sistema_Standalone.pdf
├── 02_VALIDACION/
│   ├── CTK-VP-001_Plan_Validacion.pdf
│   ├── CTK-RA-001_Risk_Assessment.pdf
│   ├── CTK-URS-001_Especificaciones.pdf
│   ├── CTK-MTZ-001_Matriz_Trazabilidad.pdf
│   ├── CTK-IQ-001_Installation_Qualification.pdf
│   ├── CTK-OQ-001_Operational_Qualification.pdf
│   ├── CTK-PQ-001_Performance_Qualification.pdf
│   └── CTK-VAL-001_Reporte_Validacion.pdf
├── 03_SOPS/
│   ├── SOP-CTK-BAK-001_Backup_Restore.pdf
│   ├── SOP-CTK-USR-001_Gestion_Usuarios.pdf
│   ├── SOP-CTK-CHG-001_Gestion_Cambios.pdf
│   ├── SOP-CTK-INC-001_Gestion_Incidencias.pdf
│   ├── SOP-CTK-CON-001_Contingencia.pdf
│   ├── SOP-CTK-AUD-001_Revision_Audit_Trail.pdf
│   └── SOP-CTK-USO-001_Uso_Sistema.pdf
├── 04_REGISTROS/
│   ├── REG-USR-001_Usuarios.xlsx
│   ├── REG-CHG-001_Cambios.xlsx
│   ├── REG-INC-001_Incidencias.xlsx
│   ├── REG-BAK-001_Backups.xlsx
│   ├── REG-RST-001_Restore.pdf
│   ├── REG-AUD-001_Revision_Audit.xlsx
│   └── REG-TRN-001_Entrenamientos.xlsx
├── 05_EVALUACIONES/
│   ├── CTK-REV-001_Evaluacion_Periodica.pdf
│   └── CTK-MAP-001_Mapeo_Anexo6.pdf
└── 06_SOPORTE/
    ├── CTK-MAN-001_Manual_Usuario.pdf
    ├── CTK-AUD-RESP_Guia_Respuestas.pdf
    └── CTK-CHK-001_Checklist.pdf
```

---

## GARANTIA DE APROBACION

### Por que este plan garantiza aprobar:

1. **Cubre el 100% de los requisitos del Anexo 6** - Mapeo explicito
2. **Responde el 100% de las preguntas del banco hostil** - Todas tienen documento soporte
3. **Implementa los 5 principios de auditoria** - Documentado, firmado, trazado, evidenciado, registrado
4. **Incluye mock audit** - Los problemas se detectan y corrigen antes
5. **No deja nada a la improvisacion** - Guia de respuestas preparada
6. **Demuestra madurez del sistema** - Evaluacion periodica completada
7. **Evidencia controles funcionando** - Registros con entradas reales

### Respuesta a la pregunta mas dificil:

**Auditor:** "Si usted no estuviera, ¿existe suficiente documentacion para mantener el sistema en estado validado sin depender de su memoria?"

**Respuesta blindada:** "Si. Tenemos 39 documentos formales que describen completamente el sistema, su validacion, operacion y mantenimiento. Ver carpeta de auditoria con: Plan de Validacion, URS, IQ/OQ/PQ, 7 SOPs operativos, Manual de Usuario, y Guia de Respuestas. Cualquier persona calificada puede asumir la responsabilidad con esta documentacion."

---

## FIRMAS DE COMPROMISO

| Rol | Nombre | Firma | Fecha Compromiso |
|-----|--------|-------|------------------|
| System Owner | | | |
| Process Owner | | | |
| QA | | | |
| IT | | | |
| Direccion General | | | |

**Compromiso:** Nos comprometemos a completar TODOS los entregables de este plan antes de la fecha de auditoria, sin excepciones.

---

**Documento creado:** 2025-12-09
**Revision:** Semanal hasta completar
**Proxima revision:** [Fecha]
