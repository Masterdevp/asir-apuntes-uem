# 04 - Casos Prácticos y Ejercicios Resueltos: Tema 1

> **Fuente:** [https://campus.europaeducationgroup.es/courses/147580](https://campus.europaeducationgroup.es/courses/147580)  
> **Módulo:** Unidad 1. Recursos complementarios y actividades (ejercicios1_tema1.zip)

## 📌 Conceptos Clave
- **Interacción básica por terminal:** Diferenciación operativa entre `Console.Write` (sin salto de línea) y `Console.WriteLine` (con salto de línea final).
- **Pausa y captura síncrona:** Uso de `Console.ReadLine()` para detener la consola y capturar el flujo de datos del usuario hasta la pulsación de la tecla *Enter*.
- **Estructuración secuencial:** Flujo de ejecución lineal de instrucciones sin estructuras de control ramificadas.
- **Formateo y presentación visual:** Presentación clara de menús y mensajes informativos orientados a la experiencia del usuario (CLI UX).

---

## 📖 Desarrollo y Resumen

El primer bloque práctico de Fundamentos de Programación busca asentar el dominio del flujo de entrada y salida básica por terminal, la compilación de scripts independientes y la familiarización con la sintaxis de C#.

A continuación se analizan, refactorizan y documentan técnicamente los tres ejercicios oficiales extraídos del repositorio del curso:
1. **01_Bienvenida.cs:** Tarjeta de presentación e identificación del alumno con pausa de ejecución.
2. **02_Conversacion.cs:** Captura interactiva de múltiples datos por consola mediante pares *Prompt-Input*.
3. **03_CartelModulo.cs:** Diseño de cartelera informativa con alineación y delimitadores de texto para avisos técnicos.

---

## 💻 Código / Ejemplos

### Ejercicio 1: Tarjeta de Bienvenida (`01_Bienvenida.cs`)

```csharp
// 01_Bienvenida.cs
// Objetivo: Mostrar datos de bienvenida y mantener abierta la consola hasta interacción.
using System;

namespace EjerciciosTema1
{
    internal class Bienvenida
    {
        static void Main(string[] args)
        {
            Console.WriteLine("==================================================");
            Console.WriteLine("Bienvenido/a a Fundamentos de Programación");
            Console.WriteLine("Alumno/a: Marcelo Alix Dantas de Castro");
            Console.WriteLine("Estado: Mi primer programa en C# funciona correctamente");
            Console.WriteLine("==================================================");
            
            // Console.Write mantiene el cursor en la misma línea para la escritura
            Console.Write("Escribe cualquier palabra y pulsa Enter para continuar: ");
            string? entrada = Console.ReadLine();
            
            Console.WriteLine($"\nHas introducido: '{entrada}'. Finalizando ejecución.");
        }
    }
}
```

---

### Ejercicio 2: Conversación Interactiva (`02_Conversacion.cs`)

```csharp
// 02_Conversacion.cs
// Objetivo: Diálogo interactivo capturando múltiples entradas secuenciales.
using System;

namespace EjerciciosTema1
{
    internal class Conversacion
    {
        static void Main(string[] args)
        {
            Console.WriteLine("--- Módulo de Diagnóstico Interactivo ---");
            Console.WriteLine("Hola. Vamos a probar la entrada por consola del sistema.\n");

            Console.Write("¿Cómo te llamas? ");
            string? nombre = Console.ReadLine();

            Console.Write("¿En qué ciudad vives? ");
            string? ciudad = Console.ReadLine();

            Console.WriteLine("\n-------------------------------------------");
            Console.WriteLine($"Confirmación: Usuario {nombre} registrado desde {ciudad}.");
            Console.WriteLine("¡Perfecto! Hemos terminado la prueba con éxito.");
            Console.WriteLine("-------------------------------------------");
        }
    }
}
```

---

### Ejercicio 3: Cartelera Normativa del Módulo (`03_CartelModulo.cs`)

```csharp
// 03_CartelModulo.cs
// Objetivo: Imprimir cartelería estructurada con reglas de desarrollo y buenas prácticas.
using System;

namespace EjerciciosTema1
{
    internal class CartelModulo
    {
        static void Main(string[] args)
        {
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("╔═════════════════════════════════════════════════╗");
            Console.WriteLine("║         FUNDAMENTOS DE PROGRAMACIÓN - ASIR      ║");
            Console.WriteLine("╠═════════════════════════════════════════════════╣");
            Console.WriteLine("║  1. Practicar todos los días con código real.   ║");
            Console.WriteLine("║  2. Leer minuciosamente los mensajes de error.  ║");
            Console.WriteLine("║  3. Consultar y preguntar cuando algo no encaje.║");
            Console.WriteLine("║  4. Modularizar y documentar cada solución.     ║");
            Console.WriteLine("╚═════════════════════════════════════════════════╝");
            Console.ResetColor();

            Console.Write("\nPulsa Enter para terminar el programa...");
            Console.ReadLine();
        }
    }
}
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Cierre repentino de la ventana en ejecución directa:** Si ejecutas un ejecutable compilado mediante doble clic en Windows, la ventana de la terminal se cerrará instantáneamente al terminar. Terminar el programa con `Console.ReadLine()` o `Console.ReadKey()` evita este comportamiento.
- **Valores nulos en `Console.ReadLine()`:** En C# moderno con soporte de tipos nulos habilitado (`<Nullable>enable</Nullable>`), `Console.ReadLine()` devuelve `string?` (puede ser nulo si el flujo de entrada finaliza con EOF). Si asignas a `string` directo, el compilador emitirá la advertencia `CS8600`. Usa `string?` o el operador de fusión nula `?? string.Empty`.
