# 02 - Nivel Físico y Enlace de Datos: Medios de Transmisión, Ethernet y Conmutación

> **Fuente:** [https://campus.europaeducationgroup.es/courses/141418](https://campus.europaeducationgroup.es/courses/141418)  
> **Módulo:** Unidades 02, 03 y 04. Medios de transmisión, Nivel Físico y Nivel Enlace

## 📌 Conceptos Clave
- **Medios Guiados vs No Guiados:** Par trenzado de cobre (**UTP/STP/FTP**), **Fibra Óptica** (Monomodo y Multimodo) frente a ondas electromagnéticas de radiofrecuencia (Wi-Fi, Microondas).
- **Categorías de Cableado Estructurado:** Cat 5e (1 Gbps hasta 100 MHz), Cat 6 (1 Gbps / 10 Gbps a corta distancia, 250 MHz) y Cat 6A (10 Gbps a 100 m, 500 MHz).
- **Estándar IEEE 802.3 (Ethernet):** Formato formal de la trama Ethernet II: Preámbulo, MAC Destino (6B), MAC Origen (6B), EtherType (2B), Datos (46-1500B) y FCS/CRC (4B).
- **Mecanismos de Control de Acceso al Medio (CSMA/CD):** Detección de colisiones en dominios compartidos (histórico en Hubs / Half-Duplex).
- **Conmutación en Capa 2 (Switching):** Aprendizaje dinámico de direcciones MAC, tabla CAM/FDB y aislamiento de dominios de colisión.
- **Redes de Área Local Virtuales (VLAN - IEEE 802.1Q):** Segmentación lógica en Capa 2, puertos de acceso frente a puertos troncales (*Trunk*) y etiquetado de tramas (*Tagging*).

---

## 📖 Desarrollo y Resumen

### 1. Medios de Transmisión Físicos
- **Cable de Par Trenzado (Twisted Pair):** Ocho hilos de cobre trenzados por pares para cancelar interferencias electromagnéticas externas (ruido) y diafonía (*Crosstalk*). Límite de distancia horizontal según norma ANSI/TIA-568: **100 metros** (90 m de enlace permanente + 10 m de latiguillos).
- **Fibra Óptica:**
  - *Monomodo (SMF - Single Mode Fiber):* Núcleo microscópico (~9 µm) que transmite un único haz de luz láser en línea recta. Atenuación mínima, ideal para largas distancias (WAN y enlaces entre edificios de varios kilómetros).
  - *Multimodo (MMF - Multi Mode Fiber):* Núcleo mayor (50 o 62.5 µm) que utiliza emisores LED o VCSEL donde la luz viaja rebotando en múltiples trayectorias (modos). Más económica, utilizada en backbone de CPDs para distancias de hasta 300-500 m.

### 2. Anatomía de la Trama Ethernet II
```text
+-------------------+-------------+------------+-----------+----------------------+---------+
| Preámbulo + SFD   | MAC Destino | MAC Origen | EtherType | Datos / Carga Útil   | FCS/CRC |
| 7 bytes + 1 byte  | 6 bytes     | 6 bytes    | 2 bytes   | 46 a 1500 bytes      | 4 bytes |
+-------------------+-------------+------------+-----------+----------------------+---------+
```
- **Preámbulo y SFD (*Start Frame Delimiter*):** Patrón alterno de bits `10101010...10101011` que permite al receptor sincronizar su reloj de recepción.
- **Dirección MAC:** 48 bits (6 bytes) en hexadecimal. Los 3 primeros bytes representan el identificador del fabricante (**OUI**), y los 3 últimos el número de serie asignado.
- **EtherType:** Identifica el protocolo de capa superior (ej. `0x0800` para IPv4, `0x86DD` para IPv6, `0x0806` para ARP).

### 3. Conmutadores (Switches) y Segmentación VLAN
- **Funcionamiento del Switch:** Al recibir una trama por un puerto:
  1. Lee la **MAC Origen** y la asocia en su **tabla CAM** con el puerto físico por el que entró (aprendizaje).
  2. Lee la **MAC Destino**:
     - Si está en la tabla, reenvía la trama únicamente por el puerto correspondiente (*Forwarding* unicast).
     - Si no está en la tabla o es broadcast (`FF:FF:FF:FF:FF:FF`), inunda todos los puertos excepto el emisor (*Flooding*).
- **VLANs (IEEE 802.1Q):** Permiten dividir un único switch físico en múltiples dominios de broadcast lógicos aislados.
  - *Puerto de Acceso:* Conecta dispositivos finales (PCs, impresoras); envía y recibe tramas sin etiquetar.
  - *Puerto Trunk:* Conecta switches entre sí o con un router; inserta una cabecera de 4 bytes en la trama con el **VLAN ID (VID)** de 12 bits (soporta hasta 4094 VLANs).

---

## 💻 Código / Ejemplos

### Configuración de Interfaces y VLANs en Linux (CLI `ip` e `iproute2`)
```bash
# 1. Inspeccionar la tabla de resolución de direcciones MAC conocidas (Caché ARP)
ip neigh show

# 2. Crear una interfaz virtual VLAN (VLAN 10 para Servidores ASIR sobre interfaz física eth0)
sudo ip link add link eth0 name eth0.10 type vlan id 10

# 3. Asignar direccionamiento IP a la subinterfaz VLAN y levantar el enlace
sudo ip addr add 192.168.10.1/24 dev eth0.10
sudo ip link set dev eth0.10 up

# 4. Inspeccionar las interfaces VLAN activas
ip -d link show eth0.10
```

### Configuración Básica en Switch Cisco IOS:
```text
! Creación de VLANs en el conmutador
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name SERVIDORES_CPD
Switch(config-vlan)# vlan 20
Switch(config-vlan)# name GESTION_ADMIN

! Asignar puerto de acceso para un servidor
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# no shutdown

! Configurar enlace troncal (Trunk) hacia el Router
Switch(config)# interface GigabitEthernet0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
Switch(config-if)# no shutdown
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Bucles de Red en Capa 2 (*Broadcast Storms*):** Conectar dos cables entre los mismos switches sin el protocolo **Spanning Tree (STP - IEEE 802.1D/w)** genera tormentas de difusión infinitas que saturan la CPU de los conmutadores y colapsan la red en segundos. Mantén siempre activado STP/RSTP.
- **Discrepancia de Duplex (*Duplex Mismatch*):** Configurar un extremo en *Full-Duplex* forzado y el otro en *Auto-Negotiation* genera colisiones tardías (*Late Collisions*) y caídas dramáticas en el rendimiento de red.
- **Superar la longitud máxima de par trenzado (> 100 m):** Provoca atenuación excesiva y desincronización en la detección de colisiones o tramas, generando pérdidas de paquetes intermitentes.
