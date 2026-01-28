# SECURO-DOCS

Documentacion completa del sistema **Conitrack** para cumplimiento regulatorio ANMAT Anexo 6.

---

## Acerca del Sistema

**Conitrack** es un sistema de gestion de stock farmaceutico clasificado como:

- **GAMP 5** - Categoria 5 (Software Custom)
- **Criticidad:** Baja (sistema de soporte)
- **Impacto GxP:** Indirecto

---

## Estructura de Documentacion

```
securo-docs/
├── ANMAT/                      # Documentacion formal ANMAT
│   ├── documentacion-formal/   # 7 documentos oficiales
│   └── ANMAT-PENDIENTES.md     # Estado de cumplimiento
├── AUDITORIAS/                 # Guias y SOPs de auditoria
├── CU/                         # Casos de Uso del sistema
│   ├── FUNCIONAL/              # 22 CUs detallados
│   ├── SPECS/                  # Especificaciones tecnicas
│   └── SEGURIDAD/              # Validacion de seguridad
└── BACKUP-RESTORE-GUIDE.md     # Guia tecnica de backup
```

---

## Leyenda de Contenido

| Icono | Tipo de Contenido |
|-------|-------------------|
| :test_tube: | **Tests** - Casos de prueba a ejecutar |
| :white_check_mark: | **Checklist** - Lista de verificacion |
| :clipboard: | **Tareas** - Acciones a realizar |
| :warning: | **Validaciones** - Controles a verificar |
| :memo: | **Registros** - Formularios a completar |

---

## Documentos por Carpeta

### ANMAT/

| Documento | Descripcion | Contenido Ejecutable |
|-----------|-------------|----------------------|
| `ANMAT-PENDIENTES.md` | Estado de cumplimiento. Sistema **listo para auditoria** con 12/12 requisitos tecnicos y 7/7 documentos formales. | :white_check_mark: Checklist de criterios de exito |

#### ANMAT/documentacion-formal/

| Codigo | Documento | Descripcion | Contenido Ejecutable |
|--------|-----------|-------------|----------------------|
| **ERU-001** | Especificaciones de Requerimientos | 40+ requisitos funcionales, 15+ no funcionales, matriz de trazabilidad. | :warning: Matriz de trazabilidad requisitos-tests |
| **IQOQ-001** | Protocolo de Calificacion | Protocolo completo de calificacion IQ/OQ. | :test_tube: **5 tests IQ** (instalacion) :test_tube: **20+ tests OQ** (operacion) :white_check_mark: Checklist prerrequisitos :memo: Formularios de resultados PASS/FAIL :memo: Registro de desviaciones |
| **SOP-001** | Backup y Recuperacion | Procedimiento formal de backup/restore. | :clipboard: Procedimiento backup automatico :clipboard: Procedimiento backup manual :clipboard: Procedimiento de restore :white_check_mark: Checklist verificacion :memo: Registro de backups :memo: Registro de pruebas restore |
| **MAN-001** | Manual de Usuario | 14 secciones: login, lotes, compras, produccion, ventas, calidad, admin. | :clipboard: Campos a completar (URL, contactos) |
| **ROL-001** | Designacion de Roles | System Owner, Process Owner, IT, QA. Matriz RACI incluida. | :memo: Formularios de designacion :memo: Firmas de aceptacion de rol |
| **RSK-001** | Evaluacion de Riesgos | 18 riesgos identificados con controles. | :warning: **18 riesgos a evaluar** :warning: Matriz probabilidad/impacto :white_check_mark: Verificacion de controles |
| **GUIA-001** | Proximos Pasos Auditoria | Guia practica de preparacion. | :clipboard: **4 fases de tareas** :white_check_mark: Checklist documentos a firmar :white_check_mark: Checklist campos a completar :white_check_mark: Checklist verificacion por documento :memo: Cronograma 5-8 dias |

---

### AUDITORIAS/

#### Guias y Planes

