# SOP-001: Procedimiento de Backup y Recuperacion
## Sistema Conitrack - Gestion de Stock Farmaceutico

---

**Codigo documento:** SOP-001
**Version:** 1.1
**Fecha:** 2026-01-06
**Estado:** Aprobado
**Fecha proxima revision:** 2027-01-06

---

## Control de Versiones

| Version | Fecha | Autor | Descripcion |
|---------|-------|-------|-------------|
| 1.0 | 2025-12-16 | Equipo IT | Version inicial aprobada |
| 1.1 | 2026-01-06 | Equipo IT | Actualizar tablas Envers, sincronizar con GitHub Actions + R2 |

---

## Firmas de Aprobacion

| Rol | Nombre | Firma | Fecha |
|-----|--------|-------|-------|
| Elaborado por | _________________ | _________________ | ____/____/____ |
| Revisado por (QA) | _________________ | _________________ | ____/____/____ |
| Aprobado por (IT Manager) | _________________ | _________________ | ____/____/____ |

---

## Indice de Contenidos

1. [Objetivo](#1-objetivo)
2. [Alcance](#2-alcance)
3. [Responsabilidades](#3-responsabilidades)
4. [Definiciones](#4-definiciones)
5. [Politica de Backup](#5-politica-de-backup)
6. [Procedimiento de Backup Automatico](#6-procedimiento-de-backup-automatico)
7. [Procedimiento de Backup Manual](#7-procedimiento-de-backup-manual)
8. [Procedimiento de Verificacion](#8-procedimiento-de-verificacion)
9. [Procedimiento de Recuperacion (Restore)](#9-procedimiento-de-recuperacion-restore)
10. [Planes de Accion por Plataforma](#10-planes-de-accion-por-plataforma)
11. [Registro de Backups](#11-registro-de-backups)
12. [Registro de Pruebas de Restore](#12-registro-de-pruebas-de-restore)
13. [Anexos](#13-anexos)

---

## 1. Objetivo

Establecer el procedimiento para realizar backups periodicos de la base de datos del sistema Conitrack y su recuperacion en caso de falla, garantizando la integridad y disponibilidad de los datos conforme a los requisitos de ANMAT Disposicion 4159/2023 Anexo 6, Requisito 7 (Archivo de datos).

**Referencia regulatoria:**
> "Debe realizarse regularmente copias de seguridad de todos los datos relevantes. La integridad y la exactitud de las copias de seguridad de datos y la capacidad de re-establecer los datos debe comprobarse durante la validacion y controlarse periodicamente" - ANMAT Anexo 6, Requisito 7.2

---

## 2. Alcance

Este procedimiento aplica a:
- Base de datos PostgreSQL del sistema Conitrack
- Todos los ambientes: Produccion, Staging (si aplica)
- Personal de IT responsable de administracion del sistema

**Datos incluidos en backup:**
- Tabla `lotes` - Lotes de productos
- Tabla `movimientos` - Movimientos de stock
- Tabla `analisis` - Resultados de analisis
- Tabla `trazas` - Registros de trazabilidad
- Tabla `bultos` - Bultos/paquetes
- Tabla `detalle_movimientos` - Detalles por bulto de cada movimiento
- Tabla `auditoria_accesos` - Accesos de auditores
- Tablas maestras: `productos`, `proveedores`, `users`, `roles`
- Tabla `configuracion` - Configuracion del sistema
- Tabla `alertas` - Alertas del sistema
- Tabla `password_history` - Historial de contraseñas (ANMAT Req. 12)
- Tablas Hibernate Envers (audit trail): `revinfo`, `lotes_aud`, `movimientos_aud`, `bultos_aud`, `analisis_aud`, `trazas_aud`, `productos_aud`, `proveedores_aud`, `users_aud`, `roles_aud`, `configuracion_aud`, `alertas_aud`

---

## 3. Responsabilidades

| Rol | Responsabilidad |
|-----|-----------------|
| **IT Manager** | Aprobar procedimiento, supervisar cumplimiento, autorizar restore en produccion |
| **Administrador de BD / DevOps** | Configurar backups automaticos, ejecutar restore, verificar logs, mantener scripts |
| **QA** | Verificar cumplimiento de procedimiento, participar en pruebas trimestrales de restore |
| **System Owner** | Autorizar restore en produccion, notificar a usuarios |

---

## 4. Definiciones

| Termino | Definicion |
|---------|------------|
| **Backup** | Copia de seguridad de la base de datos en formato SQL o dump |
| **Restore** | Recuperacion de datos desde un backup a la base de datos activa |
| **RPO** | Recovery Point Objective - Maxima perdida de datos aceptable (expresada en tiempo) |
| **RTO** | Recovery Time Objective - Tiempo maximo aceptable para recuperacion del sistema |
| **Backup completo** | Copia de toda la base de datos incluyendo estructura y datos |
| **pg_dump** | Herramienta nativa de PostgreSQL para generar backups logicos |
| **Verificacion de integridad** | Comprobacion de que el archivo de backup no esta corrupto y contiene datos validos |

---

## 5. Politica de Backup

### 5.1 Frecuencia y Retencion

| Tipo de Backup | Frecuencia | Retencion | RPO Resultante |
|----------------|------------|-----------|----------------|
| **Automatico (Produccion)** | Semanal (Domingos 03:00 UTC) | 26 backups (~180 dias) | 7 dias maximo |
| **Manual (Pre-cambios)** | Antes de actualizaciones/migraciones | 90 dias | Inmediato |
| **Adicional Cloud** | Segun proveedor (ver Seccion 10) | Segun plan | Variable |

> **Nota:** Los backups automaticos se ejecutan via GitHub Actions y se almacenan en CloudFlare R2.

### 5.2 Ubicacion de Almacenamiento

| Ambiente | Ubicacion Principal | Ubicacion Secundaria |
|----------|--------------------|--------------------|
| **Produccion (Railway)** | CloudFlare R2 (`conitrack-backups`) | Railway Backups (automatico) |
| Docker Local/VPS | `/opt/securo/backups/` | Almacenamiento externo (USB/NAS) |

> **Referencia tecnica:** Ver `BACKUP-RESTORE-GUIDE.md` para instrucciones detalladas de restore.

### 5.3 Objetivos de Recuperacion

| Metrica | Objetivo | Justificacion |
|---------|----------|---------------|
| **RPO** | Maximo 7 dias | Sistema de soporte, operaciones pueden reconstruirse desde documentos fisicos |
| **RTO** | Maximo 4 horas | Tiempo razonable para restaurar operaciones normales |

### 5.4 Nomenclatura de Archivos

Formato estandar: `backup_YYYY-MM-DD_HH-MM-SS.sql.gz`

Ejemplo: `backup_2026-01-06_03-00-00.sql.gz`

---

## 6. Procedimiento de Backup Automatico

### 6.1 Configuracion Inicial (Docker Local/VPS)

**Responsable:** Administrador de BD / DevOps

**Prerequisitos:**
- Acceso al servidor con permisos de administrador
- Docker instalado y contenedor `postgres-db` en ejecucion
- Directorio de backups creado con permisos de escritura

**Script de backup (`/opt/securo/backup-conitrack.sh`):**

```bash
#!/bin/bash
# backup-conitrack.sh
# Backup automatico de Conitrack PostgreSQL
# Version: 1.0 | Fecha: 2025-12-16

# ============================================
# CONFIGURACION
# ============================================
BACKUP_DIR="/opt/securo/backups"
CONTAINER_NAME="postgres-db"
DB_NAME="conitrack"
DB_USER="postgres"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/conitrack_$DATE.sql"
LOG_FILE="$BACKUP_DIR/backup.log"
RETENTION_DAYS=30
MIN_TABLES=10

# ============================================
# INICIO DEL BACKUP
# ============================================
mkdir -p "$BACKUP_DIR"

echo "$(date '+%Y-%m-%d %H:%M:%S'): === INICIO BACKUP ===" >> "$LOG_FILE"

# Ejecutar backup
docker exec "$CONTAINER_NAME" pg_dump -U "$DB_USER" "$DB_NAME" > "$BACKUP_FILE" 2>> "$LOG_FILE"

if [ $? -eq 0 ]; then
    # Comprimir
    gzip "$BACKUP_FILE"
    FINAL_FILE="$BACKUP_FILE.gz"
    SIZE=$(du -h "$FINAL_FILE" | cut -f1)

    # Verificar integridad
    if gzip -t "$FINAL_FILE" 2>/dev/null; then
        INTEGRITY="OK"
    else
        INTEGRITY="ERROR"
    fi

    # Verificar contenido (contar tablas)
    TABLES=$(zcat "$FINAL_FILE" | grep -c "CREATE TABLE")
    if [ "$TABLES" -ge "$MIN_TABLES" ]; then
        CONTENT="OK ($TABLES tablas)"
    else
        CONTENT="ALERTA (solo $TABLES tablas)"
    fi

    echo "$(date '+%Y-%m-%d %H:%M:%S'): Backup exitoso" >> "$LOG_FILE"
    echo "  Archivo: $FINAL_FILE" >> "$LOG_FILE"
    echo "  Tamano: $SIZE" >> "$LOG_FILE"
    echo "  Integridad: $INTEGRITY" >> "$LOG_FILE"
    echo "  Contenido: $CONTENT" >> "$LOG_FILE"

    # Limpiar backups antiguos
    DELETED=$(find "$BACKUP_DIR" -name "conitrack_*.sql.gz" -mtime +$RETENTION_DAYS -delete -print | wc -l)
    echo "  Backups eliminados (>$RETENTION_DAYS dias): $DELETED" >> "$LOG_FILE"
else
    echo "$(date '+%Y-%m-%d %H:%M:%S'): ERROR - Backup fallido" >> "$LOG_FILE"
    # Opcional: enviar alerta
    # echo "Backup Conitrack fallido" | mail -s "ALERTA BACKUP" admin@empresa.com
fi

echo "$(date '+%Y-%m-%d %H:%M:%S'): === FIN BACKUP ===" >> "$LOG_FILE"
echo "" >> "$LOG_FILE"
```

**Asignar permisos:**
```bash
chmod +x /opt/securo/backup-conitrack.sh
```

### 6.2 Configuracion de Cron (Linux)

```bash
# Editar crontab del usuario root o con permisos
sudo crontab -e

# Agregar linea para backup semanal (Domingos 02:00)
0 2 * * 0 /opt/securo/backup-conitrack.sh

# Alternativa: backup diario
# 0 2 * * * /opt/securo/backup-conitrack.sh
```

**Verificar configuracion:**
```bash
crontab -l | grep backup
```

### 6.3 Configuracion en Windows (Programador de Tareas)

**Script PowerShell (`C:\opt\securo\backup-conitrack.ps1`):**

```powershell
# backup-conitrack.ps1
# Backup automatico de Conitrack PostgreSQL para Windows

$BackupDir = "C:\opt\securo\backups"
$ContainerName = "postgres-db"
$Date = Get-Date -Format "yyyyMMdd_HHmmss"
$BackupFile = "$BackupDir\conitrack_$Date.sql"
$LogFile = "$BackupDir\backup.log"
$RetentionDays = 30

# Crear directorio si no existe
New-Item -ItemType Directory -Force -Path $BackupDir | Out-Null

Add-Content $LogFile "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss'): === INICIO BACKUP ==="

# Ejecutar backup
docker exec $ContainerName pg_dump -U postgres conitrack > $BackupFile 2>> $LogFile

if ($LASTEXITCODE -eq 0) {
    # Comprimir
    Compress-Archive -Path $BackupFile -DestinationPath "$BackupFile.zip" -Force
    Remove-Item $BackupFile
    $FinalFile = "$BackupFile.zip"
    $Size = [math]::Round((Get-Item $FinalFile).Length / 1MB, 2)

    Add-Content $LogFile "  Backup exitoso: $FinalFile ($Size MB)"

    # Limpiar backups antiguos
    $Deleted = (Get-ChildItem "$BackupDir\conitrack_*.zip" | Where-Object {
        $_.LastWriteTime -lt (Get-Date).AddDays(-$RetentionDays)
    } | Remove-Item -PassThru).Count
    Add-Content $LogFile "  Backups eliminados (>$RetentionDays dias): $Deleted"
} else {
    Add-Content $LogFile "  ERROR: Backup fallido"
}

Add-Content $LogFile "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss'): === FIN BACKUP ==="
Add-Content $LogFile ""
```

**Crear tarea programada:**
```cmd
schtasks /create /tn "Backup Conitrack" /tr "powershell.exe -ExecutionPolicy Bypass -File C:\opt\securo\backup-conitrack.ps1" /sc weekly /d SUN /st 02:00 /ru SYSTEM
```

### 6.4 Verificacion de Ejecucion Semanal

**Frecuencia:** Cada lunes (post-backup dominical)
**Responsable:** Administrador de BD

**Pasos:**

1. Revisar log de backup:
   ```bash
   tail -30 /opt/securo/backups/backup.log
   ```

2. Verificar existencia del archivo:
   ```bash
   ls -lh /opt/securo/backups/ | head -5
   ```

3. Confirmar tamano razonable (>1 MB para BD con datos)

4. Registrar en Seccion 11

---

## 7. Procedimiento de Backup Manual

### 7.1 Cuando Ejecutar Backup Manual

- **OBLIGATORIO** antes de:
  - Actualizaciones de la aplicacion Spring Boot
  - Migraciones de base de datos (Flyway)
  - Cambios de configuracion criticos
  - Restore de un backup anterior

- **RECOMENDADO** ante:
  - Solicitud de QA o System Owner
  - Antes de operaciones masivas (imports, deletes)

### 7.2 Procedimiento (Docker Local/VPS)

**Responsable:** Administrador de BD

**Pasos:**

1. **Notificar** a usuarios si la operacion afecta disponibilidad

2. **Conectar al servidor:**
   ```bash
   ssh usuario@servidor
   # O acceder directamente si es local
   ```

3. **Ejecutar script de backup:**
   ```bash
   cd /opt/securo
   ./backup-conitrack.sh
   ```

4. **Verificar resultado:**
   ```bash
   ls -lh /opt/securo/backups/ | head -3
   tail -20 /opt/securo/backups/backup.log
   ```

5. **Registrar** en Seccion 11

### 7.3 Backup Manual Alternativo (Sin Script)

Si el script no esta disponible, ejecutar directamente:

```bash
# Variables
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="/opt/securo/backups/manual_$DATE.sql"

# Backup
docker exec postgres-db pg_dump -U postgres conitrack > "$BACKUP_FILE"

# Comprimir
gzip "$BACKUP_FILE"

# Verificar
ls -lh /opt/securo/backups/manual_$DATE.sql.gz
```

---

## 8. Procedimiento de Verificacion

### 8.1 Verificacion Automatica (En Script)

El script de backup incluye verificacion automatica de:
- **Integridad del archivo** (gzip -t)
- **Contenido minimo** (conteo de tablas >= 10)

Los resultados se registran en `backup.log`.

### 8.2 Verificacion Manual de Integridad (Mensual)

**Frecuencia:** Mensual (primer lunes del mes)
**Responsable:** Administrador de BD

**Procedimiento:**

1. Seleccionar backup mas reciente:
   ```bash
   BACKUP=$(ls -t /opt/securo/backups/conitrack_*.sql.gz | head -1)
   echo "Verificando: $BACKUP"
   ```

2. Verificar integridad del archivo:
   ```bash
   gzip -t "$BACKUP" && echo "OK: Archivo integro" || echo "ERROR: Archivo corrupto"
   ```

3. Verificar contenido (sin restaurar):
   ```bash
   zcat "$BACKUP" | head -100
   # Debe mostrar sentencias SQL validas (CREATE TABLE, COPY, etc.)
   ```

4. Contar tablas en backup:
   ```bash
   zcat "$BACKUP" | grep -c "CREATE TABLE"
   # Esperado: >= 15 tablas
   ```

5. Registrar resultado en log o planilla de control

### 8.3 Prueba de Restore (Trimestral)

**Frecuencia:** Trimestral (Marzo, Junio, Septiembre, Diciembre)
**Responsable:** Administrador de BD + QA

Ver Seccion 9.3 y registrar en Seccion 12.

---

## 9. Procedimiento de Recuperacion (Restore)

### 9.1 Tipos de Restore

| Tipo | Descripcion | Impacto | Aprobacion Requerida |
|------|-------------|---------|---------------------|
| **Restore a ambiente de prueba** | Verificacion de backup | Ninguno en produccion | No |
| **Restore completo produccion** | Reemplaza todos los datos | ALTO - Perdida de datos posteriores al backup | Si (System Owner) |
| **Restore parcial** | Recupera tabla/datos especificos | MEDIO | Si (IT Manager) |

### 9.2 Restore Completo en Produccion

**ADVERTENCIA:** Este procedimiento REEMPLAZA todos los datos actuales con los del backup. Todos los datos ingresados despues del backup SE PERDERAN.

#### Prerequisitos (Checklist)

- [ ] Aprobacion escrita del System Owner (email/ticket)
- [ ] Motivo del restore documentado
- [ ] Usuarios notificados de indisponibilidad
- [ ] Tiempo estimado comunicado (RTO: 4 horas max)
- [ ] Backup del estado actual realizado (por seguridad)
- [ ] Personal de soporte disponible
- [ ] Plan de rollback definido

#### Procedimiento

**Paso 1: Obtener aprobacion**
- Enviar solicitud formal a System Owner
- Documentar: motivo, backup a restaurar, datos que se perderan
- Esperar aprobacion escrita (email o ticket)

**Paso 2: Notificar a usuarios**
```
ASUNTO: Sistema Conitrack - Mantenimiento Programado

El sistema Conitrack estara no disponible desde [HORA] hasta [HORA ESTIMADA].
Motivo: Restauracion de base de datos.
Disculpe las molestias.
```

**Paso 3: Detener aplicacion**
```bash
docker-compose stop spring-app
# Verificar que esta detenida
docker ps | grep spring-app
```

**Paso 4: Backup del estado actual (por seguridad)**
```bash
DATE=$(date +%Y%m%d_%H%M%S)
docker exec postgres-db pg_dump -U postgres conitrack > /opt/securo/backups/pre_restore_$DATE.sql
gzip /opt/securo/backups/pre_restore_$DATE.sql
echo "Backup pre-restore: pre_restore_$DATE.sql.gz"
```

**Paso 5: Terminar conexiones activas**
```bash
docker exec postgres-db psql -U postgres -c \
  "SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE datname = 'conitrack' AND pid <> pg_backend_pid();"
```

**Paso 6: Eliminar base de datos actual**
```bash
docker exec postgres-db psql -U postgres -c "DROP DATABASE IF EXISTS conitrack;"
```

**Paso 7: Crear base de datos vacia**
```bash
docker exec postgres-db psql -U postgres -c "CREATE DATABASE conitrack;"
```

**Paso 8: Restaurar backup**
```bash
# Reemplazar YYYYMMDD_HHMMSS con fecha del backup a restaurar
BACKUP_FILE="/opt/securo/backups/conitrack_YYYYMMDD_HHMMSS.sql.gz"
gunzip -c "$BACKUP_FILE" | docker exec -i postgres-db psql -U postgres -d conitrack
```

**Paso 9: Verificar integridad**
```bash
# Contar registros en tablas principales
docker exec postgres-db psql -U postgres -d conitrack -c "SELECT 'lote' as tabla, COUNT(*) as registros FROM lote UNION ALL SELECT 'movimiento', COUNT(*) FROM movimiento UNION ALL SELECT 'users', COUNT(*) FROM users UNION ALL SELECT 'producto', COUNT(*) FROM producto;"
```

**Paso 10: Reiniciar aplicacion**
```bash
docker-compose start spring-app

# Esperar 30 segundos para inicio completo
sleep 30

# Verificar que esta corriendo
docker ps | grep spring-app
```

**Paso 11: Verificar funcionamiento**
- Acceder a URL del sistema
- Verificar que login funciona
- Verificar que datos estan presentes
- Realizar operacion de prueba (solo lectura)

**Paso 12: Notificar finalizacion**
```
ASUNTO: Sistema Conitrack - Servicio Restaurado

El sistema Conitrack ha sido restaurado y esta operativo.
IMPORTANTE: Los datos han sido restaurados al estado del [FECHA DEL BACKUP].
Cualquier dato ingresado despues de esa fecha debera re-ingresarse.
```

**Paso 13: Documentar**
- Registrar en Seccion 12
- Archivar aprobacion del System Owner
- Documentar cualquier incidencia

### 9.3 Restore a Ambiente de Prueba (Verificacion Trimestral)

**Uso:** Verificacion de que el backup es restaurable (requerido por ANMAT)

**Procedimiento:**

1. **Crear base de datos de prueba:**
   ```bash
   docker exec postgres-db psql -U postgres -c "CREATE DATABASE conitrack_restore_test;"
   ```

2. **Seleccionar backup a probar:**
   ```bash
   BACKUP=$(ls -t /opt/securo/backups/conitrack_*.sql.gz | head -1)
   echo "Restaurando: $BACKUP"
   ```

3. **Restaurar:**
   ```bash
   gunzip -c "$BACKUP" | docker exec -i postgres-db psql -U postgres -d conitrack_restore_test
   ```

4. **Verificar contenido:**
   ```bash
   # Contar registros
   docker exec postgres-db psql -U postgres -d conitrack_restore_test -c \
     "SELECT 'lote' as tabla, COUNT(*) FROM lote UNION ALL SELECT 'movimiento', COUNT(*) FROM movimiento;"

   # Comparar con produccion (opcional)
   docker exec postgres-db psql -U postgres -d conitrack -c \
     "SELECT 'lote' as tabla, COUNT(*) FROM lote UNION ALL SELECT 'movimiento', COUNT(*) FROM movimiento;"
   ```

5. **Limpiar:**
   ```bash
   docker exec postgres-db psql -U postgres -c "DROP DATABASE conitrack_restore_test;"
   ```

6. **Registrar resultado** en Seccion 12

---

## 10. Planes de Accion por Plataforma

### 10.1 Heroku

**Backups automaticos:** Incluidos en planes pagos ($9+/mes)

| Plan | Frecuencia | Retencion |
|------|------------|-----------|
| Basic ($9/mes) | Diario | 7 dias |
| Standard ($50/mes) | Diario | 25 dias |
| Premium ($200+/mes) | Diario | 50 dias |

**Comandos utiles:**
```bash
# Ver estado de backups
heroku pg:backups --app conitrack

# Crear backup manual
heroku pg:backups:capture --app conitrack

# Programar backup diario 02:00 Argentina
heroku pg:backups:schedule DATABASE_URL --at '02:00 America/Argentina/Buenos_Aires' --app conitrack

# Restaurar ultimo backup
heroku pg:backups:restore --app conitrack

# Descargar backup localmente
heroku pg:backups:download --app conitrack
# Genera: latest.dump
```

**Restore local del dump descargado:**
```bash
pg_restore --verbose --clean --no-acl --no-owner -h localhost -U postgres -d conitrack latest.dump
```

### 10.2 Render

**Backups automaticos:** Incluidos en todos los planes PostgreSQL

| Plan | Frecuencia | Retencion | PITR |
|------|------------|-----------|------|
| Starter | Diario | 7 dias | No |
| Standard | Diario | 30 dias | No |
| Pro ($85+/mes) | Continuo | 7 dias | Si |

**Procedimiento via Dashboard:**
1. Ir a https://dashboard.render.com
2. Seleccionar la base de datos
3. Tab "Backups"
4. Click en backup deseado > "Restore" o "Download"

**Restore local:**
```bash
gunzip backup.sql.gz
psql -h localhost -U postgres -d conitrack < backup.sql
```

### 10.3 Railway

**Backups automaticos:** Incluidos en plan Pro ($20/mes)

| Caracteristica | Valor |
|----------------|-------|
| Frecuencia | Cada 24 horas |
| Retencion | 7 dias |

**Comandos CLI:**
```bash
# Instalar CLI
npm install -g @railway/cli

# Login
railway login

# Ver backups
railway postgres backups

# Crear backup manual
railway postgres backup create

# Descargar backup
railway postgres backup download <backup-id>
```

### 10.4 Docker Local / VPS

**Plan de accion:**

1. **Implementar script de backup** (Seccion 6.1)
2. **Configurar cron/scheduler** (Seccion 6.2 o 6.3)
3. **Verificar ejecucion semanal** (Seccion 6.4)
4. **Prueba de restore trimestral** (Seccion 9.3)

**Almacenamiento secundario recomendado:**
- Copiar backups semanales a disco externo USB
- O sincronizar con servicio cloud (Google Drive, S3, etc.)

```bash
# Ejemplo: copiar a disco externo
cp /opt/securo/backups/conitrack_*.sql.gz /mnt/backup_externo/

# Ejemplo: sincronizar con S3 (requiere AWS CLI)
aws s3 sync /opt/securo/backups/ s3://mi-bucket-backups/conitrack/
```

---

## 11. Registro de Backups

Utilizar esta tabla para registrar la ejecucion de backups:

| Fecha | Hora | Tipo | Archivo | Tamano | Verificado | Ejecutado Por | Resultado | Observaciones |
|-------|------|------|---------|--------|------------|---------------|-----------|---------------|
| ____/____/____ | ____:____ | Auto/Manual | | | Si/No | | OK/Error | |
| ____/____/____ | ____:____ | Auto/Manual | | | Si/No | | OK/Error | |
| ____/____/____ | ____:____ | Auto/Manual | | | Si/No | | OK/Error | |
| ____/____/____ | ____:____ | Auto/Manual | | | Si/No | | OK/Error | |
| ____/____/____ | ____:____ | Auto/Manual | | | Si/No | | OK/Error | |
| ____/____/____ | ____:____ | Auto/Manual | | | Si/No | | OK/Error | |
| ____/____/____ | ____:____ | Auto/Manual | | | Si/No | | OK/Error | |
| ____/____/____ | ____:____ | Auto/Manual | | | Si/No | | OK/Error | |

---

## 12. Registro de Pruebas de Restore

Utilizar esta tabla para registrar las pruebas trimestrales de restore:

| Fecha | Trimestre | Backup Usado | Ambiente | Tiempo Restore | Registros Verificados | Ejecutado Por | Verificado Por (QA) | Resultado | Observaciones |
|-------|-----------|--------------|----------|----------------|----------------------|---------------|---------------------|-----------|---------------|
| ____/____/____ | Q1/Q2/Q3/Q4 | | Test/Prod | ___min | lote:___ mov:___ | | | OK/Fail | |
| ____/____/____ | Q1/Q2/Q3/Q4 | | Test/Prod | ___min | lote:___ mov:___ | | | OK/Fail | |
| ____/____/____ | Q1/Q2/Q3/Q4 | | Test/Prod | ___min | lote:___ mov:___ | | | OK/Fail | |
| ____/____/____ | Q1/Q2/Q3/Q4 | | Test/Prod | ___min | lote:___ mov:___ | | | OK/Fail | |

---

## 13. Anexos

### 13.1 Ubicacion de Scripts

| Script | Ubicacion | Proposito |
|--------|-----------|-----------|
| backup-conitrack.sh | /opt/securo/backup-conitrack.sh | Backup automatico Linux |
| backup-conitrack.ps1 | C:\opt\securo\backup-conitrack.ps1 | Backup automatico Windows |
| custom_backup.sh | /opt/securo/data-base/custom_backup.sh | Backup manual simple |

### 13.2 Documentacion Relacionada

| Documento | Codigo | Descripcion |
|-----------|--------|-------------|
| Guia de Automatizacion | BACKUP-AUTOMATIZACION.md | Instrucciones detalladas por plataforma |
| Protocolo IQ/OQ | IQOQ-001 | Tests TC-051 a TC-055 de backup |

### 13.3 Checklist Pre-Restore Produccion

Antes de ejecutar restore en produccion, verificar:

- [ ] Aprobacion escrita del System Owner obtenida
- [ ] Motivo del restore documentado
- [ ] Usuarios notificados de indisponibilidad
- [ ] Tiempo estimado comunicado
- [ ] Backup del estado actual realizado
- [ ] Personal de soporte disponible
- [ ] Backup a restaurar identificado y verificado
- [ ] Plan de comunicacion post-restore preparado

### 13.4 Contactos de Emergencia

| Rol | Nombre | Telefono | Email |
|-----|--------|----------|-------|
| IT Manager | _________________ | _________________ | _________________ |
| Administrador BD | _________________ | _________________ | _________________ |
| System Owner | _________________ | _________________ | _________________ |
| Soporte QA | _________________ | _________________ | _________________ |

### 13.5 Historial de Incidentes de Restore

| Fecha | Descripcion del Incidente | Backup Usado | Tiempo de Recuperacion | Datos Perdidos | Acciones Correctivas |
|-------|---------------------------|--------------|------------------------|----------------|---------------------|
| | | | | | |
| | | | | | |

---

**Fin del documento**

---

**Aprobaciones finales:**

| | Firma | Fecha |
|---|-------|-------|
| **Elaborado por:** | _________________ | ____/____/____ |
| **Revisado por (QA):** | _________________ | ____/____/____ |
| **Aprobado por (IT Manager):** | _________________ | ____/____/____ |
