# Conitrack – Guía global de cumplimiento ANMAT (Anexo 6)

Fecha: 2025-12-07
Sistema: Conitrack – Sistema de Gestión de Stock
Desarrollador: Socio fundador de la empresa (desarrollo in-house)
Ámbito: Sistema de stock farmacéutico que reemplaza planilla Excel

---

## 1. Principios básicos para aprobar la auditoría

1) Si no está documentado, no existe.
2) Si no está aprobado, no está bajo control.
3) Si no está trazado al Anexo 6, está incompleto.
4) La validación y los controles deben ser proporcionales al impacto GxP, pero nunca inexistentes.
5) El hecho de que el sistema sea desarrollado por un socio fundador es una ventaja, siempre que el rol de “proveedor interno” esté documentado.

---

## 2. Rol del sistema Conitrack

Conitrack es:

- Un sistema informatizado de soporte a la gestión de stock.
- Sustituye un proceso manual previo basado en planilla Excel.
- No libera lotes ni decide directamente sobre la calidad del producto.
- Tiene impacto GxP indirecto, porque sus datos pueden influir en la logística y trazabilidad del producto.

Conitrack no es:

- Un ERP completo.
- Un sistema de gestión de producción.
- Un sistema de QC ni de laboratorio.
- El único origen de verdad para decisiones de liberación de lote.

---

## 3. Clasificación propuesta

- GAMP 5: Categoría 5 (software custom / desarrollo in-house).
- Impacto GxP: indirecto, sistema de soporte.
- Criticidad: baja (dentro del universo GxP), justificada por:
    - No interviene directamente en la liberación de lotes.
    - No gestiona resultados de QC.
    - Reemplaza una herramienta previa (Excel) con menor control.
    - El desarrollador es interno y hay control completo del ciclo de vida.

Esta clasificación permite aplicar el Anexo 6 con enfoque proporcional, no para eliminar requisitos, sino para simplificarlos de acuerdo al riesgo.

---

## 4. Documentos mínimos necesarios para auditoría

Listado de documentos que deben existir y estar aprobados:

1. Clasificación GAMP y Criticidad
    - Documento: “Clasificación de Sistemas Informatizados – Conitrack”
    - Contenido: categoría GAMP, impacto GxP, criticidad, justificación, firmas.

2. Risk Assessment – Conitrack
    - Matriz de riesgos del sistema informatizado.
    - Impactos: integridad de datos, disponibilidad, seguridad, errores de stock.
    - Controles y evidencias vinculadas.

3. Designación de roles
    - System Owner (responsable del sistema).
    - Process Owner (responsable del proceso de stock).
    - Ambos designados por escrito.

4. Evaluación de proveedor interno
    - Documento que describe al desarrollador (socio fundador) como “proveedor interno”.
    - Experiencia, prácticas de desarrollo, uso de Git, control de cambios, etc.

5. URS – Especificaciones de Requerimientos del Usuario
    - 20–30 requerimientos funcionales y no funcionales.
    - Marcados con criticidad / impacto GxP.

6. Plan de Validación (CSV Plan)
    - Objetivo, alcance, roles, estrategia de validación basada en riesgos.
    - Referencias a Anexo 6, GAMP 5 y documentos relacionados.

7. Matriz de trazabilidad URS → Test
    - Tabla URS → Caso de prueba → Evidencia → Resultado.

8. IQ – Installation Qualification
    - Verificación del entorno de producción (SO, Java, DB, configuración).
    - Evidencias con capturas / comandos.

9. OQ – Operational Qualification
    - Casos de prueba de flujos críticos (stock, movimientos, roles, seguridad, audit trail, backup/restore).
    - Resultados y desviaciones.

10. PQ – Performance Qualification
    - Evidencia de uso normal del sistema durante un período definido.
    - Confirmación del Process Owner de que el sistema funciona según URS.

11. SOP de Backup y Restore
    - Procedimiento documentado.
    - Frecuencia y responsable.
    - Evidencia de al menos una restauración probada.

12. Audit trail
    - Implementación técnica:
        - accesos,
        - cambios críticos de stock (usuario, antes/después, timestamp).
    - Procedimiento de revisión periódica del audit trail.

13. SOP de Gestión de Cambios
    - Definición de cambios mayores y menores.
    - Evaluación de impacto GxP.
    - Aprobación previa.
    - Vinculación con Git como evidencia técnica de código.

14. Registro de Cambios
    - Lista de cambios relevantes del último año.
    - Referencia a issues o tickets, merges, etc.

15. Gestión de Incidencias
    - Registro de incidencias del sistema (al menos uno real).
    - Causa raíz, acción correctiva, verificación.

16. Evaluación Periódica del Sistema
    - Informe anual.
    - Funcionalidades actuales, cambios, incidentes, estado de validación, conclusiones.

17. Política de Seguridad de Conitrack
    - Roles, permisos, passwords, bloqueo, expiración, acceso a DB y servidores.

18. Procedimiento de Contingencia
    - Qué hacer si Conitrack cae.
    - Uso de registros manuales.
    - Reconciliación posterior.

19. Análisis de archivo y legibilidad futura
    - Cómo se asegura acceso, legibilidad e integridad a largo plazo.

20. Mapeo Anexo 6 → Controles Conitrack
    - Tabla que vincule cada punto relevante del Anexo 6 con:
        - controles técnicos,
        - SOPs,
        - evidencias.

---

## 5. Ventaja del desarrollo in-house (socio fundador)

El hecho de que el sistema sea desarrollado por uno de los socios fundadores de la empresa debe presentarse como:

- Reducción de riesgo de proveedor: no hay tercero desconocido.
- Máximo control sobre el código fuente y la arquitectura.
- Capacidad de respuesta rápida ante incidencias y hallazgos de auditoría.
- Conocimiento profundo del proceso de negocio.

Cuidado: el fundador puede ser System Owner, pero idealmente no debería ser a la vez Process Owner. Hay que separar responsabilidades.

---

## 6. Estrategia de defensa durante la auditoría

Durante la auditoría, toda respuesta debe:

1) Apoyarse en un documento.
2) Enlazar ese documento con un requisito del Anexo 6.
3) Recordar que el impacto GxP es indirecto y la criticidad baja, pero reconocida.
4) Mostrar que:
    - hay controles reales,
    - hay validación proporcional,
    - hay mejora evidente respecto al Excel previo.

Ejemplo de respuesta tipo:

“Sí, tenemos eso documentado en el Plan de Validación y en el Risk Assessment. El sistema se clasifica como GAMP 5, impacto GxP indirecto y criticidad baja; por eso aplicamos una validación proporcional, que incluye URS, IQ/OQ, backup y restore probado, audit trail de accesos y cambios críticos, y una evaluación periódica anual. Todo está vinculado al Anexo 6 en el mapeo de requisitos。”
