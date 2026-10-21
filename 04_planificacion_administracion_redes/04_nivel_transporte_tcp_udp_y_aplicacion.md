# 04 - Nivel de Transporte y Aplicación: Protocolos TCP, UDP y Servicios de Red

> **Fuente:** [https://campus.europaeducationgroup.es/courses/141418](https://campus.europaeducationgroup.es/courses/141418)  
> **Módulo:** Unidades 07 y 08. Nivel de Transporte, puertos y protocolos de aplicación

## 📌 Conceptos Clave
- **Capa de Transporte:** Proporciona comunicación lógica proceso a proceso extremo a extremo mediante el concepto de **Socket** (combinación única de `Dirección IP + Puerto`).
- **Espacio de Puertos (16 bits):** Rango de 0 a 65535 dividido en:
  - *Puertos Bien Conocidos (Well-Known Ports):* 0 a 1023 (HTTP 80, HTTPS 443, SSH 22, DNS 53).
  - *Puertos Registrados:* 1024 a 49151 (MySQL 3306, PostgreSQL 5432, RDP 3389).
  - *Puertos Dinámicos / Efímeros:* 49152 a 65535 (asignados por el cliente para el canal de respuesta).
- **Protocolo TCP (Transmission Control Protocol - RFC 793):** Orientado a la conexión, fiable, con retransmisión por confirmación (**ACK**), control de flujo por **ventana deslizante** y control de congestión.
- **Protocolo UDP (User Datagram Protocol - RFC 768):** No orientado a la conexión, no fiable (*Best-effort*), sin confirmación ni reordenación; mínima sobrecarga de cabecera (8 bytes frente a 20+ bytes de TCP).
- **Apretón de Manos de 3 Vías (Three-Way Handshake):** Secuencia SYN $\rightarrow$ SYN-ACK $\rightarrow$ ACK para establecer la conexión TCP.

---

## 📖 Desarrollo y Resumen

### 1. Comparativa Técnica: TCP vs UDP
| Parámetro | TCP | UDP |
| :--- | :--- | :--- |
| **Conexión** | Orientado a conexión previa | No orientado a conexión (Datagramas) |
| **Fiabilidad** | Entrega garantizada con ACKs y retransmisión | Sin garantía de entrega ni orden |
| **Cabecera** | 20 a 60 bytes | 8 bytes fijos |
| **Control de Flujo** | Sí (Ventana Deslizante / *Sliding Window*) | No |
| **Velocidad / Latencia** | Mayor latencia por sincronismo | Latencia mínima, tiempo real |
| **Casos de uso típicos** | Navegación web (HTTP/HTTPS), email (SMTP/IMAP), transferencia de ficheros (FTP), administración remota (SSH) | Streaming de audio/vídeo, VoIP, consultas DNS, videojuegos online, DHCP |

### 2. Establecimiento y Cierre de Conexión en TCP
- **Three-Way Handshake (Establecimiento):**
  1. **Cliente $\rightarrow$ Servidor (SYN):** El cliente envía un segmento con el flag `SYN=1` y un número de secuencia aleatorio inicial `ISN=X`.
  2. **Servidor $\rightarrow$ Cliente (SYN-ACK):** El servidor responde confirmando con `ACK=X+1`, activando su propio `SYN=1` con un número de secuencia propio `ISN=Y`.
  3. **Cliente $\rightarrow$ Servidor (ACK):** El cliente confirma la recepción enviando `ACK=Y+1`. A partir de este instante, el socket queda en estado `ESTABLISHED`.
- **Cierre de Conexión (Four-Way Teardown):**
  - Se utilizan los flags `FIN` y `ACK` en ambas direcciones (`FIN` $\rightarrow$ `ACK` $\rightarrow$ `FIN` $\rightarrow$ `ACK`) para garantizar que ningún dato quede pendiente de transmisión en los buffers.

---

## 💻 Código / Ejemplos

### Análisis de Tráfico de Transporte con `tcpdump` y `ss` en Linux
```bash
# 1. Comprobar puertos en escucha (Listening) con identificación de procesos
sudo ss -tulpn

# 2. Capturar el Three-Way Handshake de una conexión TCP hacia un servidor web
sudo tcpdump -i any -n "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0 and port 443" -c 10

# 3. Prueba de socket TCP mediante Netcat (comprobar si un puerto remoto está abierto)
nc -zv 192.168.10.10 3306

# 4. Servidor de eco TCP mínimo en Python:
```

```python
# servidor_echo_tcp.py
import socket

HOST = "0.0.0.0"
PORT = 9000

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as servidor:
    servidor.bind((HOST, PORT))
    servidor.listen(5)
    print(f"[*] Servidor TCP a la escucha en el puerto {PORT}...")
    
    while True:
        cliente, direccion = servidor.accept()
        print(f"[+] Conexión establecida desde: {direccion}")
        with cliente:
            datos = cliente.recv(1024)
            if not datos:
                break
            print(f"[RECIBIDO] {datos.decode('utf-8').strip()}")
            cliente.sendall(b"ECO_OK: " + datos)
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Agotamiento de Puertos Efímeros (*Port Exhaustion*):** Servidores de alta concurrencia que abren y cierran millones de conexiones TCP rápidamente acumulan sockets en estado `TIME_WAIT`, bloqueando la apertura de nuevas conexiones. Ajusta los parámetros de kernel `net.ipv4.tcp_tw_reuse`.
- **Elegir TCP para aplicaciones de tiempo real sensible a la latencia:** En streaming interactivo o videojuegos, una retransmisión de paquetes TCP causa congelamiento (*stuttering*). Es preferible perder un paquete no crítico en UDP que retrasar todo el flujo.
- **Ataques de SYN Flood:** Un atacante envía millones de peticiones `SYN` sin responder con el último `ACK`, saturando la tabla de conexiones pendientes del servidor. Se mitiga activando **SYN Cookies** en el kernel (`net.ipv4.tcp_syncookies = 1`).
