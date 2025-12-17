# Conitrack – Mapeo de requisitos ANMAT Anexo 6 a controles del sistema

Fecha: 2025-12-07
Base normativa: Anexo 6 – Sistemas Informatizados (ANMAT)

Este documento vincula puntos clave del Anexo 6 con controles concretos de Conitrack y evidencias esperadas.

---

## 1. Gestión de riesgos (punto 1.1)

Requisito ANMAT:
- La gestión de riesgos debe aplicarse durante el ciclo de vida del sistema, considerando seguridad del paciente, integridad de datos y calidad del producto.

Implementación Conitrack:
- Documento “Risk Assessment – Conitrack”.
- Matriz de riesgos con impacto en integridad de datos y disponibilidad.
- Riesgos → Controles → Evidencias.

Evidencia:
- Risk Assessment firmado por System Owner, Process Owner y QA.

---

## 2. Personal (punto 2.1)

Requisito ANMAT:
- Definir responsabilidades de process owner, system owner y personal relevante.

Implementación Conitrack:
- Designación formal de:
    - System Owner (ej. socio fundador desarrollador).
    - Process Owner (responsable de stock).
- Registros de entrenamiento de usuarios.

Evidencia:
- Documento de designación de roles.
- Registros de entrenamiento.

---

## 3. Proveedores (puntos 3.1–3.5)

Requisito ANMAT:
- Evaluación del proveedor, acuerdos formales y calidad del software.

Implementación Conitrack:
- Proveedor interno (socio fundador).
- Documento de evaluación de proveedor interno:
    - experiencia,
    - prácticas de desarrollo,
    - control de versiones,
    - gestión de incidencias.

Evidencia:
- Evaluación del proveedor interno.
- Documentos que muestren buenas prácticas de desarrollo (Git, revisiones, etc.).

---

## 4. Validación (puntos 4.1–4.9)

Requisito ANMAT:
- Documentación de validación que cubra ciclo de vida.
- URS basadas en evaluación de riesgos.
- Trazabilidad de requerimientos.
- Evidencia de testing adecuado (límites, errores, migraciones si las hay).

Implementación Conitrack:
- Plan de Validación.
- URS.
- Matriz URS → Test.
- IQ/OQ/PQ.
- (Si hubo migración de datos desde Excel: protocolo de migración y resultados).

Evidencia:
- Plan de Validación aprobado.
- URS y matriz de trazabilidad.
- Protocolos y evidencias IQ/OQ/PQ.
- Reporte de validación final.

---

## 5. Datos (punto 5) y comprobaciones de exactitud (punto 6)

Requisito ANMAT:
- Comprobaciones intrínsecas de entrada y procesamiento de datos.
- Comprobación adicional de exactitud para datos críticos.

Implementación Conitrack:
- Validaciones de entrada (framework de validación y validadores propios).
- Constraints en DB.
- Reglas de negocio para cantidades, fechas, estados.
- Procedimientos de revisión adicionales si se considera necesario.

Evidencia:
- Esquema de base de datos.
- Código de validadores.
- Casos de prueba de validación de datos.

---

## 6. Archivo de datos y backups (puntos 7.1–7.2)

Requisito ANMAT:
- Protección frente a daños.
- Copias de seguridad regulares.
- Verificación de integridad y recuperabilidad.

Implementación Conitrack:
- SOP de backup y restore.
- Backup automatizado (ej. cron).
- Prueba de restore periódica.

Evidencia:
- SOP de backup/restore.
- Logs de backup.
- Informe de prueba de restore con resultado satisfactorio.

---

## 7. Impresiones (punto 8)

Requisito ANMAT:
- Capacidad de obtener copias impresas claras.
- Impresiones que reflejen datos cambiados, si se usan para liberación.

Implementación Conitrack:
- Reportes imprimibles de stock, lotes y movimientos.
- Si los reportes se usan solo como soporte operativo (no para liberación de lotes), se deja constancia en el alcance.

Evidencia:
- Ejemplos de reportes impresos.
- Manual de usuario con sección de reportes.

---

## 8. Registro de auditoría (punto 9)

Requisito ANMAT:
- Registro de todos los cambios y eliminaciones relevantes, incluyendo motivo cuando aplica.
- Revisión regular del audit trail.

