# 01 - Introducción al Desarrollo con C# y Plataforma .NET

> **Fuente:** [https://campus.europaeducationgroup.es/courses/147580](https://campus.europaeducationgroup.es/courses/147580)  
> **Módulo:** Unidad 1. Introducción al desarrollo con C#

## 📌 Conceptos Clave
- **Algoritmo:** Conjunto ordenado y finito de instrucciones no ambiguas diseñadas para resolver un problema específico.
- **Ciclo de vida del software:** Fases secuenciales e iterativas (Análisis, Diseño, Implementación, Pruebas y Mantenimiento) que garantizan la viabilidad técnica y operativa.
- **Metodologías de desarrollo:** Comparativa entre modelos predictivos rígidos (**Cascada**) y adaptativos continuos (**Scrum** con sprints y roles definidos; **DevOps** enfocado en integración y despliegue continuo CI/CD).
- **Lenguajes de alto nivel vs bajo nivel:** Abstracción frente al hardware; C# como lenguaje tipado, orientado a objetos y gestionado.
- **Ecosistema .NET:** Plataforma multiplataforma unificada compuesta por el compilador **Roslyn**, la biblioteca de clases base (**BCL**), el tiempo de ejecución de lenguaje común (**CLR**) y el compilador en tiempo de ejecución (**JIT**).

---

## 📖 Desarrollo y Resumen

### 1. Fundamentos de la Programación y Algoritmos
La programación consiste en traducir la lógica de resolución de problemas a instrucciones ejecutables por un ordenador. Antes de codificar en cualquier lenguaje formal, el proceso técnico exige:
1. **Análisis del problema:** Identificación precisa de datos de entrada, restricciones y salidas esperadas.
2. **Diseño algorítmico:** Modelado formal de la lógica mediante pseudocódigo o diagramas de flujo. El pseudocódigo permite abstraerse de la sintaxis y centrarse exclusivamente en la corrección lógica.
3. **Implementación:** Traducción del pseudocódigo validado a sintaxis formal de un lenguaje de programación.

### 2. Metodologías de Desarrollo de Software
- **Modelo en Cascada (Waterfall):** Enfoque secuencial clásico. Cada fase (Requisitos → Diseño → Codificación → Pruebas → Despliegue) debe cerrarse formalmente antes de iniciar la siguiente. Carece de flexibilidad ante cambios sobre la marcha.
- **Metodología Scrum:** Marco ágil iterativo e incremental basado en periodos cerrados de trabajo llamados *Sprints* (habitualmente de 2 a 4 semanas). Roles fundamentales:
  - *Product Owner:* Representa al cliente y prioriza el *Product Backlog*.
  - *Scrum Master:* Facilita los procesos, elimina bloqueos y asegura el marco de trabajo.
  - *Equipo de Desarrollo:* Profesionales multidisciplinares autoorganizados.
- **Filosofía DevOps:** Integración cultural y tecnológica entre desarrollo (*Dev*) y operaciones de sistemas (*Ops*). Automatiza pruebas, integración continua (CI) y entrega continua (CD).

### 3. La Plataforma .NET y el Lenguaje C#
C# es un lenguaje multiparadigma (orientado a objetos, imperativo, funcional) desarrollado por Microsoft, estandarizado por ECMA e ISO.

#### Arquitectura de Ejecución en .NET:
1. **Código fuente (`.cs`):** El desarrollador escribe la lógica en archivos de texto plano.
2. **Compilador Roslyn:** Compila el código fuente C# a un lenguaje intermedio estándar llamado **CIL / MSIL** (*Common Intermediate Language*) encapsulado en un ensamblado (`.dll`).
3. **Common Language Runtime (CLR):** Es la máquina virtual de .NET encargada de:
   - Administrar la memoria automáticamente mediante el **Garbage Collector (GC)**.
   - Proporcionar seguridad de tipos y control de excepciones.
4. **Compilador JIT (Just-In-Time):** Justo en el momento de ejecutar la aplicación, el JIT traduce el CIL/MSIL a código máquina nativo específico de la arquitectura del procesador (x64, ARM64).

---

## 💻 Código / Ejemplos

### Estructura Tradicional vs Top-Level Statements en C#

#### 1. Sintaxis Tradicional (Estructura explícita de Clases y Métodos):
```csharp
// Espacio de nombres del sistema base
using System;

namespace FundamentosProgramacion
{
    internal class Program
    {
        // Punto de entrada principal de la aplicación
        static void Main(string[] args)
        {
            Console.WriteLine("Iniciando entorno .NET con arquitectura formal...");
            
            // Invocación a función modular
            string mensaje = ObtenerEstadoServicio("BaseDatos_Produccion", true);
            Console.WriteLine(mensaje);
        }

        static string ObtenerEstadoServicio(string nombreServicio, bool activo)
        {
            return $"[SERVICIO] {nombreServicio} -> Estado: {(activo ? "OPERATIVO" : "DETENIDO")}";
        }
    }
}
```

#### 2. Sintaxis Moderna con Top-Level Statements (Estándar .NET 6+ / C# 10+):
```csharp
// Top-level statements: el compilador genera automáticamente la clase Program y el método Main internamente.
Console.WriteLine("========================================");
Console.WriteLine("Sistema de Diagnóstico ASIR - Módulo C#");
Console.WriteLine("========================================");

string versionDotNet = Environment.Version.ToString();
string sistemaOperativo = Environment.OSVersion.ToString();

Console.WriteLine($"Versión de Runtime .NET: {versionDotNet}");
Console.WriteLine($"Plataforma Anfitriona:    {sistemaOperativo}");
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Confundir C# con .NET:** C# es el lenguaje de programación (sintaxis, palabras reservadas y gramática); .NET es el entorno de ejecución, bibliotecas (BCL) y herramientas SDK.
- **Omitir el análisis previo:** Escribir código directamente sin diseñar el flujo algorítmico conduce a errores de arquitectura difíciles de depurar.
- **Mezclar estilos en un mismo proyecto:** En versiones recientes de C#, si usas *Top-Level Statements*, no debes incluir manualmente la declaración `class Program` y `static void Main` en el mismo archivo raíz.
- **Asumir que C# es solo para Windows:** Desde la llegada de .NET Core (.NET moderno), C# se compila y ejecuta de manera nativa en Linux, macOS y contenedores Docker.
