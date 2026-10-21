# 04 - Lenguaje SQL: Definición (DDL), Manipulación (DML), Consultas (DQL) y Transacciones (TCL)

> **Fuente:** [https://campus.europaeducationgroup.es/courses/143845](https://campus.europaeducationgroup.es/courses/143845)  
> **Módulo:** Unidad 06 a 09. SQL I-IV: Diseño físico, DDL, DQL, DML, TCL

## 📌 Conceptos Clave
- **SQL (Structured Query Language):** Lenguaje estándar declarativo para la gestión de bases de datos relacionales (ISO/IEC 9075).
- **Sublenguajes de SQL:**
  - **DDL (*Data Definition Language*):** Creación y modificación de estructuras de tablas, vistas e índices (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`).
  - **DQL (*Data Query Language*):** Recuperación y filtrado de datos (`SELECT`).
  - **DML (*Data Manipulation Language*):** Inserción, actualización y borrado de tuplas (`INSERT`, `UPDATE`, `DELETE`).
  - **TCL (*Transaction Control Language*):** Control de transacciones ACID (`COMMIT`, `ROLLBACK`, `SAVEPOINT`).
  - **DCL (*Data Control Language*):** Gestión de usuarios y permisos (`GRANT`, `REVOKE`).
- **Operaciones de Cruce (`JOIN`):** `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN` y `CROSS JOIN`.
- **Funciones de Agregación y Agrupamiento:** `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`, con cláusulas `GROUP BY` y filtros agregados con `HAVING`.

---

## 📖 Desarrollo y Resumen

### 1. Definición de Datos (DDL) y Restricciones de Integridad
El DDL permite materializar el modelo físico:
- `PRIMARY KEY`: Define la unicidad e indexación primaria (no admite nulos).
- `FOREIGN KEY ... REFERENCES`: Enlaza con otra tabla y define reglas referenciales (`ON DELETE CASCADE | SET NULL | RESTRICT`).
- `UNIQUE`: Asegura que no existan valores duplicados en la columna.
- `NOT NULL`: Impide la inserción de campos vacíos.
- `CHECK`: Valida que el dato cumpla una condición booleana específica (ej. `CHECK (Edad >= 18)`).
- `DEFAULT`: Asigna un valor predeterminado si no se especifica en la inserción.

### 2. Consultas Avanzadas y Tipología de JOINs (DQL)
- **`INNER JOIN`:** Devuelve únicamente las filas donde existe coincidencia exacta en la condición de unión de ambas tablas.
- **`LEFT JOIN`:** Devuelve todas las filas de la tabla izquierda y las filas coincidentes de la derecha; si no hay coincidencia, rellena los campos derechos con `NULL`.
- **`RIGHT JOIN`:** Equivalente inverso del `LEFT JOIN`.
- **`GROUP BY` vs `HAVING`:**
  - `WHERE` filtra registros **antes** de realizar cualquier agrupación.
  - `GROUP BY` colapsa las filas que comparten valores idénticos en columnas específicas.
  - `HAVING` filtra grupos de datos **después** de haber aplicado las funciones de agregación (ej. `HAVING COUNT(*) > 5`).

---

## 💻 Código / Ejemplos

### Script SQL Completo: Gestión de Infraestructura de Servidores ASIR
```sql
-- 1. CREACIÓN DE ESTRUCTURAS (DDL)
CREATE TABLE Racks (
    id_rack INT PRIMARY KEY AUTO_INCREMENT,
    codigo_rack VARCHAR(20) NOT NULL UNIQUE,
    sala_cpd VARCHAR(50) NOT NULL,
    unidades_u_totales INT NOT NULL DEFAULT 42 CHECK (unidades_u_totales > 0)
);

CREATE TABLE Servidores (
    id_servidor INT PRIMARY KEY AUTO_INCREMENT,
    hostname VARCHAR(100) NOT NULL UNIQUE,
    ip_gestion VARCHAR(15) NOT NULL UNIQUE,
    memoria_ram_gb INT NOT NULL CHECK (memoria_ram_gb >= 4),
    sistema_operativo VARCHAR(50) NOT NULL,
    id_rack INT NOT NULL,
    estado ENUM('Operativo', 'Mantenimiento', 'Apagado') DEFAULT 'Operativo',
    CONSTRAINT fk_servidores_rack FOREIGN KEY (id_rack)
        REFERENCES Racks(id_rack)
        ON DELETE RESTRICT
);

-- 2. INSERCIÓN DE DATOS (DML)
INSERT INTO Racks (codigo_rack, sala_cpd, unidades_u_totales) VALUES
('RACK-A01', 'Sala Principal TIER III', 42),
('RACK-B02', 'Sala Secundaria', 42);

INSERT INTO Servidores (hostname, ip_gestion, memoria_ram_gb, sistema_operativo, id_rack, estado) VALUES
('srv-ldap.asir.local', '192.168.10.5', 16, 'Debian 12', 1, 'Operativo'),
('srv-bbdd.asir.local', '192.168.10.10', 128, 'Ubuntu Server 24.04', 1, 'Operativo'),
('srv-web.asir.local',  '192.168.10.15', 32, 'Ubuntu Server 24.04', 1, 'Operativo'),
('srv-backup.asir.local','192.168.10.20', 64, 'Red Hat Enterprise Linux', 2, 'Mantenimiento');

-- 3. CONSULTAS COMPLEJAS (DQL)
-- Obtener inventario de servidores con su ubicación física y capacidad
SELECT 
    s.hostname,
    s.ip_gestion,
    s.memoria_ram_gb,
    s.sistema_operativo,
    r.codigo_rack,
    r.sala_cpd
FROM Servidores s
INNER JOIN Racks r ON s.id_rack = r.id_rack
WHERE s.estado = 'Operativo'
ORDER BY s.memoria_ram_gb DESC;

-- Consulta agregada: Consumo de RAM y conteo de servidores por Rack
SELECT 
    r.codigo_rack,
    COUNT(s.id_servidor) AS total_servidores,
    SUM(s.memoria_ram_gb) AS total_ram_instalada,
    AVG(s.memoria_ram_gb) AS media_ram_por_servidor
FROM Racks r
LEFT JOIN Servidores s ON r.id_rack = s.id_rack
GROUP BY r.id_rack, r.codigo_rack
HAVING COUNT(s.id_servidor) > 0;
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Omitir la cláusula `WHERE` en `UPDATE` o `DELETE`:** Ejecutar `DELETE FROM Servidores;` sin `WHERE` vaciará la tabla por completo. En entornos de producción, activa siempre el modo `safe-updates` o realiza transacciones con confirmación manual.
- **Uso inadecuado de `SELECT *`:** Solicitar todas las columnas en lugar de solo las requeridas satura el ancho de banda de red, degrada el rendimiento de la caché de buffer del motor e invalida el uso de índices de cobertura (*Covering Indexes*).
- **Confusión entre `WHERE` y `HAVING`:** Intentar filtrar por funciones agregadas en `WHERE` (ej. `WHERE COUNT(*) > 1`) arroja error de compilación SQL. Las condiciones sobre agregaciones pertenecen exclusivamente a `HAVING`.
- **Riesgo de Inyección SQL:** Concatenar cadenas de texto en sentencias SQL en lugar de utilizar **consultas parametrizadas (Prepared Statements)** expone la base de datos a ataques destructivos.
