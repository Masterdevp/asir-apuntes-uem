# 05 - Bases de Datos en la Nube y Modelos NoSQL

> **Fuente:** [https://campus.europaeducationgroup.es/courses/143845](https://campus.europaeducationgroup.es/courses/143845)  
> **Módulo:** Unidad 10. Bases de datos en la nube: SQL y NoSQL

## 📌 Conceptos Clave
- **Teorema CAP (Eric Brewer, 2000):** En un sistema distribuido de datos solo es posible garantizar simultáneamente dos de tres propiedades: **Consistencia** (C), **Disponibilidad** (A) y **Tolerancia a Particiones** (P).
- **Modelo BASE vs ACID:** Paradigma de sistemas NoSQL: Básicamente Disponible (*Basically Available*), Estado blando (*Soft state*) y Consistencia eventual (*Eventual consistency*).
- **Taxonomía de Motores NoSQL:**
  - **Documentales:** Almacenan documentos semiestructurados JSON/BSON (ej. **MongoDB**, CouchDB).
  - **Clave-Valor:** Acceso de latencia ultrabaja en memoria RAM (ej. **Redis**, AWS DynamoDB).
  - **Columnares / Wide-Column:** Diseñadas para analítica masiva y alta tasa de escritura (ej. Apache **Cassandra**, HBase).
  - **Grafos:** Especializadas en redes complejas e interconexiones (ej. **Neo4j**).
- **Bases de Datos Gestionadas en Cloud (DBaaS):** Servicios administrados (Amazon RDS, Amazon Aurora, Azure SQL Database, Google Cloud SQL).

---

## 📖 Desarrollo y Resumen

### 1. El Teorema CAP y la Escalabilidad Horizontal
Los sistemas relacionales tradicionales priorizan la consistencia estricta mediante ACID, escalando típicamente de forma vertical (aumentando CPU, RAM y disco de un único servidor).

Ante volúmenes masivos de datos e internet global, las bases de datos distribuidas particionan los datos en múltiples nodos (*Sharding*):
- **Tolerancia a Particiones (P):** El sistema debe seguir funcionando aunque se caigan las comunicaciones entre dos nodos del cluster. En redes reales, las particiones son inevitables, por lo que la elección real en la nube es entre:
  - **Sistemas CP (Consistencia + Tolerancia a Partición):** Si se pierde la comunicación con un nodo, se bloquean las escrituras para evitar inconsistencias (ej. MongoDB, HBase).
  - **Sistemas AP (Disponibilidad + Tolerancia a Partición):** Todos los nodos siguen respondiendo a lecturas y escrituras, asumiendo que los datos convergerán posteriormente mediante consistencia eventual (ej. Apache Cassandra, CouchDB).

### 2. Comparativa entre Modelos SQL y NoSQL
| Dimensión | Bases de Datos Relacionales (SQL) | Bases de Datos NoSQL |
| :--- | :--- | :--- |
| **Esquema** | Estricto, predefinido y rígido (*Schema-on-write*) | Dinámico, flexible y adaptable (*Schema-on-read*) |
| **Escalabilidad** | Principalmente Vertical (Scale-up) | Horizontal y distribuida nativa (Scale-out) |
| **Modelo transaccional** | ACID estricto | BASE / Consistencia Eventual |
| **Relaciones / JOINs** | Soportados nativamente con gran eficiencia | Evitados; se prioriza la desnormalización o embebido |
| **Casos de uso típicos** | Sistemas ERP, banca, inventario transaccional | Big Data, telemetría IoT, catálogos e-commerce, sesiones |

---

## 💻 Código / Ejemplos

### 1. Modelo Documental con MongoDB (Colecciones y Documentos BSON/JSON):
```javascript
// Conexión e inserción de un documento semiestructurado en MongoDB
db.servidores.insertOne({
  hostname: "srv-cloud-01.asir.local",
  ip_gestion: "10.0.1.50",
  hardware: {
    cpu_cores: 16,
    ram_gb: 64,
    almacenamiento: [
      { tipo: "NVMe", tamano_gb: 512, rol: "SO" },
      { tipo: "SSD_SATA", tamano_gb: 2048, rol: "Datos" }
    ]
  },
  servicios_activos: ["docker", "nginx", "prometheus"],
  activo: true,
  fecha_puesta_marcha: new Date("2026-10-01T08:00:00Z")
});

// Consulta indexada filtrando por campos anidados
db.servidores.find({
  "hardware.ram_gb": { $gte: 32 },
  "servicios_activos": "nginx"
}, { hostname: 1, ip_gestion: 1, _id: 0 });
```

### 2. Modelo Clave-Valor con Redis (Caché de sesiones en memoria):
```bash
# Operaciones básicas en CLI de Redis
# 1. Establecer sesión de usuario con expiración automática (TTL de 3600 segundos)
SET user:session:1042 "{\"usuario\":\"master\",\"rol\":\"sysadmin\",\"ip\":\"192.168.1.100\"}" EX 3600

# 2. Recuperar la sesión en microsegundos
GET user:session:1042

# 3. Métricas de contadores atómicos en tiempo real
INCR peticiones:servidor:web01
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Usar NoSQL cuando el negocio exige integridad referencial estricta:** Intentar implementar relaciones complejas simulando `JOIN`s manuales por software en MongoDB degrada el rendimiento de forma crítica. Si tu modelo son entidades fuertemente acopladas con transacciones bancarias, usa un SGBD relacional.
- **Ignorar el costo de lectura en bases de datos Cloud (DynamoDB / CosmosDB):** Los servicios DBaaS serverless facturan por unidades de lectura/escritura (RCU/WCU). Un mal diseño de clave de partición generará nodos calientes (*Hot Partitions*) disparando la factura mensual.
- **Falta de índices en colecciones documentales:** Aunque NoSQL no exija esquema rígido, no crear índices en los campos más consultados obligará a recorrer la colección completa (*Full Collection Scan*).
