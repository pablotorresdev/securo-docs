# SOP – Gestión de Cambios de Conitrack

Código: SOP-CTK-CHG-001  
Versión: 1.0  
Fecha: 2025-12-07

---

## 1. Objetivo

Definir el proceso para gestionar cambios en Conitrack (software, configuración, infraestructura), asegurando que:

- se evalúe el impacto GxP,
- se obtenga la aprobación adecuada,
- y se preserve el estado validado del sistema.

---

## 2. Alcance

Aplica a:

- Cambios en el código de Conitrack (nuevas versiones, parches).
- Cambios en la base de datos (estructura, constraints).
- Cambios en la configuración PROD.
- Cambios en la infraestructura que hospeda Conitrack.

No aplica a:

- Cambios puramente administrativos sin impacto en el sistema.

---

## 3. Responsabilidades

- Solicitante del cambio:
    - Describe la necesidad y el alcance.

- System Owner:
    - Evalúa impacto técnico y GxP.
    - Define la necesidad de pruebas y revalidación.

- Process Owner:
    - Evalúa impacto sobre el proceso de stock.

- QA:
    - Revisa y aprueba cambios que afecten el estado de validación.

- Desarrollador / Proveedor interno:
    - Implementa el cambio en el código o configuración.
    - Participa en pruebas según lo acordado.

---

## 4. Definiciones

- Cambio menor:
    - No impacta funcionalidades críticas de stock o seguridad.
    - Ej.: texto en pantalla, reporte secundario.

- Cambio mayor:
    - Impacta funcionalidades críticas, integridad de datos o seguridad.
    - Ej.: lógica de cálculo de stock, esquema de DB, roles.

---

## 5. Registro de cambios

Todos los cambios deben registrarse en un Registro de Cambios que incluya:

- ID del cambio.
- Fecha de solicitud.
- Descripción.
- Tipo (menor / mayor).
- Impacto esperado.
- Evaluación de riesgos.
- Aprobaciones.
- Resultados de pruebas.
- Fecha de implementación.

Git puede complementar este registro a nivel técnico (commits, tags), pero no lo reemplaza como documento GxP.

---

## 6. Flujo para cambios menores

1. Solicitud:
    - El solicitante describe el cambio y justifica por qué es menor.

2. Evaluación:
    - System Owner confirma que no hay impacto GxP relevante.
    - Process Owner valida que el cambio no afecta el proceso crítico.

3. Aprobación:
    - System Owner aprueba el cambio.
    - QA puede ser informado, sin necesidad de aprobación formal en cada caso (a criterio de la organización).

4. Implementación:
    - El desarrollador implementa el cambio en un entorno de prueba.
    - Se realizan pruebas limitadas (smoke test).

5. Migración a producción:
    - Se realiza el despliegue siguiendo el procedimiento habitual.

6. Cierre:
    - Se actualiza el Registro de Cambios con la fecha de implementación y resultados.

---

## 7. Flujo para cambios mayores

1. Solicitud:
    - Descripción detallada.
    - Justificación.
    - Análisis preliminar de impacto sobre datos de stock, seguridad y trazabilidad.

2. Evaluación:
    - System Owner y Process Owner completan una evaluación de impacto GxP.
    - Se determinan actividades de testing/revalidación requeridas (OQ adicional, regresión, etc.).

3. Aprobación:
    - QA revisa y aprueba el plan de cambio y pruebas asociadas.
    - Sin esta aprobación el cambio no puede implementarse en producción.

4. Implementación en entorno de prueba:
    - Desarrollador implementa el cambio.
    - Se ejecutan pruebas planificadas.
    - Se registran resultados y desviaciones.

5. Aprobación para producción:
    - System Owner y QA revisan los resultados de pruebas.
    - Si son satisfactorios, aprueban la implementación en producción.

6. Implementación en producción:
    - Se ejecuta el despliegue en PROD.
    - Se verifica funcionamiento básico post-deploy.

7. Cierre:
    - Registro de Cambios actualizado.
    - Si el cambio es significativo, se evalúa su impacto en el estado de validación y se ajusta la documentación (URS, OQ, etc.).

---

## 8. Relación con validación

Los cambios mayores pueden requerir:

- Actualización de URS.
- Nuevos casos de prueba OQ.
- Actualización de la matriz de trazabilidad.
- Inclusión en la Evaluación Periódica del sistema.

---

## 9. Registros generados

- Registro de Cambios.
- Evaluaciones de impacto.
- Evidencias de pruebas (capturas, logs).
- Aprobaciones de QA y responsables.

---

## 10. Aprobaciones

- System Owner: [Nombre, firma, fecha]
- Process Owner: [Nombre, firma, fecha]
- QA: [Nombre, firma, fecha]