| Documento | Descripcion | Contenido Ejecutable |
|-----------|-------------|----------------------|
| `CONITRACK-ANMAT-GUIA-GLOBAL.md` | Guia conceptual de cumplimiento. | :white_check_mark: **20 documentos minimos** requeridos |
| `CONITRACK-ANMAT-CLASIFICACION-Y-RIESGOS.md` | Clasificacion GAMP 5 y matriz de riesgos. | :warning: Matriz de riesgos base :warning: Criterios de evaluacion P x S |
| `CONITRACK-ANMAT-MAPEO-ANEXO6.md` | Mapeo Anexo 6 a controles del sistema. | :white_check_mark: Mapeo punto por punto :warning: Evidencias requeridas por requisito |
| `CONITRACK-ANMAT-PLAN-VALIDACION.md` | Plan de Validacion CSV. | :clipboard: **10 documentos asociados** a validacion :clipboard: Actividades IQ/OQ/PQ |
| `CONITRACK-ANMAT-PREGUNTAS-AUDITORIA.md` | Banco de preguntas de auditor. | :white_check_mark: **50+ preguntas** para entrenamiento :clipboard: Ejercicios mock audit |
| `CONITRACK-EVALUACION-PERIODICA.md` | Plantilla evaluacion periodica. | :memo: **Plantilla completa** a ejecutar anualmente :white_check_mark: Checklist revision funcionalidades :memo: Tabla cambios del periodo :memo: Tabla incidencias :memo: Registro backup/restore |

#### SOPs Operativos

| Documento | Descripcion | Contenido Ejecutable |
|-----------|-------------|----------------------|
| `CONITRACK-SOP-GESTION-CAMBIOS.md` | Gestion de cambios del sistema. | :clipboard: Procedimiento evaluacion cambios :memo: Registro de cambios :warning: Evaluacion impacto GxP |
| `CONITRACK-SOP-GESTION-INCIDENCIAS.md` | Gestion de incidencias. | :clipboard: Procedimiento reporte/cierre :memo: Registro de incidencias :clipboard: Proceso CAPA |
| `CONITRACK-SOP-GESTION-USUARIOS.md` | Alta/baja/modificacion usuarios. | :clipboard: Procedimiento alta/baja :memo: Registro de usuarios :white_check_mark: Matriz 8 roles y permisos |

#### Planes de Accion

| Documento | Descripcion | Contenido Ejecutable |
|-----------|-------------|----------------------|
| `PLAN-ACCION-AUDITORIA.md` | Evaluacion inicial de gaps *(supersedido)*. | :white_check_mark: Analisis 20 documentos :warning: Estado por documento |
| `PLAN-ACCION-BLINDADO.md` | **Plan definitivo de auditoria.** | :white_check_mark: **39 entregables** con checkboxes :memo: 11 docs gobierno :memo: 8 docs validacion :memo: 7 SOPs :memo: 7 registros operativos :clipboard: Respuestas blindadas a 50+ preguntas |

---

### CU/ (Casos de Uso)

| Documento | Descripcion | Contenido Ejecutable |
|-----------|-------------|----------------------|
| `README.md` | Indice de 22 Casos de Uso. | :white_check_mark: Clasificacion por tipo de operacion |
| `GUIA-CICLO-VIDA.md` | Ciclo de vida completo de lotes. | :warning: Matriz de permisos por CU :warning: Transiciones de estado validas :white_check_mark: Estados terminales |

#### CU/FUNCIONAL/

**22 Casos de Uso** organizados por area:

| Carpeta | CUs | Descripcion | Contenido |
|---------|-----|-------------|-----------|
| `1-PRODUCCION/` | CU1-CU11 | Ingreso, cuarentena, muestreo, devolucion, analisis, consumo, reanalisis, vencimientos, anulacion. | :warning: Validaciones de negocio :clipboard: Flujos de operacion |
| `2-VENTAS/` | CU20-CU25 | Ingreso produccion, trazado, liberacion, venta, devolucion venta, retiro mercado. | :warning: Validaciones de negocio :clipboard: Flujos de operacion |
| `3-CONTINGENCIAS/` | CU30-CU31 | Ajuste de stock, reverso de movimiento. | :warning: Validaciones de negocio :clipboard: Flujos de operacion |
| `4-AMBs/` | CU40-CU43 | ABM proveedores, productos, configuracion, usuarios. | :warning: Validaciones de negocio :clipboard: Flujos de operacion |

