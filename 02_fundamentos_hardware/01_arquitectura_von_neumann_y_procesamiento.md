# 01 - Arquitectura de Von Neumann y Elementos Funcionales del Procesador

> **Fuente:** [https://campus.europaeducationgroup.es/courses/150547](https://campus.europaeducationgroup.es/courses/150547)  
> **Módulo:** Unidad 01. Fundamentos de Hardware

## 📌 Conceptos Clave
- **Arquitectura de Von Neumann (1945):** Modelo de computación basado en programa almacenado donde datos e instrucciones comparten el mismo subsistema de memoria y bus.
- **Unidad Central de Proceso (CPU):** Compuesta por la **Unidad de Control (UC)**, la **Unidad Aritmético-Lógica (ALU)** y el banco de **Registros internos**.
- **Ciclo de Instrucción:** Proceso continuo dividido en dos macrofases: **Búsqueda (*Fetch*)** y **Ejecución (*Execute*)**.
- **Registros Especializados:** Contador de Programa (**PC**), Registro de Instrucción (**IR**), Registro de Dirección de Memoria (**MAR**) y Registro de Datos de Memoria (**MDR/MBR**).
- **Tipología de Buses:** Bus de **Datos** (bidireccional), Bus de **Direcciones** (unidireccional desde CPU) y Bus de **Control** (señales de temporización y sincronismo).
- **Jerarquía de Memoria y Direccionamiento:** Delimitación de velocidad/coste desde registros de CPU y cachés L1/L2/L3 hasta RAM y almacenamiento secundario.

---

## 📖 Desarrollo y Resumen

### 1. El Modelo Von Neumann y sus Componentes
El comportamiento funcional de la práctica totalidad de los ordenadores modernos se basa en cuatro bloques interconectados:
1. **Unidad Central de Proceso (CPU):**
   - **Unidad de Control (UC):** Interpreta las instrucciones binarias, genera las microórdenes hacia el resto de subsistemas y sincroniza los ciclos de reloj.
   - **Unidad Aritmético-Lógica (ALU):** Circuito combinacional/secuencial que ejecuta operaciones aritméticas (sumas, restas) y lógicas (AND, OR, desplazamientos a nivel de bit).
   - **Banco de Registros:** Celdas de memoria de acceso ultrarrápido integradas directamente en el silicio del procesador.
2. **Memoria Principal (RAM):** Espacio estructurado en palabras binarias indexadas por direcciones numéricas donde conviven instrucciones de código y datos.
3. **Sistema de Buses:** Conjunto de pistas conductoras paralelas encargadas del transporte de bits:
   - *Bus de Datos:* Transporta el contenido de las instrucciones o los operandos. Su ancho (64 bits en arquitecturas modernas) determina la palabra de datos transferible por ciclo.
   - *Bus de Direcciones:* Señaliza la celda física de memoria o el puerto de E/S al que se desea acceder. Con $n$ líneas de direcciones se puede direccionar un espacio de $2^n$ bytes.
   - *Bus de Control:* Líneas de señalización (lectura/escritura `R/W`, peticiones de interrupción `IRQ`, reloj `CLK`, reset).
4. **Subsistema de Entrada / Salida (E/S):** Módulos que interconectan la arquitectura central con periféricos externos.

### 2. El Ciclo de Instrucción Paso a Paso
El procesador ejecuta programas repitiendo de forma ininterrumpida este ciclo:
1. **Fase de Búsqueda (*Fetch*):**
   - El contenido del **Contador de Programa (PC)**, que apunta a la siguiente instrucción, se transfiere al **Registro de Direcciones de Memoria (MAR)**.
   - La UC activa la señal de lectura en el bus de control.
   - La palabra de memoria leída viaja por el bus de datos hacia el **Registro de Datos de Memoria (MDR)**.
   - El valor del MDR se transfiere al **Registro de Instrucción (IR)**.
   - El **PC** se incrementa automáticamente para apuntar a la siguiente dirección.
2. **Fase de Decodificación y Ejecución (*Decode & Execute*):**
   - El decodificador de la UC interpreta el código de operación (*OpCode*) presente en el IR.
   - Si la instrucción requiere operandos en memoria, se calculan las direcciones efectivas.
   - La UC activa las señales hacia la ALU para operar sobre los registros de trabajo (**Acumulador / Registros Generales**).
   - El resultado se almacena en el registro destino o se escribe en memoria principal.

---

## 💻 Código / Ejemplos

### Simulación en Pseudoensamblador del Ciclo Fetch-Execute
```assembly
; Simulación de instrucción: ADD R1, [0x0400] (Suma contenido de dirección a R1)

; 1. FETCH
MAR <- PC                     ; Carga dirección de la instrucción actual
READ_SIGNAL <- 1              ; Solicita lectura a memoria RAM
MDR <- MEMORY[MAR]            ; Llega el código de instrucción binario
IR <- MDR                     ; Guarda la instrucción en el Registro de Instrucción
PC <- PC + 4                  ; Incrementa el contador (instrucción de 32 bits)

; 2. DECODE
DECODE(IR.Opcode)             ; La UC detecta operación ADD con direccionamiento directo

; 3. EXECUTE
MAR <- IR.Address (0x0400)    ; Carga dirección del operando
READ_SIGNAL <- 1              ; Lee el operando en memoria
MDR <- MEMORY[MAR]            ; Llega el dato a sumar
ALU_OUT <- R1 + MDR           ; La ALU realiza la suma binaria
R1 <- ALU_OUT                 ; Almacena el resultado en el registro destino R1
FLAGS.Z <- (R1 == 0)          ; Actualiza flags de estado (Zero, Carry, Overflow)
```

### Script Bash: Inspección de Registros y Arquitectura CPU en Linux
```bash
# Inspección de las características de la CPU y buses
lscpu

# Comprobación de la memoria direccionable y arquitectura de 64 bits
grep -E "flags|address sizes" /proc/cpuinfo | head -n 2

# Verificación de la jerarquía de cachés de la CPU
lscpu --caches
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **El Cuello de Botella de Von Neumann (*Von Neumann Bottleneck*):** Dado que datos e instrucciones comparten el mismo bus de memoria, la CPU puede verse forzada a esperar transferencias de RAM a pesar de operar a gigahercios. Se mitiga mediante cachés multinivel (L1, L2, L3) y arquitectura Harvard modificada internamente en la CPU.
- **Confundir MAR y MDR:** El MAR **solo almacena direcciones** (hacia dónde apunta la operación), mientras que el MDR **almacena datos o instrucciones** (el contenido transferido).
- **Asumir que 32 bits y 64 bits solo definen el ancho de palabra:** Una arquitectura de 32 bits limita el bus de direcciones a $2^{32} \text{ bytes} = 4 \text{ GiB}$ de RAM física direccionable. Las arquitecturas de 64 bits eliminan esta restricción teórica permitiendo petabytes de memoria.
