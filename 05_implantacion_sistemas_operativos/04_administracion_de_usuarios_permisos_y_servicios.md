# 04 - Administración de Usuarios, Permisos POSIX, ACLs y Servicios con Systemd

> **Fuente:** [https://campus.europaeducationgroup.es/courses/156443](https://campus.europaeducationgroup.es/courses/156443)  
> **Módulo:** Unidad C. Administración y aseguramiento de la información

## 📌 Conceptos Clave
- **Modelo de Seguridad Multi-Usuario:** Identificación unívoca de identidades mediante Identificador de Usuario (**UID**) e Identificador de Grupo (**GID**).
- **Archivos de Autenticación Central:** `/etc/passwd` (cuentas e información básica), `/etc/shadow` (hashes criptográficos de contraseñas con sal y políticas de expiración) y `/etc/group`.
- **Permisos Estándar POSIX:** Matriz de permisos de tres niveles: **Usuario propietario (u)**, **Grupo (g)** y **Otros (o)**, con modos de **Lectura (r=4)**, **Escritura (w=2)** y **Ejecución (x=1)**.
- **Permisos Especiales:** **SUID (4000)**, **SGID (2000)** y **Sticky Bit (1000)**.
- **Listas de Control de Acceso (ACLs):** Extensión granular para definir permisos específicos sobre múltiples usuarios o grupos sin alterar la propiedad base.
- **Gestión de Servicios con Systemd:** Arquitectura basada en unidades (`.service`, `.socket`, `.target`) administrada mediante la herramienta `systemctl` e inspección de logs con `journalctl`.

---

## 📖 Desarrollo y Resumen

### 1. Permisos Estándar POSIX y Permisos Especiales
- **Significado del permiso de ejecución (`x`) en directorios:** A diferencia de los archivos regulares (donde permite ejecutar el binario), en un directorio el permiso `x` permite **acceder y atravesar el directorio** (`cd`) y acceder a los inodos de su interior.
- **Permisos Especiales:**
  - **SUID (*Set User ID* - 4000):** Cuando un usuario ejecuta un binario con SUID, el proceso se ejecuta temporalmente con los privilegios del **propietario del archivo** en lugar de los del usuario que lo invocó (ej. `/usr/bin/passwd` para que un usuario estándar pueda escribir su nueva contraseña en `/etc/shadow`).
  - **SGID (*Set Group ID* - 2000):** En archivos, ejecuta con los privilegios del grupo. En directorios, cualquier archivo creado en su interior hereda automáticamente el **grupo propietario del directorio padre**, fundamental para carpetas compartidas entre departamentos.
  - **Sticky Bit (1000):** Aplicado a directorios con permisos de escritura para todos (como `/tmp`), impide que un usuario elimine o renombre archivos pertenecientes a otros usuarios; solo el propietario del archivo o `root` pueden borrarlo.

### 2. Administración de Servicios con Systemd
`systemd` es el sistema de inicio estándar en las principales distribuciones Linux empresariales (RHEL, Debian, Ubuntu):
- **Ciclo de vida de un servicio:**
  - `systemctl start <servicio>`: Inicia el servicio en la sesión actual.
  - `systemctl stop <servicio>`: Detiene el proceso limpiamente enviando SIGTERM.
  - `systemctl restart <servicio>`: Detiene e inicia de nuevo el servicio.
  - `systemctl reload <servicio>`: Recarga la configuración sin cortar conexiones activas.
  - `systemctl enable <servicio>`: Crea los enlaces simbólicos en `/etc/systemd/system/` para que arranque automáticamente con el sistema.
  - `systemctl status <servicio>`: Muestra el estado operativo, PID principal, consumo de memoria y últimas líneas de log.

---

## 💻 Código / Ejemplos

### Script de Creación de Usuarios, Permisos de Departamento y ACLs
```bash
#!/bin/bash
# Script de aprovisionamiento de seguridad departamental ASIR

# 1. Crear grupo de trabajo para administradores de sistemas
sudo groupadd -g 2000 sysadmins

# 2. Crear usuario técnico sin acceso a shell interactiva (para servicios)
sudo useradd -r -u 1500 -g sysadmins -s /usr/sbin/nologin -d /var/run/app-service appservice

# 3. Crear usuario administrador con pertenencia a grupos secundarios
sudo useradd -m -u 1001 -g sysadmins -G sudo -s /bin/bash sysuser01

# 4. Crear carpeta departamental compartida con SGID
sudo mkdir -p /srv/compartido_ti
sudo chown root:sysadmins /srv/compartido_ti
# Permisos 2770: SGID activo + rwx para propietario y grupo + 0 para otros
sudo chmod 2770 /srv/compartido_ti

# 5. Asignar ACL granular para permitir lectura exclusiva a un auditor externo
sudo setfacl -m u:auditor:rx /srv/compartido_ti
sudo getfacl /srv/compartido_ti
```

### Creación de una Unidad de Servicio Personalizada (`/etc/systemd/system/asir-backup.service`):
```ini
[Unit]
Description=Servicio Automatizado de Respaldo ASIR
After=network.target

[Service]
Type=oneshot
User=root
ExecStart=/usr/local/bin/backup_script.sh
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

```bash
# Registrar y probar el nuevo servicio en systemd
sudo systemctl daemon-reload
sudo systemctl start asir-backup.service
sudo journalctl -u asir-backup.service -e
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Asignación permisiva `chmod 777`:** Otorgar permisos globales de lectura, escritura y ejecución a "Otros" (`777`) es una grave negligencia de seguridad que permite a cualquier usuario local modificar, inyectar código o eliminar archivos críticos del servicio.
- **Configurar SUID en binarios interpretados o scripts:** Asignar permisos SUID a scripts de shell (`.sh`) o scripts en lenguajes interpretados abre vulnerabilidades severas de escalada de privilegios a través de variables de entorno manipuladas (`PATH`, `LD_PRELOAD`).
- **Olvidar `systemctl daemon-reload`:** Modificar manualmente un archivo `.service` en disco sin ejecutar `systemctl daemon-reload` hace que systemd ignore los cambios y continúe ejecutando la configuración anterior residente en memoria.
