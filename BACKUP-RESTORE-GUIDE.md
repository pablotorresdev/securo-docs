# Guía de Backup y Restore - Conitrack

> **Documento formal ANMAT:** Esta guía es un complemento técnico del procedimiento formal `SOP-001-BACKUP-RECUPERACION.md` ubicado en `docs/ANMAT/documentacion-formal/`. Para auditorías ANMAT, referirse al SOP.

## Índice
1. [Información General](#información-general)
2. [Credenciales del Sistema](#credenciales-del-sistema)
3. [Restore en Windows (PC Nueva)](#restore-en-windows-pc-nueva)
4. [Restaurar en Docker Local (Desarrollo)](#restaurar-en-docker-local-desarrollo)
5. [Verificar Restauración](#verificar-restauración)
6. [Solución de Problemas](#solución-de-problemas)

---

## Información General

| Aspecto | Detalle |
|---------|---------|
| **Storage** | CloudFlare R2 |
| **Bucket** | `conitrack-backups` |
| **Frecuencia** | Automático: Domingos 3:00 AM UTC |
| **Retención** | 26 backups (~180 días) |
| **Formato** | `backup_YYYY-MM-DD_HH-MM-SS.sql.gz` |
| **PostgreSQL** | Versión 17 |

---

## Credenciales del Sistema

### CloudFlare R2 (Storage de Backups)

| Credencial | Valor |
|------------|-------|
| **Bucket Name** | `conitrack-backups` |
| **Account ID** | `b855cb04f73c723bba6fb58a56693c4c` |
| **Endpoint URL** | `https://b855cb04f73c723bba6fb58a56693c4c.r2.cloudflarestorage.com` |
| **Access Key ID** | Solicitar al administrador del sistema |
| **Secret Access Key** | Solicitar al administrador del sistema |

### Railway (Base de Datos de Producción)

| Credencial | Valor |
|------------|-------|
| **Host** | `maglev.proxy.rlwy.net` |
| **Puerto** | `48337` |
| **Database** | `railway` |
| **Usuario** | `postgres` |
| **Password** | Solicitar al administrador del sistema |
| **URL Completa** | `postgresql://postgres:PASSWORD@maglev.proxy.rlwy.net:48337/railway` |

> **Nota**: Las credenciales sensibles (Access Key, Secret Key, Password) deben solicitarse al administrador del sistema. No se incluyen en este documento por seguridad.

---

## Restore en Windows (PC Nueva)

Esta sección explica cómo restaurar un backup en una PC con Windows que no tiene nada instalado.

### Paso 1: Instalar Software Necesario

#### 1.1 Instalar Git for Windows (incluye gzip)

1. Descargar desde: https://git-scm.com/download/win
2. Ejecutar el instalador
3. **IMPORTANTE**: En la pantalla "Adjusting your PATH environment", seleccionar:
   - ✅ "Git from the command line and also from 3rd-party software"
4. Completar la instalación con opciones por defecto

#### 1.2 Instalar AWS CLI

1. Descargar desde: https://aws.amazon.com/cli/
2. Ejecutar el instalador `AWSCLIV2.msi`
3. Completar la instalación con opciones por defecto
4. **Reiniciar PowerShell** después de instalar

#### 1.3 Instalar PostgreSQL Client

1. Descargar desde: https://www.postgresql.org/download/windows/
2. Ejecutar el instalador
3. **IMPORTANTE**: En "Select Components", asegurate de marcar:
   - ✅ Command Line Tools
4. Instalar (podés desmarcar Stack Builder al final)
5. Agregar PostgreSQL al PATH:
   - Abrir PowerShell como Administrador
   - Ejecutar:
   ```powershell
   [Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\Program Files\PostgreSQL\17\bin", "Machine")
   ```
6. **Reiniciar PowerShell**

#### 1.4 Verificar instalación

Abrir una **nueva ventana de PowerShell** y ejecutar:

```powershell
git --version
aws --version
psql --version
gzip --version
```

Todos deben mostrar su versión sin errores.

---

### Paso 2: Configurar AWS CLI

Ejecutar en PowerShell:

```powershell
aws configure
```

Ingresar los siguientes datos cuando pregunte:

```
AWS Access Key ID [None]: <SOLICITAR AL ADMIN>
AWS Secret Access Key [None]: <SOLICITAR AL ADMIN>
Default region name [None]: auto
Default output format [None]: json
```

---

### Paso 3: Listar Backups Disponibles

```powershell
aws s3 ls s3://conitrack-backups/ --endpoint-url https://b855cb04f73c723bba6fb58a56693c4c.r2.cloudflarestorage.com
```

Salida esperada:
```
2026-01-06 10:46:26      12852 backup_2026-01-06_13-46-08.sql.gz
2026-01-13 10:00:15      14523 backup_2026-01-13_03-00-00.sql.gz
```

**Anotar el nombre del backup a restaurar** (ej: `backup_2026-01-06_13-46-08.sql.gz`)

---

### Paso 4: Descargar el Backup

Reemplazar `NOMBRE_DEL_BACKUP` con el archivo elegido:

```powershell
aws s3 cp s3://conitrack-backups/NOMBRE_DEL_BACKUP ./backup.sql.gz --endpoint-url https://b855cb04f73c723bba6fb58a56693c4c.r2.cloudflarestorage.com
```

**Ejemplo:**
```powershell
aws s3 cp s3://conitrack-backups/backup_2026-01-06_13-46-08.sql.gz ./backup.sql.gz --endpoint-url https://b855cb04f73c723bba6fb58a56693c4c.r2.cloudflarestorage.com
```

---

### Paso 5: Descomprimir el Backup

```powershell
gzip -d backup.sql.gz
```

Esto crea el archivo `backup.sql` en la carpeta actual.

---

### Paso 6: Limpiar la Base de Datos de Railway

> **⚠️ ADVERTENCIA**: Este comando BORRA TODOS LOS DATOS de la base de datos.

Reemplazar `PASSWORD` con la contraseña real:

```powershell
psql "postgresql://postgres:PASSWORD@maglev.proxy.rlwy.net:48337/railway" -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public; GRANT ALL ON SCHEMA public TO postgres; GRANT ALL ON SCHEMA public TO public;"
```

---

### Paso 7: Restaurar el Backup

Reemplazar `PASSWORD` con la contraseña real:

```powershell
psql "postgresql://postgres:PASSWORD@maglev.proxy.rlwy.net:48337/railway" -f backup.sql
```

Vas a ver muchos mensajes como `CREATE TABLE`, `ALTER TABLE`, `COPY`, etc. **Esto es normal.**

---

### Paso 8: Verificar la Restauración

```powershell
psql "postgresql://postgres:PASSWORD@maglev.proxy.rlwy.net:48337/railway" -c "SELECT 'roles' as tabla, COUNT(*) FROM roles UNION ALL SELECT 'users', COUNT(*) FROM users UNION ALL SELECT 'productos', COUNT(*) FROM productos UNION ALL SELECT 'lotes', COUNT(*) FROM lotes;"
```

Salida esperada (los números pueden variar):
```
    tabla    | count
-------------+-------
 roles       |     8
 users       |     8
 productos   |    72
 lotes       |     2
```

---

### Paso 9: Reiniciar la Aplicación en Railway

1. Ir a [railway.app](https://railway.app/)
2. Seleccionar el proyecto → el servicio de la aplicación (Spring Boot)
3. Click en **Deployments**
4. Click en **Redeploy** en el deployment más reciente

---

### Resumen Rápido de Comandos

```powershell
# Listar backups disponibles
aws s3 ls s3://conitrack-backups/ --endpoint-url https://b855cb04f73c723bba6fb58a56693c4c.r2.cloudflarestorage.com

# Descargar backup (reemplazar NOMBRE_DEL_BACKUP)
aws s3 cp s3://conitrack-backups/NOMBRE_DEL_BACKUP ./backup.sql.gz --endpoint-url https://b855cb04f73c723bba6fb58a56693c4c.r2.cloudflarestorage.com

# Descomprimir
gzip -d backup.sql.gz

# Limpiar DB (reemplazar PASSWORD)
psql "postgresql://postgres:PASSWORD@maglev.proxy.rlwy.net:48337/railway" -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public; GRANT ALL ON SCHEMA public TO postgres; GRANT ALL ON SCHEMA public TO public;"

# Restaurar (reemplazar PASSWORD)
psql "postgresql://postgres:PASSWORD@maglev.proxy.rlwy.net:48337/railway" -f backup.sql

# Verificar (reemplazar PASSWORD)
psql "postgresql://postgres:PASSWORD@maglev.proxy.rlwy.net:48337/railway" -c "SELECT COUNT(*) FROM users;"
```

---

## Restaurar en Docker Local (Desarrollo)

### Paso 1: Levantar PostgreSQL local

```bash
cd /ruta/a/securo
docker-compose up -d postgres-db
```

### Paso 2: Limpiar la base de datos (BORRA TODOS LOS DATOS)

```bash
docker exec postgres-db psql -U postgres -d conitrack -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public; GRANT ALL ON SCHEMA public TO postgres;"
```

### Paso 3: Descargar y restaurar backup

Reemplazar `NOMBRE_BACKUP` con el archivo que querés restaurar (ej: `backup_2026-01-06_13-46-08.sql.gz`).

#### Linux / macOS / Git Bash

```bash
docker run --rm --network securo_default \
  -e AWS_ACCESS_KEY_ID=$R2_ACCESS_KEY_ID \
  -e AWS_SECRET_ACCESS_KEY=$R2_SECRET_ACCESS_KEY \
  -e PGPASSWORD=root \
  postgres:17 bash -c '
    apt-get update -qq && apt-get install -y -qq curl unzip > /dev/null 2>&1
    curl -s "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip -q awscliv2.zip && ./aws/install > /dev/null 2>&1
    aws configure set aws_access_key_id "$AWS_ACCESS_KEY_ID"
    aws configure set aws_secret_access_key "$AWS_SECRET_ACCESS_KEY"
    aws s3 cp s3://conitrack-backups/NOMBRE_BACKUP /tmp/backup.sql.gz \
      --endpoint-url https://b855cb04f73c723bba6fb58a56693c4c.r2.cloudflarestorage.com
    gunzip -c /tmp/backup.sql.gz | psql -h postgres-db -U postgres -d conitrack
  '
```

#### Windows PowerShell

```powershell
docker run --rm --network securo_default `
  -e AWS_ACCESS_KEY_ID=$env:R2_ACCESS_KEY_ID `
  -e AWS_SECRET_ACCESS_KEY=$env:R2_SECRET_ACCESS_KEY `
  -e PGPASSWORD=root `
  postgres:17 bash -c 'apt-get update -qq && apt-get install -y -qq curl unzip > /dev/null 2>&1 && curl -s "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && unzip -q awscliv2.zip && ./aws/install > /dev/null 2>&1 && aws configure set aws_access_key_id "$AWS_ACCESS_KEY_ID" && aws configure set aws_secret_access_key "$AWS_SECRET_ACCESS_KEY" && aws s3 cp s3://conitrack-backups/NOMBRE_BACKUP /tmp/backup.sql.gz --endpoint-url https://b855cb04f73c723bba6fb58a56693c4c.r2.cloudflarestorage.com && gunzip -c /tmp/backup.sql.gz | psql -h postgres-db -U postgres -d conitrack'
```

#### Windows PowerShell (comando directo sin variables)

Reemplazar `TU_ACCESS_KEY`, `TU_SECRET_KEY` y `NOMBRE_BACKUP`:

```powershell
docker run --rm --network securo_default -e AWS_ACCESS_KEY_ID=TU_ACCESS_KEY -e AWS_SECRET_ACCESS_KEY=TU_SECRET_KEY -e PGPASSWORD=root postgres:17 bash -c 'apt-get update -qq && apt-get install -y -qq curl unzip > /dev/null 2>&1 && curl -s "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && unzip -q awscliv2.zip && ./aws/install > /dev/null 2>&1 && aws configure set aws_access_key_id "$AWS_ACCESS_KEY_ID" && aws configure set aws_secret_access_key "$AWS_SECRET_ACCESS_KEY" && aws s3 cp s3://conitrack-backups/NOMBRE_BACKUP /tmp/backup.sql.gz --endpoint-url https://b855cb04f73c723bba6fb58a56693c4c.r2.cloudflarestorage.com && gunzip -c /tmp/backup.sql.gz | psql -h postgres-db -U postgres -d conitrack'
```

### Paso 4: Levantar la aplicación

```bash
docker-compose up -d spring-app
# O con Gradle:
./gradlew bootRun
```

---

## Verificar Restauración

### Docker Local

```bash
docker exec postgres-db psql -U postgres -d conitrack -c "
  SELECT 'roles' as tabla, COUNT(*) FROM roles
  UNION ALL SELECT 'users', COUNT(*) FROM users
  UNION ALL SELECT 'productos', COUNT(*) FROM productos
  UNION ALL SELECT 'proveedores', COUNT(*) FROM proveedores
  UNION ALL SELECT 'lotes', COUNT(*) FROM lotes
  UNION ALL SELECT 'configuracion', COUNT(*) FROM configuracion;
"
```

### Railway (PowerShell)

```powershell
psql "postgresql://postgres:PASSWORD@maglev.proxy.rlwy.net:48337/railway" -c "SELECT 'roles' as tabla, COUNT(*) FROM roles UNION ALL SELECT 'users', COUNT(*) FROM users UNION ALL SELECT 'productos', COUNT(*) FROM productos UNION ALL SELECT 'lotes', COUNT(*) FROM lotes;"
```

### Verificar en la Aplicación

1. Abrir la aplicación en el navegador
2. Iniciar sesión con un usuario existente
3. Verificar que los datos (lotes, productos, etc.) estén correctos

---

## Solución de Problemas

### Error: "server version mismatch"

```
pg_dump: error: server version: 17.x; pg_dump version: 16.x
```

**Solución**: Usar imagen `postgres:17` en Docker, no `postgres:16`.

### Error: "connection refused"

```
psql: error: connection refused
```

**Solución**:
- Verificar que la URL de la base de datos es correcta
- Verificar que el servicio PostgreSQL está corriendo
- Verificar que no hay firewall bloqueando el puerto

### Error: "permission denied"

```
ERROR: permission denied for schema public
```

**Solución**: Ejecutar como usuario `postgres` o con permisos de superusuario.

### El backup está vacío (muy pequeño)

Si el archivo `.sql.gz` pesa menos de 1KB, puede ser que:
- La base de datos origen estaba vacía
- Hubo un error de conexión durante el backup

**Solución**: Verificar los logs del workflow en GitHub Actions.

---

## Contacto

Para problemas con el sistema de backup, contactar al equipo de desarrollo.

---

*Última actualización: 2026-01-06*

**Documento relacionado:** [SOP-001-BACKUP-RECUPERACION](docs/ANMAT/documentacion-formal/SOP-001-BACKUP-RECUPERACION.md)
