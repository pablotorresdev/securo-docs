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
