# 01 - Introducción a los Sistemas Gestores de Bases de Datos (SGBD)

> **Fuente:** [https://campus.europaeducationgroup.es/courses/143845](https://campus.europaeducationgroup.es/courses/143845)  
> **Módulo:** Unidad 01. Introducción a las bases de datos

## 📌 Conceptos Clave
- **Ficheros Tradicionales vs SGBD:** Superación de problemas históricos de redundancia no controlada, inconsistencia, aislamiento de datos y dependencia física.
- **Sistema de Gestión de Bases de Datos (SGBD / DBMS):** Conjunto de programas que permite almacenar, modificar y extraer información de forma segura, concurrente e íntegra.
- **Arquitectura ANSI/X3/SPARC de 3 Niveles:** Nivel **Interno / Físico**, Nivel **Conceptual / Lógico** y Nivel **Externo / Vistas**.
- **Independencia de Datos:** Independencia **física** (modificar el almacenamiento sin alterar esquemas lógicos) e independencia **lógica** (alterar el esquema conceptual sin romper las vistas de usuario).
- **Propiedades ACID:** Pilares de las transacciones relacionales: **Atomicidad**, **Consistencia**, **Aislamiento** y **Durabilidad**.
- **Usuarios de la Base de Datos:** Administrador de Bases de Datos (**DBA**), Diseñadores, Desarrolladores de aplicaciones y Usuarios finales.

---

## 📖 Desarrollo y Resumen

### 1. Evolución: Del Procesamiento de Ficheros a los SGBD
Antes de los SGBD, las aplicaciones gestionaban datos en archivos planos propietarios (CSV, binarios, texto). Este enfoque presentaba graves deficiencias:
- *Redundancia e inconsistencia:* El mismo dato existía duplicado en varios ficheros con valores contradictorios.
- *Dificultad de acceso:* Cada nueva consulta requería programar una nueva rutina de lectura de disco.
- *Dependencia de datos:* Cualquier cambio en la estructura física del fichero obligaba a reescribir todos los programas asociados.
- *Problemas de concurrencia:* La modificación simultánea por dos procesos producía corrupción de datos (*Lost Updates*).
- *Vulnerabilidad de seguridad y fallos:* Ausencia de control de accesos granular y falta de recuperación ante caídas de corriente.

### 2. La Arquitectura ANSI/SPARC de Tres Niveles
Propuesta en 1975 para garantizar la **independencia de datos**:
1. **Nivel Físico o Interno:** Describe cómo se almacenan físicamente los datos en el disco (estructuras de bloques, punteros, índices B-Tree, algoritmos de compresión y cifrado).
2. **Nivel Conceptual o Lógico:** Estructura global de la base de datos libre de detalles físicos (qué tablas existen, qué columnas, qué restricciones de clave primaria y foránea aplican).
3. **Nivel Externo o de Vistas:** Subconjuntos de datos personalizados para diferentes usuarios o departamentos (ej. el departamento de ventas solo ve clientes y pedidos, pero no nóminas de empleados).

### 3. Las Propiedades ACID en Transacciones
Una transacción es una unidad lógica de trabajo indivisible:
- **A - Atomicidad (*Atomicity*):** Se ejecutan todas las operaciones de la transacción con éxito o no se ejecuta ninguna. Si falla a la mitad, se aplica `ROLLBACK` al estado inicial.
- **C - Consistencia (*Consistency*):** Una transacción solo puede llevar la base de datos de un estado válido a otro igualmente válido, respetando todas las reglas de integridad.
- **I - Aislamiento (*Isolation*):** Las operaciones concurrentes de múltiples transacciones no interfieren entre sí. Los niveles de aislamiento estándar son: *Read Uncommitted, Read Committed, Repeatable Read, Serializable*.
- **D - Durabilidad (*Durability*):** Una vez confirmada la transacción (`COMMIT`), los cambios son persistentes e irreversibles incluso ante fallos de hardware o caídas del sistema operativo.

---

## 💻 Código / Ejemplos

### Ejemplo Práctico de Transacción ACID en SQL (PostgreSQL / MySQL)
```sql
-- Demostración de Atomicidad y Consistencia: Transferencia bancaria entre dos cuentas
BEGIN TRANSACTION;

-- 1. Comprobar saldo y restar fondos de la cuenta origen
UPDATE CuentasBancarias
SET Saldo = Saldo - 500.00
WHERE NumeroCuenta = 'ES91-0001-0002-03' 
  AND Saldo >= 500.00;

-- 2. Incrementar fondos en la cuenta destino
UPDATE CuentasBancarias
SET Saldo = Saldo + 500.00
WHERE NumeroCuenta = 'ES91-0004-0005-06';

-- 3. Registrar movimiento en la auditoría contable
INSERT INTO RegistroMovimientos (CuentaOrigen, CuentaDestino, Importe, Fecha)
VALUES ('ES91-0001-0002-03', 'ES91-0004-0005-06', 500.00, CURRENT_TIMESTAMP);

-- Si no hay errores, se consolidan los cambios de forma duradera
COMMIT;

-- Si se produce cualquier fallo de restricción o excepción:
-- ROLLBACK;
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Ignorar el Aislamiento en Concurrencia:** Seleccionar niveles de aislamiento demasiado bajos (*Read Uncommitted*) provoca lecturas sucias (*Dirty Reads*), donde un proceso lee datos transitorios que luego son revertidos.
- **Tratar la base de datos como un simple almacén pasivo:** Desactivar claves foráneas o validaciones para gestionarlas exclusivamente por software en el backend genera inconsistencias cuando varios sistemas acceden a la misma base de datos.
- **Falta de planificación de copias del Transaction Log:** En motores relacionales (SQL Server, Oracle, PostgreSQL con WAL), los registros de transacciones crecen indefinidamente si no se configuran copias de seguridad periódicas del log.
