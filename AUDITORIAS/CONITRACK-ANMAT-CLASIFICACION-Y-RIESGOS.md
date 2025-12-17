# Conitrack – Clasificación GAMP y Análisis de Riesgos

Fecha: 2025-12-07
Sistema: Conitrack – Sistema de Gestión de Stock
Desarrollador: Socio fundador (proveedor interno)
Versión: 1.0 (plantilla)

---

## 1. Clasificación GAMP

- Tipo de sistema: desarrollo in-house (custom)
- Categoría GAMP 5: Software personalizado
- Impacto GxP: indirecto (sistema de soporte)
- Criticidad: baja, justificada por:
    - No realiza liberación de lotes.
    - No registra resultados de análisis de QC.
    - No ejecuta lógica de negocio que afecte directamente la calidad del producto.
    - Sustituye una planilla Excel con controles débiles.
    - Desarrollador interno con control completo del ciclo de vida.

Esta clasificación se utilizará para aplicar el Anexo 6 con enfoque proporcional al riesgo.

---

## 2. Alcance del análisis de riesgos

Alcance:

- Módulos de stock (lotes, cantidades, ubicaciones, vencimientos).
- Gestión de usuarios y roles.
- Funcionalidades de login, bloqueo y contraseñas.
- Auditoría de accesos y cambios.
- Backup y restore de la base de datos.
- Integración con otros sistemas (si aplica).
- Entorno de ejecución (servidor, base de datos, red).

Quedan fuera de alcance:

- Sistemas externos de QC, ERP completo, o sistemas que gestionan liberación de lote, aunque puedan consumir información de Conitrack.

---

## 3. Criterios de evaluación de riesgos

Cada riesgo se evalúa con:

- Probabilidad (P): Baja / Media / Alta
- Severidad (S): Baja / Media / Alta
- Nivel de riesgo inicial: combinación P×S
- Nivel de riesgo residual: después de aplicar controles

Enfoque:

- Riesgos con severidad alta reciben controles más fuertes, aunque la probabilidad sea baja.
- El objetivo es reducir riesgos a nivel aceptable para un sistema de impacto GxP indirecto y criticidad baja.

---

## 4. Matriz de riesgos (ejemplos base)

Esta matriz se debe completar y adaptar a la realidad.

### 4.1 Riesgos de integridad de datos

1) Riesgo: Modificación no autorizada de cantidades de stock
- Impacto: Cantidad de stock incorrecta → riesgo de errores logísticos y de trazabilidad.
- Probabilidad inicial: Media
- Severidad inicial: Media/Alta
- Controles:
    - Control de acceso por roles.
    - Audit trail de cambios de stock (usuario, fecha, antes/después).
    - Revisión periódica de movimientos.
- Evidencia:
    - Política de roles y permisos.
    - Implementación de audit trail.
    - Registros de revisión periódica.
- Riesgo residual: Bajo/Medio

2) Riesgo: Eliminación física de registros de stock
- Impacto: Pérdida de trazabilidad.
- Probabilidad inicial: Media
- Severidad inicial: Alta
- Controles:
    - Uso de soft deletes.
    - Restricciones en la base de datos.
    - Audit trail de cambios de estado.
- Evidencia:
    - Esquema de base de datos.
    - Código de lógica de soft delete.
- Riesgo residual: Bajo

3) Riesgo: Ingreso de datos inválidos (fechas, cantidades negativas)
- Impacto: Datos incoherentes, errores de gestión.
- Probabilidad inicial: Alta
- Severidad inicial: Media
- Controles:
    - Validaciones de entrada (Jakarta Validation, validadores custom).
    - Restricciones en DB (CHECK, NOT NULL, FK).
- Evidencia:
    - Código de validadores.
    - Script de esquema de DB.
- Riesgo residual: Bajo

---

### 4.2 Riesgos de seguridad y acceso

4) Riesgo: Acceso al sistema por usuario no autorizado
- Impacto: Cambio no autorizado de datos de stock.
- Probabilidad inicial: Media
- Severidad inicial: Alta
- Controles:
    - Autenticación con usuario y contraseña.
    - Política de contraseñas (longitud, complejidad).
    - Bloqueo tras varios intentos fallidos.
    - Timeout de sesión.
- Evidencia:
    - Configuración de seguridad (código).
    - Tests de login y bloqueo.
- Riesgo residual: Bajo

5) Riesgo: Usuario con rol excesivo (permisos que no corresponden)
- Impacto: Cambios no autorizados de datos.
- Probabilidad inicial: Media
- Severidad inicial: Alta
- Controles:
    - Matriz de roles y permisos.
    - Proceso de alta/cambio/baja de usuarios.
    - Revisión periódica de roles.
- Evidencia:
    - SOP de gestión de usuarios.
    - Registro de revisiones.
- Riesgo residual: Bajo/Medio

---

### 4.3 Riesgos de disponibilidad y continuidad

6) Riesgo: Caída del servidor sin backup válido
- Impacto: Pérdida total o parcial de datos de stock.
- Probabilidad inicial: Media
- Severidad inicial: Alta
- Controles:
    - Backup regular automatizado.
    - SOP de backup y restore.
    - Prueba periódica de restore.
- Evidencia:
    - Cron de backup.
    - Informe de restore probado.
- Riesgo residual: Bajo

7) Riesgo: Conitrack fuera de servicio durante tiempo crítico
- Impacto: No se pueden registrar movimientos de stock → riesgo operativo.
- Probabilidad inicial: Media
- Severidad inicial: Media/Alta
- Controles:
    - Procedimiento de contingencia (registro manual).
    - Reconciliación posterior a la restauración.
- Evidencia:
    - SOP de contingencia.
    - Registros de reconciliación (si se ejecutó).
- Riesgo residual: Bajo/Medio

---

### 4.4 Riesgos de proceso y uso

8) Riesgo: Uso del sistema para decisiones para las cuales no fue diseñado (ej. liberación de lotes)
- Impacto: Decisiones GxP tomadas con información no validada para ese propósito.
- Probabilidad inicial: Media
- Severidad inicial: Alta
- Controles:
    - Procedimientos que delimitan el uso del sistema.
    - Formación de usuarios.
- Evidencia:
    - SOP de uso de Conitrack.
    - Registros de entrenamiento.
- Riesgo residual: Bajo

9) Riesgo: Falta de entrenamiento de usuarios
- Impacto: Mala utilización, ingreso de datos erróneos o incompletos.
- Probabilidad inicial: Alta
- Severidad inicial: Media
- Controles:
    - Plan de entrenamiento.
    - Manual de usuario.
    - Registros de entrenamiento.
- Evidencia:
    - Actas de formación.
- Riesgo residual: Bajo/Medio

---

### 4.5 Riesgos de cambios no controlados

10) Riesgo: Cambio de versión del sistema sin evaluación de impacto
- Impacto: Introducción de errores no detectados, pérdida de integridad de datos.
- Probabilidad inicial: Media
- Severidad inicial: Alta
- Controles:
    - SOP de gestión de cambios.
    - Evaluación de impacto GxP.
    - Revalidación parcial (tests de regresión).
- Evidencia:
    - Registros de cambios.
    - Resultados de revalidación.
- Riesgo residual: Bajo/Medio

---

## 5. Aprobaciones

- System Owner: [Nombre, firma, fecha]
- Process Owner: [Nombre, firma, fecha]
- QA / Garantía de Calidad: [Nombre, firma, fecha]
