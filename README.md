# Repositorio Técnico de Apuntes y Documentación: 1º ASIR

> **Titulación:** Técnico Superior en Administración de Sistemas Informáticos en Red (ASIR)  
> **Institución:** Universidad Europea de Madrid (Campus Virtual Canvas LMS)  
> **Ruta Local:** `/mnt/d/uem/` (Unidad `D:\uem\` en Windows)  
> **Fecha de Compilación:** Octubre 2026  
> **Nivel de Rigor:** Técnico, modular y aplicado con código, comandos de consola y buenas prácticas.

---

## 🧭 Índice Maestro de Asignaturas y Módulos

```
/mnt/d/uem/
├── 01_fundamentos_programacion/            # C#, .NET 8/10 SDK, VS Code y Ejercicios
├── 02_fundamentos_hardware/                # Arquitectura Von Neumann, Componentes, CPDs y Libro McGraw-Hill
├── 03_gestion_bases_datos/                 # Modelado E-R, SQL Relacional, NoSQL y Libro McGraw-Hill
├── 04_planificacion_administracion_redes/  # Modelo OSI, TCP/IP, Subnetting VLSM y Switching
├── 05_implantacion_sistemas_operativos/    # Arquitectura Kernel, Procesos, Memoria y Systemd
└── 06_itinerario_personal_empleabilidad_i/ # PRL en IT, Ergonomía PVD, Emergencias y PDP
```

---

### [Módulo 01: Fundamentos de Programación (ID: 147580)](./01_fundamentos_programacion/README.md)
*Enfoque en desarrollo de software de base mediante C# y la plataforma moderna .NET.*
- [01 - Introducción al Desarrollo con C# y Plataforma .NET](./01_fundamentos_programacion/01_introduccion_desarrollo_csharp.md)
  - *Algoritmos, ciclo de vida del software, metodologías (Cascada, Scrum, DevOps), arquitectura .NET (CLR, Roslyn, JIT, BCL).*
- [02 - Entorno de Desarrollo: Instalación de .NET SDK y Visual Studio Code](./01_fundamentos_programacion/02_entorno_instalacion_vscode_dotnet.md)
  - *Herramientas CLI `dotnet`, configuración de Visual Studio Code con C# Dev Kit y archivo `.csproj`.*
- [03 - Estructura Básica: Variables, Operadores, Métodos y Entrada/Salida](./01_fundamentos_programacion/03_estructura_variables_operadores_metodos.md)
  - *Tipado estático, ámbitos de variable, métodos modulares `static`, operadores con cortocircuito y conversión segura con `int.TryParse`.*
- [04 - Casos Prácticos y Ejercicios Resueltos: Tema 1](./04_ejercicios_practicos_tema1.md)
  - *Implementación y análisis de los scripts de clase: `01_Bienvenida.cs`, `02_Conversacion.cs` y `03_CartelModulo.cs`.*
- 📁 **Material descargado:** `material_original/` (4 PDFs) y `ejercicios/` (`ejercicios1_tema1.zip` + archivos `.cs` extraídos).

---

### [Módulo 02: Fundamentos de Hardware (ID: 150547)](./02_fundamentos_hardware/README.md)
*Arquitectura física del computador, subsistemas electrónicos, diagnóstico y centros de datos.*
- [01 - Arquitectura de Von Neumann y Elementos Funcionales del Procesador](./02_fundamentos_hardware/01_arquitectura_von_neumann_y_procesamiento.md)
  - *Modelo Von Neumann, CPU (UC, ALU, registros PC/IR/MAR/MDR), ciclo de instrucción Fetch-Decode-Execute y buses.*
- [02 - Componentes Físicos: Placa Base, Memoria RAM y Almacenamiento Secundario](./02_fundamentos_hardware/02_componentes_fisicos_placa_memoria_almacenamiento.md)
  - *Chipsets, zócalos LGA/PGA, DDR4 vs DDR5 Dual-Channel, comparativa HDD vs SSD SATA vs NVMe PCIe.*
- [03 - Ensamblado, Diagnosis y Mantenimiento de Equipos Microinformáticos](./02_fundamentos_hardware/03_ensamblado_diagnosis_y_mantenimiento.md)
  - *Prevención de descargas ESD, protocolo de montaje, secuencia POST, códigos de pitidos/LEDs y diagnóstico.*
- [04 - Respaldo, Recuperación y Creación de Imágenes de Sistema](./02_fundamentos_hardware/04_respaldo_recuperacion_y_creacion_imagenes.md)
  - *Estrategia de backup 3-2-1, copias completas/diferenciales/incrementales, niveles RAID (0, 1, 5, 10), RTO/RPO y clonación con `dd`.*
- [05 - Centros de Procesamiento de Datos (CPD), Estándares TIA-942 y Cloud Computing](./02_fundamentos_hardware/05_centros_de_procesamiento_de_datos_y_cloud.md)
  - *Niveles ANSI/TIA-942 TIER I a IV, métrica de eficiencia energética PUE, contención térmica y modelos IaaS/PaaS/SaaS.*
- 📁 **Material descargado:** Libro oficial completo de McGraw-Hill (`9788448626617_Fundamentos_de_Hardware.pdf`, 102 págs), guía docente y planificación.

---

### [Módulo 03: Gestión de Bases de Datos (ID: 143845)](./03_gestion_bases_datos/README.md)
*Diseño conceptual, modelado relacional, lenguaje SQL integral y bases de datos modernas en la nube.*
- [01 - Introducción a los Sistemas Gestores de Bases de Datos (SGBD)](./03_gestion_bases_datos/01_introduccion_sistemas_gestion_bases_datos.md)
  - *Ficheros planos vs SGBD, arquitectura ANSI/SPARC de 3 niveles, independencia física/lógica y propiedades transaccionales ACID.*
- [02 - Modelado Conceptual de Datos: Modelo Entidad / Relación (E-R) y Extendido](./03_gestion_bases_datos/02_modelado_conceptual_entidad_relacion.md)
  - *Entidades fuertes/débiles, atributos, cardinalidades (1:1, 1:N, N:M), jerarquías de herencia y diagramas Mermaid.*
- [03 - Modelo Relacional y Teoría de la Normalización (1FN a BCNF)](./03_gestion_bases_datos/03_modelo_relacional_y_normalizacion.md)
  - *Reglas formales de transformación E-R a tablas relacionales, integridad referencial y Formas Normales (1FN, 2FN, 3FN, BCNF).*
- [04 - Lenguaje SQL: Definición (DDL), Manipulación (DML), Consultas (DQL) y Transacciones (TCL)](./03_gestion_bases_datos/04_lenguaje_sql_ddl_dml_dql_tcl.md)
  - *Sintaxis formal SQL, restricciones de integridad, JOINs complejos, agregaciones `GROUP BY`/`HAVING` y control transaccional.*
- [05 - Bases de Datos en la Nube y Modelos NoSQL](./03_gestion_bases_datos/05_bases_datos_cloud_y_nosql.md)
  - *Teorema CAP, modelo BASE, bases de datos documentales (MongoDB), clave-valor (Redis), columnares y DBaaS.*
- 📁 **Material descargado:** Libro oficial completo de McGraw-Hill (`9788448626624_Gestion_de_Bases_de_Datos.pdf`, 150 págs).

---

### [Módulo 04: Planificación y Administración de Redes (ID: 141418)](./04_planificacion_administracion_redes/README.md)
*Infraestructuras de interconexión telemática, modelos de capas y direccionamiento lógico.*
- [01 - Fundamentos de Redes Telemáticas y Modelos de Referencia (OSI y TCP/IP)](./04_planificacion_administracion_redes/01_introduccion_redes_telematicas_y_modelos_osi_tcpip.md)
  - *Topologías, Modelo OSI (Capas 1-7), Pila TCP/IP, unidades PDU y encapsulamiento/desencapsulamiento.*
- [02 - Nivel Físico y Enlace de Datos: Medios de Transmisión, Ethernet y Conmutación](./04_planificacion_administracion_redes/02_medios_transmision_nivel_fisico_y_enlace.md)
  - *Cableado de par trenzado Cat 6/6A, fibra óptica monomodo/multimodo, formato de trama Ethernet II, conmutación y VLANs 802.1Q.*
- [03 - Nivel de Red: Direccionamiento IPv4, Subnetting VLSM y Transición a IPv6](./04_planificacion_administracion_redes/03_nivel_red_direccionamiento_ipv4_ipv6_subnetting.md)
  - *Rangos privados RFC 1918, cálculo matemático de subredes, máscaras CIDR, rangos de broadcast y direccionamiento IPv6.*
- [04 - Nivel de Transporte y Aplicación: Protocolos TCP, UDP y Servicios de Red](./04_planificacion_administracion_redes/04_nivel_transporte_tcp_udp_y_aplicacion.md)
  - *Espacio de puertos, Three-Way Handshake de TCP, comparativa TCP vs UDP, sockets y monitorización con `ss` y `tcpdump`.*
- 📁 **Material descargado:** Guía docente oficial, planificación de 1er trimestre y presentación de Sesión 1.

---

### [Módulo 05: Implantación de Sistemas Operativos (ID: 156443)](./05_implantacion_sistemas_operativos/README.md)
*Administración de software de base, núcleo del sistema, gestión de recursos y servicios.*
- [01 - Arquitectura y Fundamentos de los Sistemas Operativos](./05_implantacion_sistemas_operativos/01_arquitectura_y_fundamentos_sistemas_operativos.md)
  - *Funciones del SO, núcleo (monolítico, microkernel, híbrido), modos de CPU Ring 0/Ring 3 y llamadas al sistema (syscalls).*
- [02 - Gestión de Procesos, Hilos de Ejecución y Planificación de CPU](./05_implantacion_sistemas_operativos/02_gestion_de_procesos_hilos_y_planificacion.md)
  - *Bloque de control PCB, ciclo de vida de procesos, cambio de contexto, planificador CFS, señales del kernel (`kill`) y comandos CLI.*
- [03 - Gestión de Memoria Virtual y Sistemas de Archivos](./05_implantacion_sistemas_operativos/03_gestion_de_memoria_y_sistemas_de_archivos.md)
  - *Paginación, MMU, TLB, Swap, inodos en sistemas de archivos Linux (EXT4) y enlaces duros vs simbólicos.*
- [04 - Administración de Usuarios, Permisos POSIX, ACLs y Servicios con Systemd](./05_implantacion_sistemas_operativos/04_administracion_de_usuarios_permisos_y_servicios.md)
  - *Permisos rwx, permisos especiales SUID/SGID/Sticky bit, listas de control de acceso (ACLs) y unidades de servicio en systemd.*
- 📁 **Material descargado:** Guía docente oficial y planificación del 1er trimestre.

---

### [Módulo 06: Itinerario Personal para la Empleabilidad I (ID: 142878)](./06_itinerario_personal_empleabilidad_i/README.md)
*Seguridad y salud laboral en el sector IT, ergonomía frente a pantallas, emergencias y desarrollo profesional.*
- [01 - Prevención de Riesgos Laborales y Salud Laboral en el Sector Tecnológico (IT)](./06_itinerario_personal_empleabilidad_i/01_prevencion_riesgos_laborales_sector_it.md)
  - *Ley 31/1995 de PRL, riesgos en pantallas PVD (RD 488/1997), ergonomía, regla 20-20-20, riesgos eléctricos y tecnoestrés.*
- [02 - Gestión de la Prevención en la Empresa, Planes de Emergencia y Primeros Auxilios](./06_itinerario_personal_empleabilidad_i/02_gestion_de_la_prevencion_y_emergencias.md)
  - *Organización preventiva (SPP/SPA), evaluación de riesgos, clases de fuego, extintores para CPD ($CO_2$) y protocolo PAS.*
- [03 - Competencias Profesionales, Plan de Desarrollo Personal e Identidad Digital](./06_itinerario_personal_empleabilidad_i/03_competencias_profesionales_e_identidad_digital.md)
  - *Hard vs Soft Skills para administradores de sistemas, objetivos SMART, gestión estratégica de identidad digital y portafolio GitHub.*
- 📁 **Material descargado:** Guía docente oficial completa (21 páginas) y planificación del 1er trimestre.

---

## 🛠️ Instrucciones de Uso y Consulta
1. **Navegación:** Puedes explorar los archivos directamente abriendo este archivo raíz en cualquier visor Markdown o editor (como VS Code, Obsidian o GitHub). Todos los enlaces internos son relativos.
2. **Archivos Originales:** En cada subcarpeta `material_original/` se preservan los PDFs oficiales y libros de texto completos de McGraw-Hill sin modificar.
3. **Ejercicios Prácticos:** En `01_fundamentos_programacion/ejercicios/` se encuentran los archivos de código fuente de C# listos para compilar con `dotnet run`.
