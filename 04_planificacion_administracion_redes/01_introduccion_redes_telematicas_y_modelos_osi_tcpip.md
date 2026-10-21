# 01 - Fundamentos de Redes Telemáticas y Modelos de Referencia (OSI y TCP/IP)

> **Fuente:** [https://campus.europaeducationgroup.es/courses/141418](https://campus.europaeducationgroup.es/courses/141418)  
> **Módulo:** Unidad 01. Redes telemáticas, estándares y protocolos

## 📌 Conceptos Clave
- **Red Telemática:** Conjunto de nodos de computación interconectados mediante enlaces físicos o inalámbricos para compartir recursos, servicios e información.
- **Clasificación por Alcance Geográfico:** PAN (Área Personal), LAN (Local), CAN (Campus), MAN (Metropolitana) y WAN (Área Extensa).
- **Topologías Físicas y Lógicas:** Estrella (conmutada por Switch), Bus (compartida con terminadores), Anillo y Malla completa/parcial.
- **Modelo OSI de ISO (7 Capas):** Marco teórico normativo estructurado en niveles: Física, Enlace de datos, Red, Transporte, Sesión, Presentación y Aplicación.
- **Pila de Protocolos TCP/IP (4 Capas):** Estándar práctico de facto en Internet: Acceso a la Red, Internet, Transporte y Aplicación.
- **Unidades de Datos de Protocolo (PDU) y Encapsulamiento:** Proceso jerárquico de adición de cabeceras (*Headers*) y colas (*Trailers*): Datos → Segmentos → Paquetes → Tramas → Bits.

---

## 📖 Desarrollo y Resumen

### 1. Clasificación y Topologías de Red
- **Topología en Estrella:** Todos los dispositivos finales se conectan a un nodo central de interconexión (Switch). La caída de un enlace individual solo aísla a ese equipo, siendo la arquitectura estándar en redes locales cableadas Ethernet.
- **Topología en Malla:** Cada nodo tiene múltiples enlaces punto a punto con otros nodos. Proporciona máxima redundancia y tolerancia a fallos, utilizada en el núcleo (*Core*) de redes WAN y centros de datos.

### 2. Comparativa entre el Modelo OSI y la Pila TCP/IP

| Nivel OSI | Nombre Capa OSI | Función Principal | Capa TCP/IP | PDU Asociada | Protocolos Representativos |
| :---: | :--- | :--- | :---: | :---: | :--- |
| **7** | **Aplicación** | Interfaz directa con el usuario y servicios de red | **Aplicación** | Datos | HTTP, HTTPS, DNS, DHCP, SSH, FTP |
| **6** | **Presentación** | Formateo, sintaxis, cifrado (TLS/SSL) y compresión | ^ | ^ | TLS, ASCII, JPEG |
| **5** | **Sesión** | Apertura, diálogo y sincronismo de sesiones | ^ | ^ | NetBIOS, RPC, Sockets |
| **4** | **Transporte** | Comunicación extremo a extremo y control de flujo | **Transporte** | **Segmento** (TCP) / Datagrama (UDP) | TCP, UDP |
| **3** | **Red** | Direccionamiento lógico global y enrutamiento | **Internet** | **Paquete** | IPv4, IPv6, ICMP, ARP, OSPF, BGP |
| **2** | **Enlace de Datos** | Direccionamiento físico local y control de acceso al medio | **Acceso a la Red** | **Trama (Frame)** | Ethernet (IEEE 802.3), Wi-Fi (802.11) |
| **1** | **Física** | Transmisión de señales eléctricas, ópticas o RF | ^ | **Bits** | Cable UTP (Cat 6), Fibra óptica, RJ-45 |

### 3. El Proceso de Encapsulamiento y Desencapsulamiento
1. **Emisor (Encapsulamiento - Capas superiores a inferiores):**
   - La capa de Aplicación genera el mensaje de **Datos**.
   - La capa de Transporte añade la cabecera con los **puertos origen y destino**, generando un **Segmento**.
   - La capa de Red añade las **direcciones IP origen y destino**, encapsulando el segmento en un **Paquete**.
   - La capa de Enlace añade las **direcciones MAC origen y destino** y un código de redundancia cíclica (**FCS/CRC**) para detección de errores, formando la **Trama**.
   - La capa Física modula la trama en una secuencia continua de **Bits** transmitidos por el cable o aire.
2. **Receptor (Desencapsulamiento):** Proceso inverso verificado capa a capa; cada nivel retira su cabecera y entrega la carga útil (*Payload*) al nivel superior.

---

## 💻 Código / Ejemplos

### Diagnóstico de Conectividad y Capas de Red en Linux (CLI)
```bash
# 1. Capa Física y Enlace: Comprobar estado de la interfaz de red, enlace y dirección MAC
ip link show eth0
ethtool eth0 | grep -E "Speed|Duplex|Link detected"

# 2. Capa de Red: Comprobar direccionamiento IP asignado y tabla de enrutamiento
ip -4 addr show eth0
ip route show

# 3. Capa de Red: Comprobar conectividad y latencia mediante ICMP
ping -c 4 8.8.8.8

# 4. Capa de Transporte: Monitorizar sockets abiertos, puertos a la escucha y conexiones activas
ss -tulpn

# 5. Capa de Aplicación: Diagnóstico y resolución de nombres de dominio (DNS)
dig +short A campus.europaeducationgroup.es
curl -I https://campus.europaeducationgroup.es
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Diagnóstico Desordenado (Troubleshooting caótico):** Cuando falla la red, comenzar probando aplicaciones o servidores web sin validar previamente las capas inferiores (¿hay enlace físico? ¿hay IP asignada por DHCP? ¿responde el gateway?) genera pérdidas críticas de tiempo. Aplica siempre la metodología de diagnóstico ascendente (*Bottom-Up*): Física → Enlace → Red → Transporte → Aplicación.
- **Confundir Dirección MAC con Dirección IP:** La dirección MAC identifica unívocamente la interfaz física dentro del mismo segmento local de Capa 2; no se propaga a través de routers. La dirección IP proporciona direccionamiento lógico global y enrutable en Capa 3.
- **Desconocer la MTU (Maximum Transmission Unit):** El tamaño máximo de trama estándar en Ethernet es de 1500 bytes. Paquetes que superen este valor con el bit DF (*Don't Fragment*) activado serán descartados por routers intermedios, provocando fallos de conexión intermitentes (*Black Hole Router*).
