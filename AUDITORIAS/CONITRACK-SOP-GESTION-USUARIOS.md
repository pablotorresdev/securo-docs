# SOP – Gestión de Usuarios y Roles en Conitrack

Código: SOP-CTK-USR-001  
Versión: 1.0  
Fecha: 2025-12-07

---

## 1. Objetivo

Definir el procedimiento para alta, modificación y baja de usuarios y roles en Conitrack, garantizando que solo personal autorizado tenga acceso y que los permisos sean acordes a sus funciones.

---

## 2. Alcance

Aplica a:

- Todos los usuarios con acceso a Conitrack.
- Todos los roles definidos en el sistema.
- Administradores con capacidad de crear o modificar usuarios.

---

## 3. Responsabilidades

- Process Owner:
    - Solicitar altas, cambios y bajas de usuarios.
    - Aprobar las solicitudes que afecten permisos y roles.

- System Owner:
    - Configurar roles y permisos en el sistema.
    - Garantizar que las configuraciones técnicas coincidan con lo definido en este SOP.

- Administrador de usuarios:
    - Ejecutar las altas, modificaciones y bajas según solicitudes aprobadas.
    - Mantener el registro de usuarios actualizado.

- QA:
    - Verificar periódicamente que los accesos sean acordes a las funciones.

---

## 4. Definiciones

- Usuario: Persona con credenciales para acceder a Conitrack.
- Rol: Conjunto de permisos asignado a un tipo de usuario.
- Alta: Creación de un nuevo usuario.
- Modificación: Cambio de rol, permisos o datos asociados.
- Baja: Desactivación o eliminación lógica de un usuario.

---

## 4.1 Roles del Sistema

El sistema define 8 roles organizados por areas funcionales:

| Rol | Area | Nivel | Descripcion |
|-----|------|-------|-------------|
| ADMIN | GLOBAL | 6 | Administrador del sistema - Acceso total |
| DT | GLOBAL | 5 | Director Tecnico - Aprobaciones finales |
| GERENTE_GARANTIA_CALIDAD | CALIDAD | 4 | Gerente QA - Aprobaciones de calidad |
| GERENTE_CONTROL_CALIDAD | CALIDAD | 3 | Gerente QC - Gestion de analisis |
| ANALISTA_CONTROL_CALIDAD | CALIDAD | 2 | Analista QC - Registro de analisis |
| SUPERVISOR_PLANTA | PLANTA | 3 | Supervisor produccion - Movimientos produccion |
| ANALISTA_PLANTA | PLANTA | 2 | Analista produccion - Movimientos basicos |
| AUDITOR | GLOBAL | 1 | Auditor externo - Solo lectura |

**Jerarquia por areas:**
- Roles GLOBAL (ADMIN, DT, AUDITOR): Tienen autoridad sobre todas las areas
- Roles CALIDAD: Solo tienen autoridad dentro del area CALIDAD
- Roles PLANTA: Solo tienen autoridad dentro del area PLANTA
- CALIDAD y PLANTA no tienen autoridad cruzada entre si
- AUDITOR nunca puede realizar operaciones de modificacion

---

## 4.2 Matriz CU→Roles (Permisos por Caso de Uso)

### Compras
| CU | Descripcion | Roles Autorizados |
|----|-------------|-------------------|
| CU1 | Ingreso de Lote por Compra | ADMIN, ANALISTA_PLANTA |
| CU4 | Devolucion de Compra | ADMIN, ANALISTA_PLANTA |

### Calidad
| CU | Descripcion | Roles Autorizados |
|----|-------------|-------------------|
| CU2 | Dictamen: Cuarentena | ADMIN, ANALISTA_CONTROL_CALIDAD |
| CU3 | Muestreo (Multi-Bulto y Trazable) | ADMIN, ANALISTA_CONTROL_CALIDAD |
| CU5/CU6 | Resultado Analisis Aprobado/Rechazado | ADMIN, GERENTE_CONTROL_CALIDAD |
| CU8 | Reanalisis de Producto | ADMIN, GERENTE_CONTROL_CALIDAD |
| CU11 | Anulacion de Analisis | ADMIN, GERENTE_CONTROL_CALIDAD |

### Produccion
| CU | Descripcion | Roles Autorizados |
|----|-------------|-------------------|
| CU7 | Consumo de Produccion | ADMIN, SUPERVISOR_PLANTA |
| CU20 | Ingreso por Produccion Interna | ADMIN, SUPERVISOR_PLANTA |

