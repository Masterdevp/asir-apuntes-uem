# 01 - Prevención de Riesgos Laborales y Salud Laboral en el Sector Tecnológico (IT)

> **Fuente:** [https://campus.europaeducationgroup.es/courses/142878](https://campus.europaeducationgroup.es/courses/142878)  
> **Módulo:** Unidad 1. La prevención de riesgos laborales y la salud laboral

## 📌 Conceptos Clave
- **Marco Normativo Central:** Ley 31/1995 de Prevención de Riesgos Laborales (LPRL) y Real Decreto 39/1997 (Reglamento de los Servicios de Prevención).
- **Diferenciación Conceptual:**
  - **Peligro:** Propiedad o aptitud intrínseca de algo para ocasionar daños (ej. un cable de red deshilachado con corriente).
  - **Riesgo Laboral:** Probabilidad combinada con la severidad de que un trabajador sufra un daño derivado del trabajo.
  - **Daños derivados del trabajo:** Accidente de trabajo (suceso repentino en jornada o *in itinere*) y Enfermedad profesional (deterioro paulatino por agentes reconocidos en el cuadro legal).
- **Riesgos Específicos del Administrador de Sistemas (ASIR):**
  - **Pantallas de Visualización de Datos (PVD - RD 488/1997):** Fatiga visual (astenopía) y sequedad ocular.
  - **Trastornos Musculoesqueléticos (TME):** Síndrome del túnel carpiano, cervicalgias y lumbalgias por sedentarismo prolongado.
  - **Riesgos Eléctricos y en CPD:** Contactos directos e indirectos en racks de servidores, atrapamientos y manipulación manual de cargas al montar servidores pesados (2U/4U).
  - **Riesgos Psicosociales:** Síndrome de *Burnout* (desgaste profesional), estrés laboral y tecnoestrés derivado de guardias y resolución de incidencias críticas 24/7.

---

## 📖 Desarrollo y Resumen

### 1. El Marco Legal y Derechos/Deberes
La Ley 31/1995 establece un marco de corresponsabilidad preventiva:
- **Deberes del Empresario:** Garantizar la seguridad y salud integral de los trabajadores mediante la integración de la prevención, evaluación continua de riesgos, adopción de medidas preventivas, formación e información a la plantilla y vigilancia de la salud periódica.
- **Obligaciones del Trabajador:** Usar adecuadamente las máquinas, herramientas y equipos de protección individual (**EPI**); cooperar con la empresa y comunicar de inmediato situaciones que entrañen riesgo inminente.

### 2. Ergonomía en Puestos con Pantallas de Visualización de Datos (PVD)
El Real Decreto 488/1997 regula las condiciones del puesto informático para prevenir lesiones musculoesqueléticas y fatiga visual:
- **Monitor:** Situado a una distancia de 40 a 60 cm de los ojos; el borde superior de la pantalla debe quedar a la altura o ligeramente por debajo del eje visual horizontal del usuario para evitar sobrecarga cervical. Debe orientarse perpendicularmente a las fuentes de luz natural (ventanas) para evitar reflejos.
- **Silla de trabajo:** Regulable en altura y profundidad, con apoyo lumbar anatómico, reposabrazos ajustables y base estable de 5 radios con ruedas. El ángulo entre rodillas, caderas y codos debe mantenerse en torno a los 90°.
- **Teclado y ratón:** Teclado inclinable e independiente con espacio libre delante (al menos 10 cm) para apoyar las muñecas. El uso de ratones ergonómicos verticales previene la torsión prolongada del nervio mediano (túnel carpiano).
- **Regla 20-20-20:** Cada 20 minutos de trabajo continuo frente a la pantalla, enfocar la mirada a un objeto distante a 20 pies (~6 metros) durante al menos 20 segundos para relajar los músculos ciliares oculares.

### 3. Seguridad en Salas Técnicas y Centros de Datos (CPD)
- **Manipulación de Servidores:** Servidores de rack de 2U o 4U pueden superar los 25 kg. Su instalación en guías requiere levantamiento por parejas o elevadores mecánicos de servidores, manteniendo la espalda recta y flexionando las piernas.
- **Riesgo Eléctrico (RD 614/2001):** Trabajos en cuadros eléctricos y ePDUs deben realizarse aplicando las **5 Reglas de Oro**:
  1. Desconectar y aislar todas las fuentes de alimentación.
  2. Prevenir cualquier posible reconexión (bloqueo y etiquetado *Lockout/Tagout - LOTO*).
  3. Verificar la ausencia de tensión con voltímetro calibrado.
  4. Poner a tierra y en cortocircuito las fases.
  5. Señalizar y delimitar la zona de trabajo segura.

---

## 💻 Código / Ejemplos

### Script Python: Recordatorio Ergonómico y Vigilancia de la Regla 20-20-20
```python
# ergonomia_asir.py
import time
import sys

def iniciar_monitor_ergonomico(minutos_sesion=20):
    print("==================================================")
    print("   MONITOR PREVENTIVO ERGONÓMICO ASIR (RD 488/97) ")
    print("==================================================")
    print(f"[*] Monitor activo. Aviso configurado cada {minutos_sesion} minutos.")
    print("[*] Recuerde mantener el borde superior del monitor a la altura de los ojos.\n")

    ciclos = 0
    segundos = minutos_sesion * 60

    try:
        while True:
            time.sleep(segundos)
            ciclos += 1
            print(f"\n[!] AVISO ERGONÓMICO #{ciclos} ({ciclos * minutos_sesion} min trabajados)")
            print("--------------------------------------------------")
            print("1. REGLA 20-20-20: Mira a un punto lejano (6 metros) durante 20 segundos.")
            print("2. MOVILIDAD: Realiza estiramientos suaves de cuello, hombros y muñecas.")
            print("3. POSTURA: Verifica que tus pies tocan planos el suelo y rodillas a 90°.")
            print("--------------------------------------------------\a") # Emite sonido campana
    except KeyboardInterrupt:
        print("\n[*] Monitor ergonómico finalizado por el usuario.")

if __name__ == "__main__":
    iniciar_monitor_ergonomico(minutos_sesion=20)
```

---

## ⚠️ Buenas Prácticas / Errores Comunes
- **Ignorar el cableado en CPDs:** Dejar cables de red o alimentación sueltos por el suelo fuera de pasacables o bandejas bajo suelo técnico genera riesgos críticos de caídas al mismo nivel y desconexiones accidentales de servicios críticos.
- **Bloqueo de muñecas al teclear:** Apoyar las muñecas sobre superficies duras mientras se teclea aumenta la presión en el túnel carpiano; las muñecas deben flotar relajadas en posición neutra.
- **Consumo prolongado de pantallas sin hidratación:** No parpadear conscientemente frente al monitor reduce la lubricación ocular, acelerando el síndrome de ojo seco.