#### CU/SPECS/

**24 especificaciones tecnicas** - Cada una contiene:
- :warning: Validaciones de entrada
- :warning: Reglas de negocio
- :test_tube: Tests esperados
- :clipboard: Endpoints API

#### CU/SEGURIDAD/

| Documento | Descripcion | Contenido Ejecutable |
|-----------|-------------|----------------------|
| `CONITRACK-SEGURIDAD-VALIDACION-FORMAL.md` | Controles de seguridad. | :warning: Validaciones de seguridad :white_check_mark: Controles implementados |

---

### Raiz

| Documento | Descripcion | Contenido Ejecutable |
|-----------|-------------|----------------------|
| `BACKUP-RESTORE-GUIDE.md` | Guia tecnica de backup/restore. | :clipboard: **Procedimiento paso a paso** Windows :clipboard: **Procedimiento paso a paso** Docker :white_check_mark: Comandos de verificacion :clipboard: Solucion de problemas |

---

## Resumen de Contenido Ejecutable

| Tipo | Cantidad | Documentos Principales |
|------|----------|------------------------|
| :test_tube: **Tests** | 25+ | IQOQ-001 (5 IQ + 20 OQ), CU/SPECS |
| :white_check_mark: **Checklists** | 15+ | GUIA-001, PLAN-ACCION-BLINDADO, GUIA-GLOBAL |
| :clipboard: **Tareas/Procedimientos** | 20+ | SOPs, GUIA-001, BACKUP-RESTORE-GUIDE |
| :warning: **Validaciones** | 50+ | RSK-001, CU/FUNCIONAL, CU/SPECS, MAPEO-ANEXO6 |
| :memo: **Registros/Formularios** | 15+ | IQOQ-001, SOPs, EVALUACION-PERIODICA |

---

## Documentos Clave para Ejecucion

### Para ejecutar validacion IQ/OQ:
1. **IQOQ-001** - Protocolo con 25+ tests
2. **GUIA-001** - Fases de preparacion

### Para preparar auditoria:
1. **PLAN-ACCION-BLINDADO** - 39 entregables + respuestas
2. **PREGUNTAS-AUDITORIA** - 50+ preguntas de practica

### Para operacion diaria:
1. **SOP-GESTION-CAMBIOS** - Registro de cambios
2. **SOP-GESTION-INCIDENCIAS** - Registro de incidencias
3. **SOP-GESTION-USUARIOS** - Altas/bajas

### Para backup/restore:
1. **BACKUP-RESTORE-GUIDE** - Procedimiento tecnico
2. **SOP-001** - Procedimiento formal

---

## Resumen de Documentacion

| Categoria | Cantidad | Estado |
|-----------|----------|--------|
| Documentos Formales ANMAT | 7 | Completos |
| SOPs Operativos | 4 | Completos |
| Casos de Uso Funcionales | 22 | Completos |
| Especificaciones Tecnicas | 24 | Completos |
| Guias y Planes | 8 | Completos |
| **Total** | **65** | **Listo para Auditoria** |

---

## Estado de Cumplimiento

| Area | Estado |
|------|--------|
| Implementacion Tecnica | COMPLETO (12/12 requisitos) |
| Documentacion Formal | COMPLETO (7/7 documentos) |
| Procedimientos Backup | COMPLETO |
| Evaluacion de Riesgos | COMPLETO |
| Designacion de Roles | COMPLETO |

---

## Referencias Regulatorias

- **ANMAT Disposicion 4159/2023** - Anexo 6: Sistemas Informatizados
- **ISPE GAMP 5** - Guia para validacion de sistemas informatizados

---

*Ultima actualizacion: 2025-12-16*
