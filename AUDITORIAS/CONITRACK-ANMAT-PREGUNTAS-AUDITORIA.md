# Conitrack – Banco de preguntas de auditoría (modo hostil)

Este documento recopila preguntas posibles de un auditor muy estricto, agrupadas por tipo, para entrenamiento previo a auditoría.

---

## 1. Preguntas trampa

1. ¿Podés garantizar que nunca un dato de Conitrack (stock, lote, vencimiento) se usa como insumo para una decisión GxP, directa o indirectamente? ¿Qué evidencia mostrás?
2. Si mañana ANMAT te pide reconstruir todos los movimientos de un lote específico de hace 18 meses, ¿podés hacerlo solo con Conitrack?
3. ¿Tenés hoy, firmado, un documento donde QA acepte que la validación “proporcional” que proponés es suficiente para este sistema?
4. ¿Hay algún campo o tabla de la base de datos que pueda editarse directamente (SQL) sin dejar rastro en Conitrack?
5. ¿Podés asegurar que no existen usuarios compartidos (por ejemplo “depósito” o “operador”) usados por más de una persona?
6. ¿Conitrack se puede utilizar para decidir qué lote entregar a un cliente/paciente? Si no, ¿dónde está escrito?
7. ¿Tenés documentado y aprobado que el Excel anterior era aceptable GxP antes de Conitrack?
8. ¿Alguna vez un bug de Conitrack obligó a corregir stock manualmente? ¿Dónde está registrado?
9. ¿La versión actualmente en producción es exactamente la misma que la última versión validada/documentada?
10. ¿Existe algún escenario donde un fallo en Conitrack pueda llevar a un stock “falso positivo” (parece haber stock y no lo hay)?

---

## 2. Preguntas sobre contradicciones internas

1. En un documento decís “implementación técnica completa, documentación en proceso”. En otro hablás de “cumplimiento mínimo”. ¿Cuál describe el estado real hoy?
2. Si la criticidad es baja, ¿por qué definiste controles de seguridad tan robustos (hash de contraseñas, timeouts, audit trail)? ¿La seguridad es baja o alta?
3. Si la validación formal está “pendiente”, ¿cómo podés argumentar hoy que cumplís con la validación del Anexo 6?
4. Si la gestión de cambios se hace solo con Git, ¿cómo encaja eso con la exigencia de un procedimiento formal de gestión de cambios?
5. Si el backup y restore todavía no se probaron, ¿cómo podés afirmar que la configuración actual garantiza integridad de datos?
6. Si reemplazaste Excel, ¿dónde está el análisis formal que compare riesgos del proceso manual vs Conitrack?
7. Si las operaciones de stock afectan trazabilidad, ¿por qué descartás totalmente la necesidad de firma electrónica para aprobar movimientos críticos?
8. Si dices tener 100 % de cobertura de tests en módulos críticos, ¿dónde están los reportes de ejecución (GxP) y no sólo métricas de coverage de código?
9. Si afirmás que aplicás el Anexo 6 con enfoque proporcional, ¿dónde está el mapeo explícito punto del Anexo 6 → control de Conitrack?
10. Si el sistema ya se usa, ¿por qué nunca se ha realizado una evaluación periódica documentada?

---

## 3. Preguntas de auditor desconfiado (no te cree nada)

1. Decís que el sistema no se usa para liberación de lotes, pero no me mostrás ningún procedimiento que lo prohíba. ¿Por qué debería creerte?
2. Afirmás que la criticidad es baja, pero no traés la matriz de riesgos. ¿En qué evidencia objetiva te basás?
3. Usás el término “validación proporcional”, pero no existe un reporte de validación aprobado. ¿No será una manera elegante de nombrar algo que aún no hicieron?
4. Asegurás que los usuarios no comparten credenciales, pero no mostrás auditorías internas ni registros de control. ¿Cómo lo demostrás?
5. Decís que tienen backups, pero no hay un solo informe de restore probado. ¿Sobre qué afirmás que la recuperación está garantizada?
6. Mencionás “100 % de tests”, pero no mostras lista de casos ni firmas de QA. ¿No será solo una métrica interna de desarrollo?
7. Afirmás que no hubo incidentes críticos, pero tampoco existe un registro de incidentes menores. ¿Cómo sabés que no se repiten errores?
8. Decís que Conitrack mejora el Excel previo, pero no hay evaluación formal del proceso anterior. ¿Cómo sabés que no creaste riesgos nuevos?
9. Operan en producción con documentación “en elaboración”. ¿No es esto operar un sistema GxP no validado?
10. Hablás de “cumplimiento mínimo” ANMAT, pero varios puntos están declarados como “planificados” o “pendientes”. ¿No es prematuro hablar de cumplimiento?

