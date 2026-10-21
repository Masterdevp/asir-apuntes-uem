# 03 - Gestión de Memoria Virtual y Sistemas de Archivos

> **Fuente:** [https://campus.europaeducationgroup.es/courses/156443](https://campus.europaeducationgroup.es/courses/156443)  
> **Módulo:** Unidad B y C. Administración y aseguramiento de la información

## 📌 Conceptos Clave
- **Memoria Virtual:** Mecanismo arquitectónico que proporciona a cada proceso la ilusión de disponer de un espacio de memoria contiguo, aislado y exclusivo, desacoplado de la RAM física.
- **Unidad de Gestión de Memoria (MMU / Memory Management Unit):** Componente hardware de la CPU encargado de traducir direcciones virtuales a direcciones físicas en tiempo real.
- **Paginación y Marcos de Página (*Page Frames*):** División de la memoria virtual en bloques de tamaño fijo (típicamente 4 KiB en arquitecturas x86) asociados a marcos en la RAM física mediante **Tablas de Páginas**.
- **Fallo de Página (*Page Fault*):** Excepción hardware lanzada cuando un proceso intenta acceder a una página que no reside físicamente en la memoria RAM (se encuentra en el espacio de intercambio o *Swap*).
- **Espacio de Intercambio (Swap / Paging File):** Área de almacenamiento secundario utilizada como extensión de la memoria RAM.
- **Sistemas de Archivos (Filesystems):** Estructura lógica en disco para organizar y recuperar datos (**EXT4**, **XFS**, **NTFS**, **ZFS**). Concepto de **Inodo (*Inode*)** en sistemas Unix/Linux.

---

## 📖 Desarrollo y Resumen

### 1. Memoria Virtual y Paginación
La memoria virtual desacopla el software de las limitaciones y ubicaciones físicas de los módulos de memoria:
- **Protección y Aislamiento:** Un proceso no puede leer ni escribir en el espacio de memoria de otro proceso ni en el espacio reservado del kernel; cualquier intento genera una violación de segmento (*Segmentation Fault* / `SIGSEGV`).
- **Traducción mediante MMU y TLB:** Para acelerar la traducción de direcciones virtuales a físicas, la MMU utiliza una caché hardware de alta velocidad llamada **TLB (*Translation Lookaside Buffer*)**. Si la traducción está en la TLB (*TLB Hit*), el acceso se realiza sin penalización.
- **Algoritmos de Reemplazo de Páginas:** Cuando la RAM se satura y se requiere cargar una nueva página, el kernel desaloja páginas inactivas hacia la Swap utilizando algoritmos como **LRU (*Least Recently Used*)** o algoritmo de Reloj (*Clock*).

### 2. Estructura de Sistemas de Archivos y el Inodo en Linux
En sistemas tipo UNIX/Linux, el sistema de archivos (ej. EXT4) no almacena la información del archivo en su nombre, sino en una estructura de metadatos llamada **Inodo**:
- **Contenido de un Inodo:**
  - Identificador numérico del inodo (`inode number`).
  - Permisos de acceso (lectura, escritura, ejecución) y propietario/grupo (UID, GID).
  - Tamaño del archivo en bytes.
  - Marcas de tiempo (*Timestamps*): `atime` (último acceso), `mtime` (última modificación de contenido) y `ctime` (último cambio de metadatos).
  - Punteros a los bloques de datos físicos en el disco.
- **Enlaces Duros (*Hard Links*) vs Enlaces Simbólicos (*Symlinks*):**
  - *Hard Link:* Entrada de directorio adicional que apunta exactamente al **mismo número de inodo**. Si se borra el archivo original, el contenido sigue intacto hasta que se eliminen todos los enlaces duros asociados (contador de enlaces en el inodo llega a cero). No puede cruzar diferentes sistemas de archivos.
  - *Symbolic Link:* Archivo especial que contiene como dato la **ruta textual** hacia otro archivo. Si se borra el destino original, el symlink queda roto (*dangling link*). Puede enlazar directorios y cruzar sistemas de archivos distintos.

---

## 💻 Código / Ejemplos

### Diagnóstico de Memoria y Sistemas de Archivos en Linux (CLI)
```bash
# 1. Comprobar uso de memoria RAM física, buffers, caché y espacio Swap
free -h

# 2. Análisis del parámetro de kernel Swappiness (agresividad de uso de Swap de 0 a 100)
cat /proc/sys/vm/swappiness

# 3. Comprobar espacio en disco montado en formato legible
df -hT

# 4. Comprobar consumo y porcentaje de Inodos libres por sistema de archivos
df -i

# 5. Creación y verificación de Enlaces Duros vs Enlaces Simbólicos
echo "Dato crítico de configuración" > original.txt
ln original.txt hardlink.txt       # Enlace duro (comparte mismo inodo)
ln -s original.txt symlink.txt      # Enlace simbólico (nuevo inodo con puntero)

# Inspeccionar números de inodo asignados (columna 1)
ls -li original.txt hardlink.txt symlink.txt
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Hiperpaginación (*Thrashing*):** Cuando la memoria RAM física es insuficiente para la carga de trabajo y el kernel dedica más del 90% del tiempo de CPU a mover páginas entre la RAM y el disco de Swap en lugar de ejecutar instrucciones útiles, el servidor queda congelado. Se resuelve ampliando RAM física o limitando el consumo de los servicios.
- **Agotamiento de Inodos con Espacio Libre en Disco:** Un sistema de archivos puede tener cientos de gigabytes libres pero rechazar la creación de nuevos archivos (`No space left on device`). Esto ocurre cuando una aplicación genera millones de archivos diminutos (ej. sesiones web o correos), agotando la tabla preasignada de inodos (`df -i`).
- **Desactivar la Swap por completo:** Deshabilitar completamente el espacio de intercambio en servidores Linux impide que el kernel mueva páginas de memoria completamente inactivas para liberar espacio para cachés de disco (*Page Cache*), lo que degrada el rendimiento global del sistema de archivos.
