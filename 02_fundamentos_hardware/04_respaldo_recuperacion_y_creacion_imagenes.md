# 04 - Respaldo, Recuperación y Creación de Imágenes de Sistema

> **Fuente:** [https://campus.europaeducationgroup.es/courses/150547](https://campus.europaeducationgroup.es/courses/150547)  
> **Módulo:** Unidad 04. Respaldo y creación de imágenes

## 📌 Conceptos Clave
- **Estrategia de Backup 3-2-1:** 3 copias de los datos, en 2 soportes físicos de distinta tecnología, con al menos 1 copia fuera de las instalaciones (*Offsite / Cloud*).
- **Tipos de Copia de Seguridad:** Copia **Completa** (*Full*), **Diferencial** (cambios acumulados desde el último Full) e **Incremental** (cambios desde la última copia de cualquier tipo).
- **RTO y RPO:** *Recovery Time Objective* (tiempo admisible de recuperación) y *Recovery Point Objective* (pérdida máxima de datos tolerable en el tiempo).
- **Configuraciones RAID:** Tolerancia a fallos por hardware/software: RAID 0 (Striping), RAID 1 (Mirroring), RAID 5 (Paridad distribuida), RAID 10 (1+0).
- **Clonación vs Imagen de Disco:** Duplicación física bit a bit directa frente a encapsulación de sectores en un único archivo contenedor comprimido (ISO, VHD, QCOW2).

---

## 📖 Desarrollo y Resumen

### 1. Comparativa de Estrategias de Backup
Para optimizar espacio y ventanas temporales de respaldo se combinan tres tipos de copias:
- **Copia Completa (*Full*):** Respalda la totalidad de los datos seleccionados. Es la más lenta y pesada de generar, pero la más rápida y sencilla de restaurar (requiere un único medio).
- **Copia Diferencial:** Respalda únicamente los archivos creados o modificados desde la **última copia completa**. La restauración requiere siempre la copia completa original más la **última copia diferencial**.
- **Copia Incremental:** Respalda únicamente los cambios producidos desde la **última copia realizada**, sea esta completa o incremental. Es la más rápida de ejecutar y la que menos espacio consume, pero la restauración exige procesar en cadena la copia completa y todas las incrementales intermedias sin perder ninguna.

### 2. Niveles de Almacenamiento Redundante (RAID)
| Nivel RAID | Mínimo Discos | Tolerancia a Fallos | Capacidad Útil | Rendimiento |
| :--- | :--- | :--- | :--- | :--- |
| **RAID 0** | 2 | **Ninguna** (Fallo = Pérdida total) | $N \times \text{Tamaño}$ (100%) | Lectura/Escritura máxima |
| **RAID 1** | 2 | 1 disco | $1 \times \text{Tamaño}$ (50%) | Lectura rápida, escritura normal |
| **RAID 5** | 3 | 1 disco (Paridad distribuida) | $(N - 1) \times \text{Tamaño}$ | Lectura muy rápida, penalización en escritura |
| **RAID 6** | 4 | 2 discos (Doble paridad) | $(N - 2) \times \text{Tamaño}$ | Alta seguridad, mayor cálculo paridad |
| **RAID 10** | 4 | Hasta 1 disco por sub-espejo | $\frac{N}{2} \times \text{Tamaño}$ (50%) | Excelente rendimiento y seguridad |

*Nota técnica fundamental:* **Un sistema RAID proporciona alta disponibilidad de hardware, NO sustituye a una copia de seguridad**. El borrado accidental, un ransomware o una corrupción de sistema de archivos se propaga en tiempo real a todos los discos del array.

### 3. Técnicas de Clonación e Imágenes
- **Clonación sector a sector (*Bit-to-Bit*):** Copia idéntica de cada sector físico (incluyendo sectores vacíos, metadatos y tabla de particiones MBR/GPT). Ideal para análisis forense o sustitución idéntica de disco con `dd` o Clonezilla.
- **Imagen de disco inteligente:** Solo copia los sectores con datos asignados por el sistema de archivos (NTFS, EXT4), aplicando compresión gzip/zstd y permitiendo restauración en discos de diferente tamaño o hardware heterogéneo.

---

## 💻 Código / Ejemplos

### Automatización de Copias de Seguridad con `rsync` en Linux
```bash
#!/bin/bash
# Script de Backup Incremental mediante Hard Links con rsync

ORIGEN="/home/master/proyectos/"
DESTINO="/mnt/d/backups/incrementales"
FECHA=$(date +"%Y-%m-%d_%H-%M-%S")
DIRECTORIO_ACTUAL="$DESTINO/$FECHA"
ULTIMO_BACKUP="$DESTINO/latest"

mkdir -p "$DESTINO"

# Si existe un backup previo, vincula archivos no modificados con enlaces duros
if [ -d "$ULTIMO_BACKUP" ]; then
    rsync -av --delete --link-dest="$ULTIMO_BACKUP" "$ORIGEN" "$DIRECTORIO_ACTUAL"
else
    rsync -av "$ORIGEN" "$DIRECTORIO_ACTUAL"
fi

# Actualiza el puntero de enlace simbólico al último backup completado
rm -f "$ULTIMO_BACKUP"
ln -s "$DIRECTORIO_ACTUAL" "$ULTIMO_BACKUP"

echo "[OK] Respaldo completado en $DIRECTORIO_ACTUAL"
```

### Clonación de Disco / Creación de Imagen Forense con `dd`:
```bash
# Crear imagen RAW comprimida de una partición de sistema
sudo dd if=/dev/sda1 status=progress bs=64K conv=noerror,sync | gzip -c > /mnt/d/backups/sda1_sistema.img.gz

# Restaurar la imagen comprimida sobre un nuevo disco
gunzip -c /mnt/d/backups/sda1_sistema.img.gz | sudo dd of=/dev/sdb1 status=progress bs=64K
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Confundir RAID con Backup:** Creer que tener un RAID 1 o RAID 5 exime de hacer copias de seguridad es un error crítico. La corrupción lógica o ataques de ransomware destruyen ambos lados del espejo simultáneamente.
- **No probar la restauración de los backups:** Un respaldo cuya restauración no ha sido verificada en un entorno de pruebas no es una garantía de seguridad. Realiza simulacros periódicos de recuperación en frío (*Disaster Recovery Test*).
- **Almacenar la copia en la misma máquina o red local:** Si la copia reside en el mismo disco, máquina o NAS sin aislamiento de red, un pico de tensión, robo o ransomware cifrará tanto los datos de producción como los respaldos.
