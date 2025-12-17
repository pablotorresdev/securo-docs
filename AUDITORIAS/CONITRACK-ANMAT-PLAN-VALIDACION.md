# Conitrack – Plan de Validación (CSV)

Fecha: 2025-12-07
Sistema: Conitrack – Sistema de Gestión de Stock
Versión del plan: 1.0

---

## 1. Objetivo

Definir el enfoque, alcance, responsabilidades, actividades y criterios de aceptación para la validación del sistema informatizado Conitrack, en cumplimiento del Anexo 6 ANMAT y GAMP 5, aplicando un enfoque proporcional al riesgo.

---

## 2. Alcance

Alcance funcional:

- Gestión de stock (lotes, cantidades, ubicaciones, vencimientos).
- Gestión de usuarios y roles.
- Login, autenticación y bloqueo de cuentas.
- Auditoría de accesos y cambios críticos de stock.
- Backup y restore de la base de datos.
- Configuración de producción.

Fuera de alcance (ejemplos):

- Sistemas externos de QC.
- Sistemas usados para liberación de lotes.
- Módulos no relacionados con stock (si existieran).

---

## 3. Base normativa y referencias

- ANMAT – Anexo 6: Sistemas Informatizados.
- GAMP 5 – A Risk-based Approach to Compliant GxP Computerized Systems.
- Política interna de validación de sistemas informatizados (si existe).
- Documento de Clasificación GAMP y Risk Assessment – Conitrack.

---

## 4. Roles y responsabilidades

- System Owner:
    - Definir requerimientos del sistema.
    - Aprobar el plan de validación.
    - Asegurar que el sistema se usa conforme a SOPs.
- Process Owner:
    - Validar que el sistema cubre las necesidades del proceso de stock.
    - Participar en PQ y en la aprobación final.
- QA:
    - Revisar y aprobar el plan de validación.
    - Verificar el cumplimiento de actividades y evidencias.
- Desarrollador / Proveedor interno:
    - Preparar entornos.
    - Ejecutar IQ.
    - Apoyar en OQ y solución de incidencias técnicas.

---

## 5. Documentos asociados a la validación

1) URS – Especificaciones de Requerimientos del Usuario
2) Risk Assessment – Conitrack
3) Matriz URS → Test
4) IQ – Protocolo y evidencias
5) OQ – Protocolo y evidencias
6) PQ – Protocolo y evidencias
7) SOP de Backup y Restore
8) SOP de Gestión de Cambios
9) SOP de Gestión de Incidencias
10) Evaluación Periódica del Sistema (posterior a la release)

---

## 6. Estrategia de validación

Enfoque proporcional basado en riesgo:

- Al ser un sistema de impacto GxP indirecto y criticidad baja:
    - Se combinarán algunas actividades de IQ/OQ.
    - El número de casos de OQ se centrará en los flujos críticos de stock y seguridad.
    - El PQ será un período de observación planificado en producción con control.

La validación debe cubrir:

- Integridad de datos: validaciones, constraints, comportamiento de transacciones.
- Seguridad y roles: login, bloqueo, permisos.
- Audit trail: registro de accesos y cambios críticos de stock.
- Backup y restore: al menos una prueba documentada exitosa.
- Continuidad de negocio: soporte al procedimiento de contingencia.

---

## 7. URS – Estructura propuesta

Las URS deben:

- Describir funciones requeridas.
- Indicar criticidad (Alta / Media / Baja).
- Estar aprobadas por System Owner, Process Owner y QA.

Ejemplo de formato URS:

| ID  | Descripción                             | Criticidad | Comentarios                    |
|-----|-----------------------------------------|-----------|--------------------------------|
| URS-01 | El sistema debe permitir login seguro con usuario y contraseña | Alta | Requisito de seguridad básica |
| URS-02 | El sistema debe registrar lotes con cantidad inicial y vencimiento | Alta | Impacto en trazabilidad        |
| URS-03 | El sistema debe registrar cada movimiento de stock con fecha y usuario | Alta | Integridad de stock            |
| URS-04 | El sistema debe registrar accesos de usuario en un log de auditoría | Media | Requisito de ANMAT Anexo 6     |

