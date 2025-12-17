# Conitrack – Evaluación Periódica del Sistema

Código: CTK-REV-PER-001  
Versión: 1.0  
Periodo evaluado: [Año / rango de fechas]  
Fecha del informe: [Fecha]

---

## 1. Objetivo

Realizar una evaluación periódica de Conitrack para confirmar que:

- sigue cumpliendo con los requerimientos de usuario,
- mantiene su estado validado,
- y continúa siendo adecuado para el proceso de stock al que da soporte.

---

## 2. Alcance

Incluye:

- Revisión de funcionalidades actuales.
- Revisión de cambios implementados en el período.
- Revisión de incidencias registradas.
- Estado de backup/restore.
- Estado del audit trail.
- Revisión de gestión de accesos.
- Revisión de documentación de validación.

---

## 3. Descripción del sistema y uso actual

Resumen:

- Versión actual de Conitrack en producción.
- Principales módulos en uso.
- Cambios significativos desde la última evaluación (si existió).

Ejemplo:
- Versión: 1.3.0
- Nuevo módulo: consulta avanzada por vencimientos.
- Eliminado: reporte legacy no usado.

---

## 4. Cambios implementados en el período

Tabla de resumen (basada en el Registro de Cambios):

| ID Cambio | Fecha | Tipo (menor/mayor) | Descripción corta | Test/Revalidación realizada | Observaciones |
|----------|-------|--------------------|-------------------|-----------------------------|---------------|
| CHG-001  | ...   | Mayor              | ...               | OQ parcial, regresión básica|               |

Conclusión de este apartado:
- Confirmar si los cambios se han gestionado según el SOP de Gestión de Cambios.
- Indicar si alguno de los cambios requiere actualización de documentación de validación.

---

## 5. Incidencias registradas

Resumen del Registro de Incidencias:

| ID Incidencia | Fecha | Severidad | Descripción | CAPA aplicada | Estado |
|---------------|-------|-----------|-------------|---------------|--------|
| INC-001       | ...   | Media     | ...         | ...           | Cerrada|

Conclusión:
- Evaluar si las incidencias indican problemas recurrentes.
- Evaluar si hubo incidencias con impacto potencial en integridad de datos.

---

## 6. Backup y restore

Revisión:

- Frecuencia de backups (¿se cumplieron las ejecuciones previstas?).
- Revisar logs de backup.
- Confirmar si se realizó al menos una prueba de restore en el período.

Conclusión:
- Indicar si el sistema mantiene la capacidad de recuperación adecuada.
- Señalar cualquier hallazgo o recomendación.

---

## 7. Audit trail

Revisión:

- Confirmar que el audit trail está activo.
- Revisar una muestra de registros:
    - accesos,
    - cambios críticos en stock (si aplica).
- Verificar que se haya realizado revisión periódica del audit trail (si se definió así en el SOP).

Conclusión:
- Indicar si se detectaron anomalías.
- Indicar si el uso del audit trail es suficiente para trazabilidad GxP.

---

## 8. Gestión de usuarios y accesos

Revisión:

- Listado actual de usuarios y roles.
- Revisión de cuentas inactivas o innecesarias.
- Verificar que las altas/bajas/modificaciones estén documentadas según SOP de usuarios.

Conclusión:
- Indicar si la asignación de roles es coherente con las funciones.
- Registrar cualquier ajuste recomendado.

---

## 9. Estado de validación

Revisión de documentación:

- Plan de Validación.
- URS y matriz URS → Test.
- IQ/OQ/PQ y evidencias.
- Reporte de validación final (si ya existe).

Conclusión:

- Confirmar si el sistema sigue siendo consistente con las URS.
- Confirmar si los cambios realizados han sido adecuadamente revalidados.
- Indicar si es necesario un esfuerzo adicional de validación.

---

## 10. Conclusión de la evaluación

Conclusión global (ejemplos):

- “Conitrack se considera en estado validado y adecuado para el uso previsto durante el período evaluado, sin hallazgos críticos.”
- “Conitrack se considera en estado aceptable, pero se identifican las siguientes acciones pendientes para mantener el estado validado.”

Listado de acciones recomendadas (si corresponde):

- Completar prueba de restore formal.
- Ampliar audit trail a ciertos tipos de cambios.
- Actualizar URS con nuevas funcionalidades.

---

## 11. Aprobaciones

- System Owner: [Nombre, firma, fecha]
- Process Owner: [Nombre, firma, fecha]
- QA: [Nombre, firma, fecha]