---

## 4. Preguntas específicas estilo ANMAT (ligadas al Anexo 6)

1. ¿Dónde figura Conitrack en el inventario de sistemas informatizados sujetos a BPF?
2. ¿Dónde está documentada la designación formal del “propietario del proceso” y del “propietario del sistema”, según definiciones de Anexo 6?
3. ¿Cómo evaluaste al proveedor de infraestructura/hosting conforme a los puntos 3.2 y 3.4 del Anexo 6?
4. ¿Dónde está escrito que Conitrack no genera registros utilizados para la liberación de lotes (punto 15 del Anexo 6)?
5. ¿Qué evidencia existe de que las herramientas usadas para testing fueron evaluadas como idóneas (punto 4.7)?
6. ¿Qué controles existen cuando Conitrack intercambia datos con otros sistemas, de acuerdo al punto 5 sobre comprobaciones intrínsecas?
7. ¿En qué SOP se describe la política de copias de seguridad y la verificación de restore que exige el punto 7.2?
8. ¿Cómo demostrás que el audit trail se revisa regularmente y que esas revisiones se documentan, tal como indica el punto 9?
9. ¿Qué registros de evaluación de impacto y de aprobación previa mostrás para cambios de configuración, siguiendo el punto 10?
10. ¿Qué evidencia concreta existe de al menos una “evaluación periódica” del sistema conforme al punto 11?

---

## 5. Preguntas de “mock audit” (pregunta amable + repregunta destructiva)

Ejemplo de uso: practicar respuesta a P1 y anticipar la P2 que destruye tu respuesta.

1.
P1: ¿Me podría contar brevemente cuál es el rol de Conitrack en su proceso de stock?
P2: Si Conitrack “refleja el stock real”, ¿cómo puede decir que no tiene impacto GxP cuando ese stock condiciona qué lote se entrega?

2.
P1: ¿Conitrack reemplaza un Excel previo, correcto?
P2: Si el Excel previo no estaba evaluado ni validado, ¿no está replicando un riesgo histórico sin control al moverlo a sistema informatizado?

3.
P1: Veo que tienen medidas de seguridad (contraseñas, timeouts, etc.), muy bien.
P2: Si la seguridad es tan importante, ¿por qué no aplicaron el mismo rigor en validación y gestión de cambios?

4.
P1: Me dice que los usuarios han probado el sistema y “funciona bien”.
P2: ¿Dónde están esos resultados incorporados en OQ o PQ? Si no, ¿no es solo percepción?

5.
P1: Entiendo que tienen un script de backup de la base de datos.
P2: ¿Qué pasa si hoy se corrompe el servidor y la única copia es ese backup no probado? ¿Cómo garantiza la integridad de datos a la luz del Anexo 6?

6.
P1: El audit trail registra accesos de usuarios.
P2: Si un usuario modifica una cantidad de stock, ¿cómo reconstruyo valor anterior y motivo, como exige el punto de registro de cambios?

7.
P1: La gestión de cambios se hace a través de Git.
P2: ¿Dónde está el procedimiento formal de gestión de cambios (impacto GxP, aprobación previa) que requiere el Anexo 6?

8.
P1: No han tenido incidentes críticos con el sistema.
P2: ¿Cómo lo sabe si no tiene registro estructurado de incidentes menores?

9.
P1: Planean una revisión anual del sistema.
P2: Si el sistema ya está en uso, ¿por qué todavía no existe ninguna evaluación periódica realizada?

10.
P1: Usted conoce muy bien el sistema a nivel técnico.
P2: Si usted no estuviera, ¿existe suficiente documentación (URS, IQ/OQ, SOPs, RA, etc.) para mantener el sistema en estado validado sin depender de su memoria?