Implementación Conitrack:
- Audit trail de accesos (login, logout, intentos fallidos).
- Audit trail de cambios críticos en stock (usuario, fecha, antes/después, motivo si aplica).
- Procedimiento de revisión periódica del audit trail.

Evidencia:
- Tabla de auditoría en DB y su definición.
- Extractos de logs.
- Registro de revisiones periódicas del audit trail.

---

## 9. Gestión de cambios y configuración (punto 10)

Requisito ANMAT:
- Cambios solo según procedimiento definido.
- Evaluación de impacto y aprobación.

Implementación Conitrack:
- SOP de Gestión de Cambios.
- Registro de cambios (formulario o planilla).
- Uso de Git como evidencia del código cambiante.
- Evaluación de impacto GxP documentada en cambios mayores.

Evidencia:
- SOP de cambios.
- Ejemplos de registros de cambios.
- Referencias a commits etiquetados.

---

## 10. Evaluación periódica (punto 11)

Requisito ANMAT:
- Evaluaciones periódicas para confirmar que el sistema se mantiene válido y conforme.

Implementación Conitrack:
- Informe anual de Evaluación Periódica:
    - funcionalidades,
    - incidentes,
    - cambios,
    - estado de validación.

Evidencia:
- “Informe de Evaluación Periódica – Año XXXX” firmado.

---

## 11. Seguridad (puntos 12.1–12.4)

Requisito ANMAT:
- Controles físicos y lógicos.
- Registro de creación y cancelación de accesos.
- Registro de identidad de los operadores.

Implementación Conitrack:
- Política de seguridad de Conitrack.
- Matriz de roles y permisos.
- Procedimiento de alta/baja/modificación de usuarios.
- Logs de login con identificación de usuario.

Evidencia:
- Documento de política de seguridad.
- Registro de usuarios.
- Extracto de logs de login.

---

## 12. Gestión de incidencias (punto 13)

Requisito ANMAT:
- Registro y evaluación de todos los incidentes.
- Identificación de causa raíz y CAPA para incidentes críticos.

Implementación Conitrack:
- SOP de gestión de incidencias.
- Registro de incidencias (planilla simple).
- Clasificación por impacto.
- CAPA para incidentes relevantes.

Evidencia:
- Registro de incidencias con al menos un ejemplo completo.
- Documentos de CAPA cuando corresponda.

---

## 13. Firma electrónica (punto 14) y liberación de lotes (punto 15)

Requisito ANMAT:
- Firma electrónica equivalente a manuscrita para registros críticos.
- Control de liberación de lotes por Personas Cualificadas.

Implementación Conitrack:
- El sistema no se usa para liberación de lotes ni registros equivalentes a firma manuscrita.
- Esto se declara explícitamente en el alcance y en los SOPs.
- Si en el futuro se usa para acciones equivalentes a liberación, se revisará la necesidad de firma electrónica.

Evidencia:
- SOP de uso de Conitrack donde se limita su rol.
- Documentación de proceso de liberación de lotes (externo al sistema).

---

## 14. Continuidad del negocio (punto 16)

Requisito ANMAT:
- Medidas para asegurar continuidad, con sistemas alternativos o manuales.
- Tiempo de puesta en marcha basado en riesgo.

Implementación Conitrack:
- Procedimiento de contingencia:
    - registro manual de movimientos en caso de caída.
    - reconciliación posterior a la restauración.

Evidencia:
- SOP de contingencia.
- Ejemplo (si existió) de uso del procedimiento.

---

## 15. Archivo (punto 17)

Requisito ANMAT:
- Asegurar accesibilidad, legibilidad e integridad de datos archivados.
- Garantizar recuperación ante cambios de hardware/software.

Implementación Conitrack:
- Plan de archivo y exportación de datos (ej. export a formatos estándar).
- Verificación periódica de legibilidad.

Evidencia:
- Documento de política de archivo.
- Pruebas de lectura de datos históricos, si se han realizado.

---

## 16. Resumen

El objetivo de este mapeo es poder responder, ante cada requerimiento del Anexo 6:

- Cómo se trata en Conitrack (técnica o procedimentalmente),
- Dónde está la evidencia,
- Y cómo eso es coherente con un sistema de impacto GxP indirecto y criticidad baja, validado con un enfoque proporcional al riesgo.
