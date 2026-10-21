# 03 - Ensamblado, Diagnosis y Mantenimiento de Equipos Microinformáticos

> **Fuente:** [https://campus.europaeducationgroup.es/courses/150547](https://campus.europaeducationgroup.es/courses/150547)  
> **Módulo:** Unidad 03. Ensamblado y diagnosis

## 📌 Conceptos Clave
- **Descarga Electrostática (ESD):** Transferencia súbita de carga estática capaz de destruir o degradar las uniones semiconductoras de circuitos integrados.
- **Protocolo de Montaje:** Orden lógico y estructurado para el ensamblado seguro de componentes evitando tensiones mecánicas en el PCB.
- **Compuesto Térmico (Pasta Térmica):** Relleno interfacial de microscópicas imperfecciones de aire entre el encapsulado de la CPU (IHS) y la base del disipador.
- **Secuencia POST (Power-On Self-Test):** Rutina diagnóstica inicial ejecutada por el firmware UEFI/BIOS al recibir alimentación.
- **Códigos de Diagnóstico:** Señalización acústica mediante el altavoz interno (*Beep Codes*), LEDs de estado en placa (*Debug LEDs: CPU, DRAM, VGA, BOOT*) y displays hexadecimales de 7 segmentos.
- **Mantenimiento Preventivo vs Correctivo:** Técnicas programadas de limpieza, sustitución térmica y monitorización frente a resolución de fallos sobrevenidos.

---

## 📖 Desarrollo y Resumen

### 1. Medidas de Protección ESD y Protocolo de Montaje
El voltaje estático imperceptible para el ser humano (< 2000 V) supera con creces el límite de tolerancia de los transistores CMOS (que operan a < 1.5 V).
- **Medidas preventivas:** Pulsera antiestática conectada a toma de tierra, alfombrilla conductora, manipulación de componentes por sus bordes plásticos o metálicos, evitar superficies textiles o moquetas.
- **Secuencia técnica de montaje:**
  1. Preparación de la placa base fuera del chasis sobre su caja protectora (nunca sobre la bolsa antiestática externa, que puede ser conductora).
  2. Instalación de la CPU respetando la muesca y el triángulo orientador en el pin 1.
  3. Colocación de los módulos RAM en sus ranuras preferentes (generalmente A2 y B2).
  4. Instalación de unidades SSD M.2 y fijación de sus disipadores térmicos.
  5. Montaje de la fuente de alimentación (PSU) en el chasis con el ventilador orientado a la rejilla de ventilación.
  6. Instalación del escudo trasero I/O Shield (si no viene preinstalado) y fijación de los separadores de latón en el chasis.
  7. Fijación de la placa base en el chasis y aplicación controlada de pasta térmica (método de gota central o distribución uniforme en cruz).
  8. Montaje del disipador/refrigeración líquida (AIO) y conexión del conector `CPU_FAN`.
  9. Cableado de alimentación: conector ATX 24 pines, EPS 12V 4/8 pines para CPU y alimentación PCIe para tarjeta gráfica.
  10. Conexión del panel frontal (*Front Panel*: Power SW, Reset SW, Power LED, HDD LED).

### 2. Diagnóstico del Proceso de Arranque (POST)
Al encender el equipo, la secuencia POST verifica en orden:
1. Suministro de voltajes estables desde la fuente de alimentación mediante la señal `Power Good`.
2. Inicialización y registro de la CPU.
3. Comprobación y conteo de la memoria RAM.
4. Detección del adaptador de vídeo principal (GPU).
5. Detección de dispositivos de almacenamiento y periféricos de entrada.
6. Transferencia del control al gestor de arranque (*Bootloader*) del sistema operativo.

### 3. Identificación y Resolución de Fallos
- **Fallo en CPU:** El equipo enciende ventiladores a máxima velocidad pero no emite vídeo; LED de depuración rojo fijo en `CPU`.
- **Fallo en RAM:** Reinicios cíclicos en bucle (*Boot loop*); pitidos largos recurrentes o LED naranja/amarillo en `DRAM`.
- **Fallo en GPU:** Un pitido largo seguido de 2 o 3 pitidos cortos; LED blanco en `VGA`.
- **Fallo de Almacenamiento/Arranque:** Mensaje `No bootable device found`; LED verde en `BOOT`.

---

## 💻 Código / Ejemplos

### Diagnóstico de Sensores Térmicos y Mantenimiento en Linux
```bash
# 1. Instalar y configurar sensores de hardware
sudo apt update && sudo apt install -y lm-sensors hddtemp smartmontools

# 2. Detectar sensores disponibles en placa base y CPU
sudo sensors-detect --auto

# 3. Monitorizar temperaturas, voltajes y RPM de ventiladores en tiempo real
watch -n 1 sensors

# 4. Inspección de errores críticos en los logs del kernel durante el POST
sudo dmesg -T --level=err,crit

# 5. Prueba de estabilidad de memoria RAM (comprobación de integridad)
sudo memtester 2048M 2
```

### Tabla de Códigos de Pitidos Estándar (BIOS AMI / UEFI Genérica):
| Secuencia de Pitidos | Subsistema Afectado | Causa Técnica Habitual |
| :--- | :--- | :--- |
| **1 pitido corto** | Sistema correcto | POST finalizado con éxito, arranque normal. |
| **Pitidos continuos** | Fuente / RAM | Fallo en suministro de energía o módulo mal encajado. |
| **1 largo, 1 corto** | Placa Base | Fallo de circuitos internos o BIOS corrupta. |
| **1 largo, 2 cortos** | Tarjeta de Vídeo | GPU mal insertada en ranura PCIe o fallo de VRAM. |
| **1 largo, 3 cortos** | Memoria convencional | Error de direccionamiento en los primeros 64 KB de RAM. |

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Olvidar los separadores de latón del chasis:** Atornillar la placa base directamente al chasis metálico produce un cortocircuito masivo que puede inutilizar la placa y la fuente de alimentación.
- **Exceso de pasta térmica:** Aplicar demasiada cantidad provoca que el compuesto desborde sobre el zócalo o los componentes SMD adyacentes; si la pasta contiene partículas metálicas conductoras, causará cortocircuitos.
- **Confundir el conector EPS de CPU (4+4 pines) con el PCIe de GPU (6+2 pines):** Aunque parezcan similares, tienen patillajes y polaridades de +12V y GND invertidas. Forzar su inserción dañará permanentemente el hardware.
- **No retirar el plástico protector del disipador:** Un error común en montajes nuevos es dejar la película plástica transparente sobre la base de cobre del disipador, provocando sobrecalentamiento instantáneo a > 100 °C.
