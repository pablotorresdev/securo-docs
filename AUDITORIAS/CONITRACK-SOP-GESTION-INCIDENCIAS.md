# SOP – Gestión de Incidencias de Conitrack

Código: SOP-CTK-INC-001  
Versión: 1.0  
Fecha: 2025-12-07

---

## 1. Objetivo

Describir el procedimiento para registrar, evaluar, investigar y cerrar incidencias relacionadas con el sistema Conitrack, asegurando que los problemas que afecten o puedan afectar procesos GxP se gestionen de manera adecuada.

---

## 2. Alcance

Aplica a:

- Incidencias técnicas (errores de sistema, fallas de performance).
- Incidencias de datos (errores de stock, datos inconsistentes).
- Incidencias de seguridad (accesos no autorizados, intentos sospechosos).
- Cualquier problema que afecte la disponibilidad o integridad de la información de Conitrack.

---

## 3. Responsabilidades

- Usuario:
    - Informar cualquier incidencia detectada.
    - Proporcionar detalle suficiente para su análisis.

- System Owner:
    - Coordinar el análisis de incidencias.
    - Definir prioridades y acciones.

- Process Owner:
    - Evaluar impacto del incidente sobre el proceso de stock.
    - Participar en la definición de acciones correctivas cuando aplique.

- QA:
    - Seguir incidencias críticas.
    - Verificar que las acciones correctivas son adecuadas.

---

## 4. Definiciones

- Incidencia: Evento no deseado que afecta o puede afectar la operación normal de Conitrack.
- Incidencia crítica: Aquel evento que impacta o puede impactar la integridad de datos GxP o la disponibilidad de información necesaria.
- CAPA: Acción correctiva y/o preventiva.

---

## 5. Registro de incidencias

Todas las incidencias deben registrarse en un Registro de Incidencias que contenga como mínimo:

- ID de incidencia.
- Fecha y hora de detección.
- Reportante.
- Descripción.
- Tipo de incidencia (técnica, datos, seguridad, etc.).
- Severidad (Baja / Media / Alta).
- Estado (Abierta / En análisis / Cerrada).
- Acciones tomadas.

Un simple archivo (por ejemplo, planilla o tabla en un MD) es aceptable, siempre que se mantenga actualizado y se conserven los registros.

---

## 6. Clasificación de incidencias

Severidad sugerida:

- Alta:
    - Pérdida de datos de stock.
    - Inconsistencias masivas.
    - Caída prolongada del sistema.
    - Acceso no autorizado con impacto potencial en integridad de datos.

- Media:
    - Errores funcionales que no comprometen datos críticos.
    - Problemas de performance moderados.

- Baja:
    - Errores cosméticos.
    - Inconvenientes menores en la interfaz.

---

## 7. Flujo de gestión de incidencias

1. Detección:
    - Un usuario o el sistema detectan una incidencia.

2. Registro:
    - Registrar la incidencia en el Registro de Incidencias.

3. Clasificación:
    - System Owner y/o Process Owner clasifican severidad y tipo.

4. Análisis:
    - Investigar causa raíz.
    - Revisar logs, audit trail, DB según corresponda.

5. Acción:
    - Definir acción correctiva.
    - Definir si se requiere acción preventiva.

6. Verificación:
    - Confirmar que la incidencia ha sido resuelta.
    - Verificar que no existan efectos colaterales.

7. Cierre:
    - System Owner/Process Owner cierran la incidencia en el registro.
    - En incidencias críticas, QA revisa y firma el cierre.

---

## 8. Incidencias críticas y CAPA

Para incidencias de severidad Alta:

1. Documentar causa raíz de forma explícita.
2. Definir CAPA:
    - Acción correctiva: qué se hizo para resolver el problema actual.
    - Acción preventiva: qué se hará para evitar recurrencia (ej. cambios de código, mejoras de proceso, entrenamiento).

3. QA revisa y aprueba la CAPA.

---

## 9. Relación con Gestión de Cambios

Si la solución a la incidencia implica cambio de software, configuración o infraestructura:

- Debe gestionarse a través del SOP de Gestión de Cambios.
- Debe evaluarse impacto GxP y, si aplica, revalidación parcial.

---

## 10. Registros generados

- Registro de Incidencias (histórico).
- Documentos de análisis de causa raíz y CAPA.
- Evidencias de pruebas posteriores a la corrección.

---

## 11. Aprobaciones

- System Owner: [Nombre, firma, fecha]
- Process Owner: [Nombre, firma, fecha]
- QA: [Nombre, firma, fecha]
