# 03 - Modelo Relacional y Teoría de la Normalización (1FN a BCNF)

> **Fuente:** [https://campus.europaeducationgroup.es/courses/143845](https://campus.europaeducationgroup.es/courses/143845)  
> **Módulo:** Unidad 04 y 05. Modelo relacional y Normalización

## 📌 Conceptos Clave
- **Modelo Relacional (Edgar F. Codd, 1970):** Estructura lógica basada en tablas formadas por tuplas (filas) y atributos (columnas), fundamentada en la teoría matemática de conjuntos y lógica de predicados.
- **Reglas de Transformación E-R a Relacional:** Reglas deterministas para convertir entidades, relaciones $1:1$, $1:N$, $N:M$ y jerarquías en esquemas de tablas con claves primarias (**PK**) y foráneas (**FK**).
- **Integridad Referencial:** Restricción que garantiza que el valor de una clave foránea debe coincidir exactamente con una clave primaria existente en la tabla padre o ser nulo.
- **Dependencia Funcional ($X \rightarrow Y$):** Propiedad semántica donde el valor de un conjunto de atributos $X$ determina de manera unívoca el valor de $Y$.
- **Formas Normales:** Proceso formal de descomposición sin pérdida de información para eliminar redundancias y anomalías de inserción, borrado y modificación: **1FN**, **2FN**, **3FN** y **Forma Normal de Boyce-Codd (BCNF)**.

---

## 📖 Desarrollo y Resumen

### 1. Reglas de Transformación del Modelo E-R al Relacional
1. **Entidades:** Cada entidad fuerte se transforma en una tabla independiente; su clave primaria se convierte en la PK de la tabla.
2. **Relaciones $1:N$:** La clave primaria del lado "1" se propaga como **clave foránea (FK)** a la tabla del lado "N".
3. **Relaciones $N:M$:** Se genera una **nueva tabla intermedia** cuya PK está compuesta por la concatenación de las PKs de ambas entidades participantes (que actúan individualmente como FKs), más los atributos propios de la relación si los hubiera.
4. **Relaciones $1:1$:** Se propaga la PK de cualquiera de las dos tablas a la otra como FK con restricción `UNIQUE`, priorizando la entidad con participación obligatoria.

### 2. Formas Normales Paso a Paso

#### Primera Forma Normal (1FN):
- **Requisito:** Todos los atributos deben ser **atómicos** (indivisibles). No se permiten grupos repetitivos ni atributos multivaluados en una misma celda.
- *Ejemplo de corrección:* Si un cliente tiene múltiples teléfonos separados por comas en un campo, se descompone creando una tupla por cada teléfono o una tabla subordinada `TelefonosCliente(idCliente, Telefono)`.

#### Segunda Forma Normal (2FN):
- **Requisito:** Estar en 1FN y que **ningún atributo no clave dependa parcialmente de una clave primaria compuesta**. Todos los atributos no primarios deben tener dependencia funcional completa de la PK completa.
- *Ejemplo de corrección:* En una tabla `DetalleFactura(idFactura, idArticulo, Cantidad, DescripcionArticulo)`, `DescripcionArticulo` depende únicamente de `idArticulo`, no de toda la clave compuesta `(idFactura, idArticulo)`. Se debe escindir en la tabla `Articulo`.

#### Tercera Forma Normal (3FN):
- **Requisito:** Estar en 2FN y que **ningún atributo no clave dependa transitivamente de la clave primaria** ($X \rightarrow Y$ y $Y \rightarrow Z$, por tanto $X \rightarrow Z$ a través de un campo no clave).
- *Ejemplo de corrección:* En `Empleado(idEmpleado, Nombre, idDepto, NombreDepto)`, `NombreDepto` depende funcionalmente de `idDepto`, que a su vez depende de `idEmpleado`. Se debe aislar en la tabla `Departamento`.

#### Forma Normal de Boyce-Codd (BCNF):
- **Requisito:** Toda determinante de una dependencia funcional debe ser una **superclave candidata**. Resuelve casos especiales de tablas en 3FN con múltiples claves candidatas compuestas y solapadas.

---

## 💻 Código / Ejemplos

### Esquema Relacional Normalizado en SQL (3FN):
```sql
-- 1. Tabla en 3FN: Catálogo de Departamentos
CREATE TABLE Departamento (
    id_depto INT PRIMARY KEY AUTO_INCREMENT,
    nombre_depto VARCHAR(100) NOT NULL UNIQUE,
    presupuesto DECIMAL(12, 2) NOT NULL DEFAULT 0.00
);

-- 2. Tabla en 3FN: Empleados con Clave Foránea (1:N)
CREATE TABLE Empleado (
    id_empleado INT PRIMARY KEY AUTO_INCREMENT,
    dni VARCHAR(9) NOT NULL UNIQUE,
    nombre VARCHAR(50) NOT NULL,
    apellidos VARCHAR(100) NOT NULL,
    id_depto INT NOT NULL,
    CONSTRAINT fk_empleado_depto FOREIGN KEY (id_depto) 
        REFERENCES Departamento(id_depto)
        ON DELETE RESTRICT 
        ON UPDATE CASCADE
);

-- 3. Tabla intermedia en 3FN para Relación N:M: Proyectos y Empleados
CREATE TABLE Proyecto (
    id_proyecto INT PRIMARY KEY AUTO_INCREMENT,
    nombre_proyecto VARCHAR(100) NOT NULL
);

CREATE TABLE AsignacionProyecto (
    id_empleado INT NOT NULL,
    id_proyecto INT NOT NULL,
    horas_asignadas INT NOT NULL CHECK (horas_asignadas > 0),
    PRIMARY KEY (id_empleado, id_proyecto),
    FOREIGN KEY (id_empleado) REFERENCES Empleado(id_empleado) ON DELETE CASCADE,
    FOREIGN KEY (id_proyecto) REFERENCES Proyecto(id_proyecto) ON DELETE CASCADE
);
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Anomalías de Modificación por Falta de Normalización:** Si los datos están desnormalizados sin justificación, actualizar el nombre de un departamento en un registro y olvidar otro dejará la base de datos en un estado inconsistente.
- **Desnormalización prematura:** Desnormalizar tablas (fusionar tablas normalizadas para evitar `JOIN`s) solo debe realizarse tras pruebas empíricas de rendimiento en almacenes de datos (*Data Warehouses* / OLAP), nunca en sistemas transaccionales (OLTP).
- **Acciones en cascada peligrosas (`ON DELETE CASCADE`):** Configurar borrado en cascada en relaciones críticas puede provocar que al eliminar un departamento se borren automáticamente todos los empleados y tickets históricos sin advertencia.