### Ventas
| CU | Descripcion | Roles Autorizados |
|----|-------------|-------------------|
| CU21 | Trazado Unidad de Venta | ADMIN, GERENTE_GARANTIA_CALIDAD, DT |
| CU22 | Confirmar Lote Liberado | ADMIN, GERENTE_GARANTIA_CALIDAD, DT |
| CU23 | Venta de Producto | ADMIN, ANALISTA_PLANTA |
| CU24 | Devolucion de Cliente | ADMIN, GERENTE_GARANTIA_CALIDAD |
| CU25 | Retiro de Mercado (Recall) | ADMIN, GERENTE_GARANTIA_CALIDAD |

### Contingencias
| CU | Descripcion | Roles Autorizados |
|----|-------------|-------------------|
| CU30 | Ajuste de Inventario | ADMIN, SUPERVISOR_PLANTA, ANALISTA_PLANTA |
| CU31 | Reverso de Movimiento | Todos excepto AUDITOR (reglas adicionales de jerarquia) |

### ABM Maestros
| CU | Descripcion | Roles Autorizados |
|----|-------------|-------------------|
| CU40 | ABM Proveedores | ADMIN, ANALISTA_PLANTA |
| CU41 | ABM Productos | ADMIN, GERENTE_CONTROL_CALIDAD |
| CU42 | Configuracion del Sistema | ADMIN |
| CU43 | ABM Usuarios | ADMIN |

### Consultas
| Funcion | Roles Autorizados |
|---------|-------------------|
| Consultas generales (Lotes, Movimientos, etc.) | Todos los roles |
| Consultas de Auditoria (incluye inactivos) | ADMIN, DT, AUDITOR |

**Nota:** El rol AUDITOR tiene acceso de solo lectura a todas las consultas y consultas de auditoria, pero NO puede realizar ninguna operacion de modificacion.

---

## 5. Proceso de alta de usuarios

1. El jefe directo del usuario (o Process Owner) completa una Solicitud de Alta de Usuario, incluyendo:
    - Nombre completo.
    - Puesto.
    - Rol requerido en Conitrack.
    - Fecha de inicio.
    - Justificación.

2. El Process Owner revisa y aprueba la solicitud.

3. El Administrador de usuarios:
    - Crea el usuario en Conitrack.
    - Asigna el rol solicitado.
    - Genera contraseña inicial (si aplica) y la comunica al usuario de forma segura.

4. El usuario debe cambiar la contraseña en el primer acceso (si la política lo indica).

5. El Administrador actualiza el Registro de Usuarios:
    - ID de usuario.
    - Rol.
    - Fecha de alta.
    - Referencia a la solicitud aprobada.

---

## 6. Proceso de modificación de usuarios

1. Cualquier modificación de rol o permisos requiere una Solicitud de Modificación, indicando:
    - Usuario.
    - Rol actual.
    - Rol solicitado.
    - Motivo del cambio.

2. El Process Owner aprueba o rechaza la solicitud.

3. El Administrador ejecuta el cambio en Conitrack.

4. El cambio se registra en el Registro de Usuarios:
    - Fecha de modificación.
    - Rol anterior.
    - Rol nuevo.
    - Aprobador.

---

## 7. Proceso de baja de usuarios

1. Ante renuncia, cambio de puesto o suspensión de funciones, el jefe directo o RRHH informa la baja.

2. El Administrador de usuarios:
    - Desactiva el usuario en Conitrack (no se recomienda borrar físicamente).
    - Registra la fecha de baja.

3. Para usuarios críticos, se puede revisar el audit trail para verificar últimas actividades, si se considera necesario.

---

## 8. Revisión periódica de accesos

1. Frecuencia recomendada: al menos 1 vez por año.

2. El Administrador extrae un listado de usuarios y roles de Conitrack.

3. El Process Owner revisa:
    - Usuarios activos.
    - Roles asignados.
    - Necesidad de ajustes.

4. Se documenta la revisión:
    - Fecha.
    - Observaciones.
    - Acciones (bajas, cambios de rol).

---

## 9. Usuarios compartidos

En principio:

- No se permiten usuarios compartidos.
- Cada usuario debe tener credenciales únicas.

Cualquier excepción debe:

- Estar justificada por escrito.
- Aprobada por System Owner y QA.
- Tener controles adicionales en audit trail.

---

## 10. Registros generados

- Solicitudes de alta, modificación y baja de usuarios.
- Registro de Usuarios (alta, cambios, baja).
- Registros de revisiones periódicas.

---

## 11. Aprobaciones

- System Owner: [Nombre, firma, fecha]
- Process Owner: [Nombre, firma, fecha]
- QA: [Nombre, firma, fecha]
