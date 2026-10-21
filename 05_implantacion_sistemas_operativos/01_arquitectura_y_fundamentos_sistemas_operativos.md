# 01 - Arquitectura y Fundamentos de los Sistemas Operativos

> **Fuente:** [https://campus.europaeducationgroup.es/courses/156443](https://campus.europaeducationgroup.es/courses/156443)  
> **Módulo:** Unidad A. Instalación y fundamentos de software de base

## 📌 Conceptos Clave
- **Sistema Operativo (SO):** Software de base que gestiona los recursos de hardware, proporciona una interfaz de abstracción estandarizada y aísla los procesos de usuario.
- **Núcleo del Sistema (Kernel):** Componente central que opera en el nivel más privilegiado del hardware; media entre el software y los circuitos electrónicos.
- **Modos de Ejecución del Procesador:** Anillos de protección (**Ring 0 / Kernel Mode** con acceso irrestricto a memoria y puertos de E/S, frente a **Ring 3 / User Mode** con ejecución restringida).
- **Llamadas al Sistema (System Calls / Syscalls):** Interfaz programática formal para solicitar servicios privilegiados del kernel desde el espacio de usuario (ej. `read()`, `write()`, `fork()`, `open()`).
- **Taxonomía de Kernels:** Núcleos **Monolíticos** (Linux), **Microkernels** (Minix, QNX) y núcleos **Híbridos** (Windows NT, macOS XNU).
- **Sistemas de Archivos y Tablas de Partición:** MBR (límite 2 TB, 4 particiones primarias) frente a **GPT (GUID Partition Table)** con arranque nativo UEFI.

---

## 📖 Desarrollo y Resumen

### 1. Funciones Esenciales de un Sistema Operativo
Un sistema operativo cumple dos roles principales en la arquitectura del computador:
1. **Administrador de recursos:** Asigna y arbitra el uso de la CPU, memoria física, dispositivos de almacenamiento y ancho de banda de red entre múltiples procesos en competencia.
2. **Máquina extendida / Máquina virtual:** Proporciona abstracciones de alto nivel (como conceptos de "archivo", "socket" o "hilo") para evitar que los programas deban programar directamente los registros electrónicos de control de los discos duros o chips de red.

### 2. Estructura y Tipos de Núcleos (Kernels)
- **Kernel Monolítico (ej. Linux):**
  - Todos los servicios del sistema operativo (planificador de procesos, gestión de memoria, pila de red, controladores de hardware y sistemas de archivos) se ejecutan dentro del mismo espacio de direcciones privilegiado (**Ring 0**).
  - *Ventaja:* Rendimiento máximo por ausencia de sobrecarga en el cambio de contexto entre subsistemas.
  - *Desventaja:* Un fallo en un controlador de dispositivo puede desestabilizar todo el kernel (*Kernel Panic*).
- **Microkernel (ej. Minix, Mach):**
  - Solo los mecanismos indispensables (comunicación entre procesos IPC, gestión mínima de memoria y planificación básica) residen en el espacio del kernel.
  - Los controladores de hardware y los sistemas de archivos se ejecutan como procesos independientes en el espacio de usuario (**Ring 3**).
  - *Ventaja:* Máxima modularidad, seguridad y tolerancia a fallos.
  - *Desventaja:* Penalización de rendimiento por continuos cambios de contexto y paso de mensajes.
- **Kernel Híbrido (ej. Windows NT):**
  - Combina la velocidad del monolítico con la modularidad conceptual del microkernel, ejecutando la mayor parte de servicios en modo kernel pero con capas de emulación y subsistemas diferenciados.

### 3. El Mecanismo de las Llamadas al Sistema (Syscalls)
Cuando una aplicación de usuario (como un servidor web o una base de datos) necesita escribir un registro en disco:
1. La aplicación invoca una función envoltorio de la biblioteca estándar de C (ej. `write()` en `glibc`).
2. La biblioteca coloca los argumentos en registros de la CPU y ejecuta una instrucción de interrupción por software (como `syscall` en x86_64).
3. La CPU conmuta por hardware de **Modo Usuario a Modo Kernel**.
4. El kernel busca la rutina correspondiente en la **Tabla de Llamadas al Sistema (System Call Table)**, valida los punteros y permisos, y ejecuta la escritura física.
5. El kernel devuelve el control a la aplicación en Modo Usuario junto con el código de retorno.

---

## 💻 Código / Ejemplos

### Trazado e Inspección de Llamadas al Sistema en Linux con `strace`
```bash
# 1. Inspeccionar las llamadas al sistema ejecutadas por un comando simple
strace -c ls -la

# 2. Rastrear en tiempo real todas las aperturas y escrituras de archivos de un proceso
strace -e trace=openat,read,write,close date

# 3. Inspeccionar el kernel en ejecución y parámetros en tiempo real (/proc y /sys)
uname -a
cat /proc/version
sysctl kernel.osrelease kernel.hostname
```

### Script C: Ejecución Directa de una Syscall en Linux (Evitando abstracciones de alto nivel)
```c
// syscall_demo.c
#include <unistd.h>
#include <sys/syscall.h>

int main() {
    const char mensaje[] = "[KERNEL] Mensaje emitido mediante syscall directa SYS_write\n";
    
    // Invocación directa a la syscall número 1 (SYS_write)
    // Parámetros: descriptor de archivo (1 = stdout), puntero al búfer, longitud
    syscall(SYS_write, 1, mensaje, sizeof(mensaje) - 1);
    
    // Syscall directa de salida limpia (SYS_exit)
    syscall(SYS_exit, 0);
    return 0;
}
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Confundir la Shell con el Sistema Operativo:** La shell (Bash, Zsh, PowerShell) es simplemente un programa intérprete de comandos que se ejecuta en espacio de usuario (Ring 3), no el núcleo del sistema operativo.
- **Ejecutar servicios de red permanentemente como usuario `root`:** Cualquier vulnerabilidad de ejecución remota de código en un servicio que corre como `root` compromete el control total del kernel de la máquina. Configura siempre usuarios de servicio dedicados sin privilegios administrativos (ej. `www-data`, `mysql`).
- **Instalar sistemas en modo heredado MBR en lugar de UEFI/GPT:** MBR limita el particionado a discos de menos de 2 TB y carece de soporte para comprobaciones de integridad de arranque seguro (*Secure Boot*).
