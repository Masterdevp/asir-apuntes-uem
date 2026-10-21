# 05 - Centros de Procesamiento de Datos (CPD), Estándares TIA-942 y Cloud Computing

> **Fuente:** [https://campus.europaeducationgroup.es/courses/150547](https://campus.europaeducationgroup.es/courses/150547)  
> **Módulo:** Unidad 05. Centros de procesos de datos. Arquitectura y estándares de cloud computing

## 📌 Conceptos Clave
- **Centro de Procesamiento de Datos (CPD / Data Center):** Instalación física especializada diseñada para albergar y gestionar infraestructuras críticas de computación, almacenamiento y red.
- **Estándar ANSI/TIA-942:** Clasificación de fiabilidad y redundancia en cuatro niveles o **TIER** (de TIER I a TIER IV).
- **Eficiencia Energética y Métrica PUE:** *Power Usage Effectiveness*, relación entre la energía total consumida por el CPD y la consumida estrictamente por los equipos IT.
- **Sistemas de Climatización:** Gestión térmica por contención de **pasillo frío / pasillo caliente**.
- **Modelos de Servicio Cloud:** Infraestructura como Servicio (**IaaS**), Plataforma como Servicio (**PaaS**) y Software como Servicio (**SaaS**).
- **Modelos de Despliegue Cloud:** Nube Pública, Privada, Híbrida y Multinube.

---

## 📖 Desarrollo y Resumen

### 1. Clasificación TIER según ANSI/TIA-942
El estándar internacional define los requisitos de disponibilidad, tolerancia a fallos y mantenimiento:

| Nivel TIER | Nombre / Tipo | Disponibilidad Anual | Tiempo Inactividad Máx. | Redundancia de Componentes |
| :--- | :--- | :--- | :--- | :--- |
| **TIER I** | Básico (No redundante) | 99.671% | 28.8 horas / año | $N$ (Sin redundancia, corte ante mantenimiento) |
| **TIER II** | Componentes redundantes | 99.741% | 22.0 horas / año | $N+1$ (SAI y generadores redundantes, 1 vía distribución) |
| **TIER III** | Mantenimiento simultáneo | 99.982% | 1.6 horas / año | $N+1$ (Mantenimiento sin interrumpir el servicio, 2 vías distribución) |
| **TIER IV** | Tolerante a fallos total | 99.995% | 26.3 minutos / año | $2(N+1)$ o $2N+1$ (Dos vías activas simultáneas, tolera cualquier fallo) |

### 2. Infraestructura Crítica y Climatización en CPD
- **Distribución Eléctrica:**
  - SAIs (*UPS - Uninterruptible Power Supply*) online de doble conversión para filtrar armónicos y absorber microcortes.
  - Grupos electrógenos diésel de arranque automático ante cortes de suministro de red pública.
  - Cuadros de distribución de energía en rack (ePDUs monitorizables por IP).
- **Gestión Térmica:**
  - Los servidores absorben aire frío por el frontal y expulsan aire caliente por la parte trasera.
  - La arquitectura de **pasillos fríos y calientes alternos** confinados con cerramientos evita que el aire caliente expulsado recircule hacia las tomas de aspiración de los servidores.
  - El índice **PUE** se calcula como:
    $$\text{PUE} = \frac{\text{Potencia Total Consumida por el CPD}}{\text{Potencia Consumida por los Equipos IT}}$$
    Un valor óptimo se aproxima a $1.1 - 1.2$, mientras que infraestructuras tradicionales ineficientes alcanzan $> 1.8$.

### 3. Modelos de Cloud Computing
- **IaaS (Infraestructura como Servicio):** El proveedor gestiona el hardware, almacenamiento, red y virtualización física. El cliente gestiona el sistema operativo, parches, middleware, runtime y aplicaciones (ej. Amazon EC2, Azure VMs, Google Compute Engine).
- **PaaS (Plataforma como Servicio):** El proveedor gestiona hardware, SO y runtime de ejecución. El desarrollador solo despliega su código y bases de datos (ej. AWS Elastic Beanstalk, Azure App Services, Heroku).
- **SaaS (Software como Servicio):** Solución de software completa lista para el usuario final gestionada al 100% por el proveedor (ej. Microsoft 365, Google Workspace, Canvas LMS).

---

## 💻 Código / Ejemplos

### Script Python: Cálculo de Disponibilidad y PUE de un CPD
```python
# calculo_metricas_cpd.py
def calcular_metricas_cpd(potencia_total_kw, potencia_it_kw, minutos_caida_anual):
    """
    Calcula el PUE y el porcentaje de disponibilidad de un CPD.
    """
    # 1. Cálculo de PUE
    pue = potencia_total_kw / potencia_it_kw
    
    # 2. Cálculo de Disponibilidad (8760 horas al año = 525600 minutos)
    minutos_totales_ano = 365 * 24 * 60
    minutos_operativos = minutos_totales_ano - minutos_caida_anual
    disponibilidad_pct = (minutos_operativos / minutos_totales_ano) * 100

    print("========================================")
    print("      MÉTRICAS TÉCNICAS DEL CPD         ")
    print("========================================")
    print(f"Potencia Total Instalada:   {potencia_total_kw:.2f} kW")
    print(f"Potencia Equipamiento IT:   {potencia_it_kw:.2f} kW")
    print(f"PUE (Eficiencia Energética): {pue:.3f}")
    
    if pue <= 1.2:
        print("  -> Calificación PUE: EXCELENTE (Alta eficiencia)")
    elif pue <= 1.5:
        print("  -> Calificación PUE: ACEPTABLE (Estándar)")
    else:
        print("  -> Calificación PUE: DEFICIENTE (Revisar refrigeración)")

    print(f"\nMinutos de inactividad:     {minutos_caida_anual} min/año")
    print(f"Disponibilidad resultante:  {disponibilidad_pct:.4f} %")
    
    # Determinar TIER equivalente
    if disponibilidad_pct >= 99.995:
        print("Cumple requisitos para:     TIER IV")
    elif disponibilidad_pct >= 99.982:
        print("Cumple requisitos para:     TIER III")
    elif disponibilidad_pct >= 99.741:
        print("Cumple requisitos para:     TIER II")
    else:
        print("Cumple requisitos para:     TIER I")

# Ejemplo de simulación
calcular_metricas_cpd(potencia_total_kw=150.0, potencia_it_kw=120.0, minutos_caida_anual=15)
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Mezcla de flujos de aire en racks:** Instalar un servidor con flujo de ventilación invertido dentro de un pasillo frío inyecta aire caliente a los equipos adyacentes, provocando puntos calientes (*Hotspots*) y apagados térmicos automáticos.
- **Confusión de responsabilidades en la nube (*Shared Responsibility Model*):** En IaaS, el proveedor no realiza copias de seguridad de los sistemas operativos ni aplica parches de seguridad a las máquinas virtuales del cliente; esa tarea recae por completo en el administrador de sistemas.
- **Sobredimensionamiento innecesario:** Contratar infraestructuras TIER IV para servicios no críticos dispara los costes de mantenimiento y energía de forma exponencial e injustificada.
