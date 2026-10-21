# 02 - Gestión de la Prevención en la Empresa, Planes de Emergencia y Primeros Auxilios

> **Fuente:** [https://campus.europaeducationgroup.es/courses/142878](https://campus.europaeducationgroup.es/courses/142878)  
> **Módulo:** Unidad 2. La gestión de la prevención de riesgos laborales y emergencias

## 📌 Conceptos Clave
- **Plan de Prevención de Riesgos Laborales:** Instrumento formal de integración de la actividad preventiva en la estructura organizativa de la empresa.
- **Modalidades de Organización de la Prevención:**
  - Asunción personal por el propio empresario (empresas de hasta 10 trabajadores o hasta 25 con un solo centro, sin actividades del Anexo I).
  - Designación de trabajadores con capacitación formativa adecuada.
  - Servicio de Prevención Propio (**SPP**) obligatorio en empresas de > 500 trabajadores (o > 250 con actividades peligrosas).
  - Servicio de Prevención Ajeno (**SPA**) contratado externamente.
- **Plan de Autoprotección y Plan de Emergencia (RD 393/2007):** Procedimientos organizados para afrontar situaciones de incendio, escape, sismo o evacuación.
- **Sistemas de Extinción en Instalaciones Críticas (CPD):** Incompatibilidad del agua; uso de gases inertes (Inergen, Nitrógeno) y agentes limpios sintéticos (**Novec 1230**, **FM-200**) que extinguen por absorción de calor sin dañar el silicio ni generar residuos corrosivos.
- **Protocolo de Primeros Auxilios PAS:** **Proteger**, **Avisar** (112) y **Socorrer**.

---

## 📖 Desarrollo y Resumen

### 1. La Evaluación de Riesgos y Planificación Preventiva
La evaluación de riesgos (metodología simplificada del INSST) estima la magnitud de los riesgos que no hayan podido evitarse:
$$\text{Nivel de Riesgo (NR)} = \text{Probabilidad (Baja, Media, Alta)} \times \text{Consecuencias (Ligeramente Dañino, Dañino, Extremadamente Dañino)}$$
- *Riesgo Trivial o Tolerable:* No requiere acción específica o se resuelve con revisiones periódicas.
- *Riesgo Moderado:* Debe corregirse en un plazo determinado.
- *Riesgo Importante o Intolerable:* Requiere paralización inmediata de la actividad hasta que se adopten medidas correctoras eficaces.

### 2. Planes de Emergencia y Clases de Fuego
- **Clasificación de Tipos de Fuego según la norma UNE-EN 2:**
  - **Clase A:** Sólidos combustibles que forman brasas (madera, papel, cartón). Agente: Agua pulverizada, espuma.
  - **Clase B:** Líquidos inflamables o sólidos licuables (gasolina, aceites, plásticos). Agente: Espuma, polvo químico ABC, $CO_2$.
  - **Clase C:** Gases inflamables (propano, butano, gas natural). Agente: Polvo químico ABC.
  - **Clase D:** Metales combustibles (magnesio, sodio, titanio). Agente: Polvo específico para metales.
  - **Fuegos con presencia eléctrica (Equipos IT / Racks):** **PROHIBIDO EL USO DE AGUA A CHORRO**. Se debe emplear extintores portátiles de Dióxido de Carbono ($CO_2$) que no dejan residuos y desplazan el oxígeno, o sistemas fijos de inundación de gas limpio en salas de servidores.

### 3. Protocolo PAS de Primeros Auxilios
Ante un accidente laboral grave en la empresa:
1. **P - Proteger:** Garantizar que la zona sea segura para el socorrista y la víctima antes de intervenir (ej. si hay electrocución en una sala técnica, cortar el interruptor general antes de tocar al accidentado).
2. **A - Avisar:** Llamar de inmediato a los servicios de emergencia (**112**), indicando:
   - Ubicación exacta del centro y sala.
   - Qué ha ocurrido y número de víctimas.
   - Estado aparente (consciencia, respiración).
3. **S - Socorrer:** Aplicar el soporte vital básico evaluando consciencia y respiración:
   - *Si respira y está inconsciente:* Colocar en Posición Lateral de Seguridad (**PLS**).
   - *Si no respira:* Iniciar maniobra de Reanimación Cardiopulmonar (**RCP**) con 30 compresiones torácicas y 2 insuflaciones a ritmo de 100-120 lpm, solicitando un Desfibrilador Externo Automático (**DEA**).

---

## 💻 Código / Ejemplos

### Matriz de Evaluación de Riesgos Simplificada (INSST) en Formato Tabular

| Severidad \ Probabilidad | Baja (B) | Media (M) | Alta (A) |
| :--- | :---: | :---: | :---: |
| **Ligeramente Dañino (LD)** | Trivial | Tolerable | Moderado |
| **Dañino (D)** | Tolerable | Moderado | Importante |
| **Extremadamente Dañino (ED)** | Moderado | Importante | **Intolerable** |

### Checklist Automatizado de Inspección de Seguridad en CPD (Bash)
```bash
#!/bin/bash
# cpd_safety_audit.sh - Checklist de seguridad física y emergencias para CPD

echo "=================================================="
echo "    AUDITORÍA DE PREVENCIÓN Y EMERGENCIAS EN CPD  "
echo "=================================================="

puntos_comprobacion=(
  "Vías de evacuación y salidas de emergencia despejadas sin obstáculos"
  "Extintores de CO2 revisados con precinto intacto y retimbrado en fecha"
  "Sistema de detección temprana de humos (VESDA) activo sin alarmas"
  "Pulsadores de parada de emergencia eléctrica (EPO) accesibles y señalizados"
  "Sistema de extinción por gas limpio (Novec/FM200) en modo AUTOMÁTICO"
  "Luminarias de emergencia operativas tras prueba de corte de tensión"
  "Kit de primeros auxilios y botiquín dotado y con material no caducado"
)

for item in "${puntos_comprobacion[@]}"; do
    echo -n "[ ] $item ... "
    # Simulación de verificación interactiva
    echo "[OK]"
done

echo "--------------------------------------------------"
echo "Auditoría completada con éxito. Fecha: $(date +'%d/%m/%Y %H:%M')"
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Activar extintores de polvo químico ABC dentro de una sala de servidores:** El polvo químico sofoca el fuego pero es altamente corrosivo y microscópico; se adhiere a las placas electrónicas y ventiladores, destruyendo todos los servidores del CPD aunque el fuego fuera menor. Usa siempre $CO_2$ o gas inerte en salas técnicas.
- **Tocar a una persona electrocutada antes de desenergizar:** El cuerpo humano es conductor; intentar apartar a la víctima directamente sin cortar el interruptor de corriente o sin usar un pértiga aislante convierte al rescatador en una segunda víctima.
- **Obstruir salidas de evacuación con cajas de embalaje:** Acumular cajas de servidores vacías en pasillos o salidas de emergencia bloquea la evacuación rápida y constituye una infracción muy grave ante la Inspección de Trabajo.
