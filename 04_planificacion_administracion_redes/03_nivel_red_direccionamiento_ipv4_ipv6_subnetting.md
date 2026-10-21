# 03 - Nivel de Red: Direccionamiento IPv4, Subnetting VLSM y Transición a IPv6

> **Fuente:** [https://campus.europaeducationgroup.es/courses/141418](https://campus.europaeducationgroup.es/courses/141418)  
> **Módulo:** Unidad 05 y 06. Nivel de Red, direccionamiento, subnetting y administración de routers

## 📌 Conceptos Clave
- **Capa de Red (Network Layer):** Responsable del direccionamiento lógico jerárquico extremo a extremo y del encaminamiento de paquetes entre redes dispares.
- **Estructura de la Dirección IPv4:** 32 bits agrupados en 4 octetos en notación decimal con puntos (ej. `192.168.1.100`), dividida en porción de **Red (NetID)** y porción de **Host (HostID)**.
- **Máscara de Subred y Notación CIDR:** Prefijo de longitud de red (ej. `/24` equivale a `255.255.255.0`).
- **Direcciones Especiales en una Subred:**
  - **Dirección de Red:** Todos los bits de host a `0` (identifica la red en tablas de enrutamiento).
  - **Dirección de Difusión (Broadcast):** Todos los bits de host a `1` (destinada a todos los nodos de la subred).
  - **Hosts Útiles:** $2^h - 2$, donde $h$ es el número de bits destinados a la porción de host.
- **Subnetting con Máscara de Longitud Variable (VLSM):** Técnica para optimizar el direccionamiento jerárquico asignando máscaras adaptadas al tamaño real de cada departamento.
- **Direccionamiento IPv6:** 128 bits expresados en 8 bloques hexadecimales de 16 bits; elimina la necesidad de NAT gracias a su gigantesco espacio de direccionamiento ($2^{128}$).

---

## 📖 Desarrollo y Resumen

### 1. Clases Históricas y Rangos Privados (RFC 1918)
Para evitar el colapso del direccionamiento IPv4 público, el RFC 1918 reservó tres bloques no enrutables en Internet global para uso interno:
- **Clase A Privada:** `10.0.0.0/8` (de `10.0.0.0` a `10.255.255.255` - 16,777,214 hosts).
- **Clase B Privada:** `172.16.0.0/12` (de `172.16.0.0` a `172.31.255.255` - 1,048,574 hosts).
- **Clase C Privada:** `192.168.0.0/16` (de `192.168.0.0` a `192.168.255.255` - 65,534 hosts divididos comúnmente en subredes `/24`).

### 2. Metodología de Cálculo de Subredes (Subnetting)
Dado un bloque de red base (ej. `192.168.100.0/24`), para crear 4 subredes:
1. **Robar bits a la porción de host:** Para 4 subredes, se necesitan $2^n \ge 4 \implies n = 2 \text{ bits}$.
2. **Nueva máscara CIDR:** Máscara original ($/24$) $+ 2 \text{ bits} = /26$ (`255.255.255.192`).
3. **Bits de host restantes:** $32 - 26 = 6 \text{ bits}$.
4. **Capacidad por subred:**
   - Direcciones totales: $2^6 = 64$ direcciones (Salto de red = 64).
   - Hosts útiles reales: $2^6 - 2 = 62$ hosts.

#### Desglose de las 4 Subredes resultantes:
| Subred | Dirección de Red | Rango de IPs Útiles (Primer y Último Host) | Dirección de Broadcast | Máscara |
| :---: | :--- | :--- | :--- | :---: |
| **1** | `192.168.100.0` | `192.168.100.1` - `192.168.100.62` | `192.168.100.63` | `/26` |
| **2** | `192.168.100.64` | `192.168.100.65` - `192.168.100.126` | `192.168.100.127` | `/26` |
| **3** | `192.168.100.128` | `192.168.100.129` - `192.168.100.190` | `192.168.100.191` | `/26` |
| **4** | `192.168.100.192` | `192.168.100.193` - `192.168.100.254` | `192.168.100.255` | `/26` |

### 3. Fundamentos de IPv6
- **Estructura:** 128 bits (ej. `2001:0db8:85a3:0000:0000:8a2e:0370:7334`).
- **Reglas de Abreviación:**
  1. Omitir ceros a la izquierda en cualquier hexteto: `:0db8:` $\rightarrow$ `:db8:`, `:0000:` $\rightarrow$ `:0:`.
  2. Sustituir una única secuencia contigua de hextetos nulos por dos puntos dobles `::`: `2001:db8:85a3::8a2e:370:7334` (solo puede usarse una vez por dirección para evitar ambigüedad).
- **Tipos de direcciones:** Unicast Global (GUA), Link-Local (`fe80::/10`, autoconfigurada obligatoria en cada interfaz) y Multicast (`ff00::/8`). En IPv6 **no existe broadcast**; se sustituye por multicast optimizado.

---

## 💻 Código / Ejemplos

### Calculadora Rápida de Subredes en Python
```python
# subnetting_calculator.py
import ipaddress

def calcular_subred(cidr_str):
    red = ipaddress.IPv4Network(cidr_str, strict=False)
    print("========================================")
    print(f"ANÁLISIS DE RED: {cidr_str}")
    print("========================================")
    print(f"Dirección de Red:       {red.network_address}")
    print(f"Máscara de Subred:      {red.netmask} (/{red.prefixlen})")
    print(f"Wildcard (Inversa):     {red.hostmask}")
    print(f"Dirección de Broadcast: {red.broadcast_address}")
    print(f"Direcciones Totales:    {red.num_addresses}")
    print(f"Hosts Útiles Reales:    {red.num_addresses - 2}")
    
    hosts = list(red.hosts())
    if hosts:
        print(f"Primera IP útil:        {hosts[0]}")
        print(f"Última IP útil:         {hosts[-1]}")
    print("----------------------------------------\n")

# Pruebas con redes típicas
calcular_subred("192.168.1.0/24")
calcular_subred("10.0.0.0/26")
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Asignar la IP de Red o Broadcast a un Host:** Intentar configurar la IP `192.168.1.0` o `192.168.1.255` en una máquina bajo máscara `/24` será rechazado por el sistema operativo o generará fallos graves en la entrega de tramas.
- **Solapamiento de Subredes (*Subnet Overlap*):** Configurar dos interfaces de un router con rangos que coinciden parcialmente impedirá que la tabla de enrutamiento determine hacia dónde reenviar los paquetes.
- **No reservar enlaces punto a punto WAN con `/30` o `/31`:** Para interconectar dos routers directamente solo se requieren 2 direcciones útiles. Asignarles un rango `/24` desperdicia 252 direcciones IP innecesariamente.
