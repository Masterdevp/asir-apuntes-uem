# 02 - Componentes Físicos: Placa Base, Memoria RAM y Almacenamiento Secundario

> **Fuente:** [https://campus.europaeducationgroup.es/courses/150547](https://campus.europaeducationgroup.es/courses/150547)  
> **Módulo:** Unidad 02. Componentes físicos

## 📌 Conceptos Clave
- **Placa Base (Motherboard):** Plataforma central de circuito impreso (PCB) que interconecta todos los subsistemas.
- **Conjunto de Chips (Chipset):** Controladores integrados que gestionan el tráfico de alta y baja velocidad entre CPU, memoria y periféricos.
- **Factores de Forma:** Estándares dimensionales y térmicos (ATX, Micro-ATX, Mini-ITX).
- **Memoria RAM (DDR4 / DDR5):** Tecnologías de memoria volátil síncrona de doble velocidad de transferencia (*Double Data Rate*), buses de 64 bits por canal y soporte multicanal (*Dual/Quad Channel*).
- **Latencia CAS (CL) vs Frecuencia:** Relación entre el tiempo de acceso en ciclos de reloj y el ancho de banda efectivo.
- **Almacenamiento Secundario:** Comparativa técnica entre discos mecánicos magnéticos (**HDD**), unidades de estado sólido SATA (**SSD AHCI**) y unidades de alto rendimiento **NVMe PCIe**.

---

## 📖 Desarrollo y Resumen

### 1. La Placa Base y su Arquitectura
La placa base alberga los componentes esenciales de comunicación del ordenador:
- **Socket / Zócalo de la CPU:** Conexión matricial de pines con el procesador (estándares **LGA** con pines en placa y contactos planos en CPU, frente a **PGA** con pines en la propia CPU).
- **Chipset Moderno:** En arquitecturas contemporáneas, el antiguo puente norte (*Northbridge*) ha sido absorbido por la propia CPU (controlador de memoria integrado IMC y líneas PCIe principales para GPU). El puente sur (*Southbridge* o PCH de Intel / Promontory de AMD) gestiona periféricos secundarios: puertos SATA, USB, audio y controladoras de red.
- **Ranuras de Expansión PCIe:** Bus serie diferencial punto a punto escalable en líneas de transmisión ($x1, x4, x8, x16$). Cada generación (PCIe 3.0, 4.0, 5.0) duplica la tasa de transferencia por carril (hasta ~4 GB/s por carril en PCIe 5.0).
- **BIOS / UEFI:** Firmware almacenado en memoria flash ROM que inicializa el hardware mediante la rutina **POST** y proporciona soporte para arranque seguro (**Secure Boot**) y tablas de partición GPT.

### 2. Memoria Principal: DDR4 vs DDR5
- **DDR4:** Opera a 1.2 V con frecuencias típicas de 2133 a 3200 MT/s, con un bus único de 64 bits por módulo.
- **DDR5:** Reduce el voltaje a 1.1 V e incrementa las velocidades base de 4800 a 8400+ MT/s. Divide el módulo en **dos subcanales independientes de 32 bits** (más 8 bits de ECC integrado en el chip o *On-Die ECC*) e incorpora el circuito de gestión de energía (**PMIC**) en el propio módulo.
- **Arquitectura Dual-Channel:** Al poblar ranuras de memoria en canales emparejados, el ancho de banda del bus de datos pasa de 64 bits a 128 bits simultáneos, duplicando la tasa de transferencia teórica.

### 3. Almacenamiento Masivo: HDD, SATA y NVMe
| Característica | HDD (Magnético) | SSD SATA (AHCI) | SSD NVMe (PCIe M.2) |
| :--- | :--- | :--- | :--- |
| **Medio físico** | Platos magnéticos giratorios | Celdas NAND Flash | Celdas NAND Flash (3D TLC/QLC) |
| **Interfaz / Protocolo** | SATA III (AHCI) | SATA III (AHCI) | Bus PCIe (Protocolo NVMe) |
| **Velocidad máx. lectura** | ~150 - 250 MB/s | ~550 MB/s (límite SATA) | 3,500 - 14,000 MB/s (PCIe 4.0/5.0) |
| **IOPS (Operaciones por seg)** | ~75 - 150 IOPS | ~90,000 IOPS | 500,000 - 1,500,000+ IOPS |
| **Colas de comandos** | 1 cola de 32 comandos | 1 cola de 32 comandos | 64,000 colas de 64,000 comandos |
| **Latencia de acceso** | ~10 - 15 ms | ~0.1 ms | ~0.02 ms |

---

## 💻 Código / Ejemplos

### Diagnóstico de Hardware y Almacenamiento en Linux (CLI)
```bash
# 1. Información detallada de placa base, BIOS y memoria RAM
sudo dmidecode -t baseboard
sudo dmidecode -t bios
sudo dmidecode -t memory | grep -E "Size|Type|Speed|Manufacturer"

# 2. Listado de dispositivos de almacenamiento por interfaz y bus
lsblk -o NAME,SIZE,TYPE,FSTYPE,TRAN,MOUNTPOINT

# 3. Inspección del estado de salud SMART de discos NVMe y SATA
sudo smartctl -i /dev/nvme0n1
sudo smartctl -H /dev/nvme0n1

# 4. Benchmark rápido de lectura secuencial en disco
sudo hdparm -Tt /dev/sda
```

### Script de Diagnóstico en PowerShell (Windows)
```powershell
# Obtener información de placa base y módulos RAM
Get-CimInstance Win32_BaseBoard | Select-Object Manufacturer, Product, SerialNumber
Get-CimInstance Win32_PhysicalMemory | Select-Object DeviceLocator, Capacity, Speed, MemoryType

# Comprobar discos físicos, bus de conexión y salud operativa
Get-PhysicalDisk | Select-Object DeviceId, FriendlyName, MediaType, BusType, OperationalStatus, HealthStatus
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Instalación incorrecta de RAM en configuraciones Dual Channel:** En placas base de 4 ranuras (A1, A2, B1, B2), colocar módulos en ranuras contiguas (A1 y A2) desactiva el canal dual. Deben instalarse habitualmente en **A2 y B2** (ranuras 2 y 4) siguiendo el manual del fabricante.
- **Saturación térmica en discos NVMe:** Los discos SSD M.2 NVMe de altas prestaciones sufren estrangulamiento térmico (*Thermal Throttling*) si no cuentan con disipadores térmicos pasivos de aluminio en la placa.
- **Cuello de botella AHCI en SSD:** Utilizar adaptadores SATA para discos rápidos restringe el rendimiento a ~550 MB/s y una sola cola de 32 comandos, impidiendo paralelismo.
- **Alimentación inadecuada (Fuente de Alimentación / PSU):** No dimensionar la línea de +12V y el certificado de eficiencia energética (80 Plus Bronze/Gold) puede provocar inestabilidades críticas bajo carga sostenida.