---

## 8. Trazabilidad URS → Test

Se elaborará una matriz de trazabilidad:

| URS  | Caso de prueba | Tipo (IQ/OQ/PQ) | Evidencia          | Resultado |
|------|----------------|-----------------|--------------------|-----------|
| URS-01 | TC-LOGIN-01   | OQ              | Capturas pantalla  | Aprobado  |
| URS-02 | TC-STOCK-01   | OQ              | Log de prueba      | Aprobado  |
| URS-03 | TC-MOV-01     | OQ              | Log + DB snapshot  | Aprobado  |
| URS-04 | TC-AUDIT-01   | OQ              | Extracto de log    | Aprobado  |

---

## 9. IQ – Installation Qualification

Objetivo:

- Verificar que el entorno de producción corresponde con el diseño y las especificaciones.

Contenido del protocolo IQ:

- Identificación del servidor / contenedor.
- Versión de sistema operativo.
- Versión de Java / JDK.
- Versión de base de datos.
- Parámetros relevantes (memoria, puertos, etc.).
- Configuración de Conitrack (application-PROD.yml).
- Configuración de logs y directorios.

Evidencias posibles:

- Capturas de comandos `java -version`, `psql --version`, etc.
- Copia o extracto de `application-PROD.yml`.
- Pruebas de conectividad y acceso restringido.

Criterio de aceptación:  
Todas las verificaciones deben coincidir con las especificaciones de diseño.

---

## 10. OQ – Operational Qualification

Objetivo:

- Verificar que el sistema funciona de acuerdo con las URS en condiciones normales.

Flujos principales a probar:

- Login / logout / bloqueo por intentos fallidos.
- Gestión de usuarios y roles.
- Alta de lote con cantidad inicial y vencimiento.
- Movimientos de stock (ingreso, egreso, ajuste).
- Consulta de stock por lote / ubicación.
- Registro de accesos (audit trail).
- Registro de cambios críticos en stock (si implementado).
- Ejecución de backup y simulacro de restore en entorno de prueba.

Formato de casos de prueba:

- ID del caso.
- Objetivo.
- Precondiciones.
- Pasos.
- Resultados esperados.
- Resultados obtenidos.
- Desviaciones (si las hubiera).

Criterio de aceptación:  
Todos los casos críticos deben resultar Aprobados o Aprobados con desviación evaluada y cerrada.

---

## 11. PQ – Performance Qualification

Objetivo:

- Confirmar que el sistema, en el entorno de producción y con usuarios reales, soporta el proceso de stock de manera estable y conforme a las URS.

Enfoque:

- Período de observación (por ejemplo, 2–4 semanas).
- Registro de:
    - incidentes,
    - problemas de rendimiento,
    - errores de uso.

Al final del período:

- El Process Owner emite una conclusión:
    - El sistema cumple las necesidades del proceso.
    - No se han detectado problemas críticos no cubiertos por la validación.

---

## 12. Criterios de aceptación global

La validación de Conitrack se considera satisfactoria cuando:

1. URS están definidas y aprobadas.
2. Risk Assessment está completado y firmado.
3. IQ se ha realizado con resultado satisfactorio.
4. OQ se ha ejecutado con todos los flujos críticos probados.
5. Se ha completado al menos un PQ con conclusión positiva.
6. Existe una prueba de restore documentada y aprobada.
7. Audit trail y controles de acceso están verificados.
8. Se ha publicado un reporte de validación que cierra formalmente el proceso.

---

## 13. Aprobaciones

- System Owner: [Nombre, firma, fecha]
- Process Owner: [Nombre, firma, fecha]
- QA: [Nombre, firma, fecha]
