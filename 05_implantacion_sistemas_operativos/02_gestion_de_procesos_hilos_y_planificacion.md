# 02 - Gestión de Procesos, Hilos de Ejecución y Planificación de CPU

> **Fuente:** [https://campus.europaeducationgroup.es/courses/156443](https://campus.europaeducationgroup.es/courses/156443)  
> **Módulo:** Unidad B. Administración de software de base: Procesos y tareas

## 📌 Conceptos Clave
- **Proceso:** Instancia en memoria de un programa en ejecución; unidad básica de asignación de recursos protegidos (espacio de direcciones virtual, descriptores de archivos, variables de entorno).
- **Hilo de Ejecución (Thread):** Unidad básica de planificación de CPU que comparte el espacio de memoria y recursos con otros hilos del mismo proceso contenedor (*Multithreading*).
- **Bloque de Control de Proceso (PCB / Process Control Block):** Estructura del kernel que almacena el identificador (**PID**), estado, registros de CPU, puntero de pila y contabilidad de recursos de cada proceso.
- **Ciclo de Vida de un Proceso:** Estados clásicos: **Nuevo (*New*)**, **Preparado (*Ready*)**, **En Ejecución (*Running*)**, **Bloqueado / Esperando (*Waiting*)** y **Terminado (*Terminated*)**.
- **Cambio de Contexto (*Context Switch*):** Coste computacional de guardar el estado del procesador de un proceso y restaurar el de otro.
- **Algoritmos de Planificación de CPU:** FCFS (Primero en llegar), SJF (Trabajo más corto primero), Round Robin (reparto equitativo por *Quantum* de tiempo) y Colas Multinivel con Retroalimentación (MLFQ).

---

## 📖 Desarrollo y Resumen

### 1. El Proceso y su Bloque de Control (PCB)
El kernel gestiona los procesos mediante estructuras de datos en memoria llamadas PCB:
- **PID (*Process Identifier*) y PPID (*Parent Process ID*):** Identificador unívoco del proceso y del proceso padre que lo generó (en Linux, el proceso raíz con PID 1 es `systemd`).
- **Estados del Proceso:**
  - *Ready:* El proceso tiene todos los recursos asignados excepto la CPU y espera en la cola de planificación.
  - *Running:* Las instrucciones del proceso se están ejecutando físicamente en un núcleo de la CPU.
  - *Waiting / Blocked:* El proceso no puede ejecutarse porque está esperando un evento de E/S (como la lectura de disco o un paquete de red).
  - *Zombie:* Proceso terminado cuyo proceso padre aún no ha leído su código de salida con `wait()`.
- **Memoria de un Proceso:** Dividida en segmentos:
  - *Text:* Código binario de instrucciones ejecutables (de solo lectura).
  - *Data / BSS:* Variables globales inicializadas y no inicializadas.
  - *Heap:* Memoria dinámica reservada en tiempo de ejecución (`malloc` / `new`).
  - *Stack:* Pila de llamadas a funciones, variables locales y direcciones de retorno.

### 2. Algoritmos de Planificación de CPU
El planificador del kernel (*Scheduler*) decide qué proceso de la cola de preparados ocupa la CPU:
- **Round Robin (RR):** Asigna a cada proceso un intervalo temporal fijo denominado **Quantum** (habitualmente de 10 a 50 ms). Si el proceso no termina antes de que expire el quantum, es desalojado y enviado al final de la cola. Evita la inanición (*Starvation*).
- **Planificador CFS de Linux (Completely Fair Scheduler):** Basado en árboles rojo-negro; asigna CPU en función del tiempo de ejecución virtual (*vruntime*) acumulado por cada proceso ponderado por su prioridad **nice** (de -20 [máxima prioridad] a +19 [mínima]).

### 3. Señales del Sistema Operativo (Signals)
Mecanismo de comunicación asíncrona mediante el cual el kernel o un proceso notifica un evento a otro proceso:
- `SIGTERM` (15): Petición de terminación limpia y ordenada; el proceso puede capturarla para guardar estado y cerrar descriptores.
- `SIGKILL` (9): Terminación forzada inmediata ejecutada directamente por el kernel; no puede ser capturada ni ignorada por el proceso.
- `SIGHUP` (1): Notificación de pérdida de terminal o recarga de configuración en demonios sin reiniciar.

---

## 💻 Código / Ejemplos

### Monitorización y Gestión de Procesos en Linux (CLI)
```bash
# 1. Árbol jerárquico de procesos (identificar proceso padre PPID y dependencias)
pstree -p

# 2. Listado avanzado de procesos ordenados por consumo de memoria y CPU
ps -eo pid,ppid,user,%cpu,%mem,stat,comm --sort=-%cpu | head -n 15

# 3. Monitorización interactiva en tiempo real con filtrado
top -b -n 1 | head -n 20

# 4. Cambiar la prioridad de ejecución (Nice value) de un proceso existente
# Reducir valor nice = Aumentar prioridad (requiere root)
sudo renice -n -5 -p 1234

# 5. Envío controlado de señales: Recarga de configuración y terminación limpia
sudo kill -HUP $(pidof nginx)
kill -15 5678

# Si no responde tras un tiempo prudencial:
kill -9 5678
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Abuso de `kill -9` (`SIGKILL`):** Utilizar sistemáticamente `kill -9` para detener procesos o bases de datos impide que el software limpie archivos temporales de bloqueo (*lockfiles*), vacíe buffers en disco o cierre transacciones pendientes, provocando corrupción de datos. Usa siempre primero `SIGTERM` (15).
- **Procesos Zombie y Procesos Huérfanos:**
  - Un *proceso huérfano* ocurre cuando el padre muere antes que el hijo; el kernel reasigna automáticamente el hijo a `systemd` (PID 1) sin problemas.
  - Un *proceso zombie* retiene una entrada en la tabla de procesos del kernel. No consume CPU ni memoria, pero si se acumulan por bugs en el software padre pueden agotar el límite de PIDs del sistema (`/proc/sys/kernel/pid_max`).
- **Problema de Inanición (*Starvation*):** Asignar prioridades estáticas altas indefinidamente a procesos que realizan cálculos intensivos puede impedir que procesos de baja prioridad o servicios del sistema obtengan tiempo de CPU.
