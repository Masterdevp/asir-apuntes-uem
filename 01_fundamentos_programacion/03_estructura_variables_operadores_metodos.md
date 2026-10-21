# 03 - Estructura Básica: Variables, Operadores, Métodos y Entrada/Salida

> **Fuente:** [https://campus.europaeducationgroup.es/courses/147580](https://campus.europaeducationgroup.es/courses/147580)  
> **Módulo:** Unidad 1. Introducción al desarrollo con C#

## 📌 Conceptos Clave
- **Tipado Estático y Fuerte:** Cada variable en C# posee un tipo de dato inmutable determinado en tiempo de compilación.
- **Tipos de Datos Primitivos:** Tipos por valor (`int`, `double`, `float`, `bool`, `char`) y tipos por referencia (`string`, `object`).
- **Ámbito de Variables (Scope):** Ámbito de clase (campos accesibles en toda la clase) frente a ámbito de bloque o local (restringido al interior de llaves `{ }`).
- **Firma de Métodos:** Declaración formal compuesta por modificador de acceso, modificador de estado (`static`), tipo de retorno, identificador y lista de parámetros.
- **Flujos de Entrada/Salida Estándar:** `Console.Write()`, `Console.WriteLine()`, `Console.ReadLine()` y parseo seguro de cadenas con `int.TryParse()`.
- **Operadores Aritméticos, Relacionales y Lógicos:** Evaluación booleana con cortocircuito (`&&`, `||`).

---

## 📖 Desarrollo y Resumen

### 1. Variables y Tipos de Datos Fundamentales
Una variable es una posición identificada en memoria donde se almacena un valor temporal durante la ejecución.

| Tipo C# | Tamaño | Rango / Descripción | Ejemplo |
| :--- | :--- | :--- | :--- |
| `int` | 32 bits (4 bytes) | Enteros de -2,147,483,648 a 2,147,483,647 | `int puerto = 8080;` |
| `long` | 64 bits (8 bytes) | Enteros de gran magnitud | `long bytesEnviados = 5000000000L;` |
| `double` | 64 bits (8 bytes) | Decimales de precisión estándar (15-17 dígitos) | `double latenciaMs = 14.85;` |
| `bool` | 8 bits (1 byte) | Valores booleanos lógicos | `bool conexionActiva = true;` |
| `char` | 16 bits (2 bytes) | Carácter Unicode individual | `char protocolo = 'U';` |
| `string` | Variable | Secuencia inmutable de caracteres Unicode | `string host = "192.168.1.1";` |

### 2. Ámbito y Ciclo de Vida de las Variables
- **Variables de Clase (Miembros / Campos):** Declaradas fuera de cualquier método. Existen mientras el objeto o la clase cargada permanezca en memoria.
- **Variables Locales (De bloque):** Creadas dentro de un método o estructura de control. Se destruyen al salir del bloque delimitador `{ }`.

### 3. Anatomía y Firma de Métodos
Un método encapsula una unidad reutilizable de lógica ejecutable.
```text
[ModificadorAcceso] [ModificadorStatic] [TipoRetorno] NombreMetodo([Tipo1 arg1, Tipo2 arg2, ...])
{
    // Bloque de instrucciones
    return [valor]; // Si TipoRetorno != void
}
```
En aplicaciones de consola procedurales iniciales, los métodos llamados desde `Main` deben incluir el modificador `static` para poder ejecutarse sin instanciar la clase.

### 4. Operadores en C#
- **Aritméticos:** Adición (`+`), sustracción (`-`), multiplicación (`*`), división (`/`), módulo o resto (`%`).
- **Asignación compuesta:** `+=`, `-=`, `*=`, `/=`, `%=`.
- **Relacionales:** Igualdad (`==`), desigualdad (`!=`), mayor/menor (`>`, `<`, `>=`, `<=`).
- **Lógicos con cortocircuito:**
  - `&&` (AND): Devuelve `true` solo si ambas expresiones son verdaderas. Si la primera es falsa, la segunda no se evalúa.
  - `||` (OR): Devuelve `true` si al menos una expresión es verdadera. Si la primera es verdadera, la segunda no se evalúa.
  - `!` (NOT): Invierte el valor de verdad.

---

## 💻 Código / Ejemplos

### Módulo Completo: Calculadora de Subredes y Métodos Auxiliares
```csharp
using System;

namespace AdministracionSistemas
{
    internal class Program
    {
        // Variable de ámbito de clase
        static string Modulo = "PLANIFICACIÓN Y ADMINISTRACIÓN DE REDES - C#";

        static void Main(string[] args)
        {
            Console.WriteLine($"=== {Modulo} ===");
            
            // Entrada de datos por consola
            Console.Write("Introduzca el número de hosts requeridos: ");
            string? entrada = Console.ReadLine();

            // Conversión segura de tipos
            if (int.TryParse(entrada, out int hostsDeseados))
            {
                int bitsNecesarios = CalcularBitsHost(hostsDeseados);
                int capacidadTotal = CalcularCapacidadSubred(bitsNecesarios);
                int hostsUtiles = capacidadTotal - 2; // Descuenta Red y Broadcast

                // Salida formateada con cadenas interpoladas ($"")
                Console.WriteLine("\n--- Resultados del Cálculo ---");
                Console.WriteLine($"Bits de Host requeridos:   {bitsNecesarios}");
                Console.WriteLine($"Direcciones totales:       {capacidadTotal}");
                Console.WriteLine($"Direcciones útiles reales: {hostsUtiles}");
                
                bool esViable = ValidarViabilidad(hostsDeseados, hostsUtiles);
                Console.WriteLine($"¿Subred viable y suficiente?: {esViable}");
            }
            else
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("[ERROR] La entrada introducida no es un entero válido.");
                Console.ResetColor();
            }
        }

        // Método estático con retorno entero y parámetro formal
        static int CalcularBitsHost(int hosts)
        {
            int bits = 1;
            while ((Math.Pow(2, bits) - 2) < hosts)
            {
                bits++;
            }
            return bits;
        }

        // Método aritmético con operador exponencial
        static int CalcularCapacidadSubred(int bitsHost)
        {
            return (int)Math.Pow(2, bitsHost);
        }

        // Método lógico con operadores relacionales
        static bool ValidarViabilidad(int requeridos, int utiles)
        {
            return (utiles >= requeridos) && (requeridos > 0);
        }
    }
}
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **División Entera Truncada:** En C#, la división entre dos enteros devuelve siempre un entero: `5 / 2` resulta en `2`, no `2.5`. Para obtener decimales, al menos uno de los operandos debe ser de tipo punto flotante: `5.0 / 2` o `(double)5 / 2`.
- **Uso arriesgado de `int.Parse()`:** `int.Parse(cadena)` lanzará una excepción no controlada (`FormatException`) si el usuario introduce letras o deja el campo vacío. Debe utilizarse siempre `int.TryParse()`.
- **Confusión de Ámbitos (Shadowing):** Declarar una variable local con el mismo nombre que una variable de clase oculta el campo exterior y puede provocar fallos lógicos sutiles.
- **Comparación estricta de cadenas:** No comparar cadenas ignorando mayúsculas/minúsculas sin usar `string.Equals(a, b, StringComparison.OrdinalIgnoreCase)`.
