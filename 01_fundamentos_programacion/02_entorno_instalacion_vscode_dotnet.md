# 02 - Entorno de Desarrollo: Instalación de .NET SDK y Visual Studio Code

> **Fuente:** [https://campus.europaeducationgroup.es/courses/147580](https://campus.europaeducationgroup.es/courses/147580)  
> **Módulo:** Unidad 1. Introducción al desarrollo con C#

## 📌 Conceptos Clave
- **.NET SDK (Software Development Kit):** Conjunto completo de herramientas de línea de comandos (`dotnet`), compiladores y bibliotecas para construir y probar aplicaciones.
- **.NET Runtime:** Componente mínimo necesario únicamente para ejecutar aplicaciones ya compiladas (sin herramientas de desarrollo).
- **Versiones LTS (Long Term Support):** Versiones con soporte extendido recomendadas para entornos de producción y formación académica estable.
- **Visual Studio Code:** Editor de código fuente ligero, modular y multiplataforma optimizado con extensiones.
- **C# Dev Kit:** Extensión oficial de Microsoft que integra el servidor de lenguaje OmniSharp/Roslyn, explorador de soluciones y soporte para depuración y pruebas unitarias.
- **Estructura del Proyecto .NET:** Archivo descriptor de compilación (`.csproj`) y archivo principal de código (`Program.cs`).

---

## 📖 Desarrollo y Resumen

### 1. Requisitos e Instalación del SDK
Para desarrollar aplicaciones en C# se requieren dos herramientas fundamentales:
1. **.NET SDK (versión recomendada LTS):** Descargado desde el portal oficial de Microsoft.
2. **Visual Studio Code:** Editor ligero con el paquete de extensiones **C# Dev Kit**.

### 2. Ciclo de Trabajo mediante la CLI `dotnet`
La interfaz de línea de comandos de .NET proporciona una gestión completa del ciclo de vida de los proyectos sin depender obligatoriamente de una interfaz gráfica pesada:

| Comando | Función Técnica |
| :--- | :--- |
| `dotnet --version` | Muestra la versión activa del SDK instalado. |
| `dotnet new console -o <Nombre>` | Genera la plantilla de una aplicación de consola en la carpeta indicada. |
| `dotnet build` | Compila el código fuente, comprueba errores y genera el ensamblado `.dll` en `bin/Debug/`. |
| `dotnet run` | Compila (si hubo cambios) y ejecuta inmediatamente el proyecto. |
| `dotnet clean` | Elimina artefactos temporales y binarios generados en `bin/` y `obj/`. |

### 3. Anatomía del archivo `.csproj`
El archivo `.csproj` es un archivo XML que define las propiedades de compilación de la aplicación:
- `<OutputType>Exe</OutputType>`: Especifica que el resultado será un ejecutable de consola.
- `<TargetFramework>netX.X</TargetFramework>`: Define la versión destino de la biblioteca base de .NET.
- `<ImplicitUsings>enable</ImplicitUsings>`: Incluye automáticamente namespaces comunes (como `System`, `System.Collections.Generic`, `System.IO`).
- `<Nullable>enable</Nullable>`: Activa el análisis de referencias nulas para prevenir excepciones `NullReferenceException`.

---

## 💻 Código / Ejemplos

### Procedimiento Completo por Consola (PowerShell / Bash / CMD):

```bash
# 1. Comprobar que el compilador .NET está accesible en el PATH
dotnet --version

# 2. Crear un nuevo proyecto de consola llamado "DiagnosticoRed"
dotnet new console -o DiagnosticoRed

# 3. Navegar al directorio generado
cd DiagnosticoRed

# 4. Inspeccionar la estructura de archivos creada
# DiagnosticoRed/
# ├── DiagnosticoRed.csproj
# ├── Program.cs
# └── obj/

# 5. Compilar y ejecutar la aplicación
dotnet run
```

### Configuración del archivo de proyecto (`DiagnosticoRed.csproj`):
```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>

</Project>
```

### Código Fuente Inicial (`Program.cs`):
```csharp
// Program.cs
namespace DiagnosticoRed
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Console.Title = "ASIR - Verificación de Entorno .NET";
            
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("==================================================");
            Console.WriteLine("   ENTORNO .NET SDK CONFIGURADO CORRECTAMENTE     ");
            Console.WriteLine("==================================================");
            Console.ResetColor();

            Console.WriteLine($"Fecha de sistema:    {DateTime.Now:dd/MM/yyyy HH:mm:ss}");
            Console.WriteLine($"Directorio de trabajo: {Environment.CurrentDirectory}");
            Console.WriteLine($"Procesadores lógicos:  {Environment.ProcessorCount}");
            
            Console.WriteLine("\nPresione ENTER para salir del entorno...");
            Console.ReadLine();
        }
    }
}
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Error `dotnet no se reconoce como un comando interno o externo`:** El instalador no agregó la ruta del SDK al `PATH` del sistema. Se debe reiniciar la terminal o reiniciar la sesión de usuario tras la instalación.
- **Ejecutar `dotnet run` fuera de la carpeta del proyecto:** `dotnet run` busca un archivo `.csproj` en el directorio actual. Si estás en la carpeta padre, arrojará el error `Couldn't find a project to run`. Usa `cd <NombreCarpeta>` antes de compilar.
- **Instalar únicamente el Runtime:** Si descargas solo el .NET Runtime podrás correr binarios pero carecerás del compilador `csc` y del comando `dotnet new`. Asegúrate de descargar **.NET SDK**.
- **Extensiones duplicadas en VS Code:** No instalar simultáneamente la extensión heredada *OmniSharp* y *C# Dev Kit*, ya que pueden colisionar en el servidor de lenguaje.
