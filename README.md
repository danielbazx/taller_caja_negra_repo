# Taller Autónomo de Pruebas de Caja Negra
**Manual de Operaciones para Equipos de Ingeniería de Calidad (QA)**

---

## 1. Información General del Proyecto
- **Institución:** Universidad / Facultad de Ingeniería de Sistemas y Computación
- **Materia:** Pruebas, Control de Calidad y Mantenimiento (7mo Semestre)
- **Estándares de Referencia:** ISTQB Foundation Level (v4.0) / ISO/IEC/IEEE 29119 Software Testing Standard
- **Propósito:** Diagnosticar y documentar rigurosamente los defectos inyectados en un módulo de análisis de presupuestos e inversión (`presupuesto_analisis.py`) utilizando técnicas formales de Caja Negra (Partición de Equivalencia y Análisis de Valores Límite) y Caja Blanca (análisis estático y localización de causa raíz).

---

## 2. Configuración del Equipo de Ingeniería QA
Para este taller, las responsabilidades operativas se estructuraron bajo el modelo colaborativo de QA:
- **Tester Principal:** Lideró el diseño del plan de pruebas a ciegas, la cobertura de clases de equivalencia, valores de frontera y la auditoría técnica de los fallos encontrados.
- **Desarrollador / Analista:** Lideró la ejecución del script en Python, la reproducción controlada de colapsos y el aislamiento de las líneas físicas defectuosas en el código fuente.
- **Documentador / Git Lead:** Estructuró los artefactos Markdown, construyó las matrices de prueba y gestionó el control de versiones con Git/GitHub.

---

## 3. Estructura del Repositorio
```text
taller_caja_negra_repo/
├── .gitignore               # Exclusión de artefactos de compilación Python (__pycache__/)
├── presupuesto_analisis.py  # Script base entregado por el docente (conservado intacto con 3 defectos)
├── casos_prueba.md          # Mapa conceptual (Mermaid) + Tabla de pruebas + Reporte de causa raíz
└── README.md                # Validación conceptual profunda (Desafíos Lógicos) y auditoría final
```

> **Nota de integridad de pruebas:** Siguiendo la directriz explícita de la práctica, `presupuesto_analisis.py` **no fue modificado ni corregido**. El objetivo de la ingeniería de pruebas en esta fase es demostrar la capacidad de detectar, aislar y reportar fallos y sus defectos causales, no alterar el artefacto bajo prueba.

---

## 4. Instrucciones de Ejecución y Reproducción

Para ejecutar y validar las pruebas de forma interactiva en la terminal:

```bash
# 1. Clonar el repositorio
git clone <URL_DEL_REPOSITORIO>
cd taller_caja_negra_repo

# 2. Ejecutar el script base
python presupuesto_analisis.py
```

### Reproducción de los Casos de Prueba:
- **CP-01 (División por cero):** Ingrese presupuesto `1000`, socios `0`, meses `6`.  
  *Resultado:* El programa se interrumpe con `ZeroDivisionError: float division by zero`.
- **CP-02 (Cálculo incorrecto de intereses):** Ingrese presupuesto `1000`, socios `2`, meses `12`.  
  *Resultado:* Intereses generados de `$2880.00` en lugar de los `$240.00` matemáticamente correctos.
- **CP-03 (Entradas negativas sin validación):** Ingrese presupuesto `-500`, socios `2`, meses `6`.  
  *Resultado:* Cálculos contables negativos procesados sin ninguna validación de negocio.

---

## 5. Cierre y Validación Conceptual de Alta Profundidad

### Desafío Lógico 1
> **Pregunta:** Según lo investigado en ISTQB, ¿es posible que un Defecto (Bug) exista en el código fuente de `presupuesto_analisis.py` durante años sin llegar a causar nunca un Fallo (Failure)? Justifiquen técnicamente.

**Respuesta y Justificación Técnica:**  
**Sí, es plenamente posible.**  
Bajo el estándar y glosario de ISTQB, es fundamental distinguir la naturaleza estática de un **Defecto** frente a la naturaleza dinámica de un **Fallo**:
1. Un **Defecto (Bug)** es una imperfección física o lógica residente estáticamente en el código fuente (por ejemplo, la ausencia de una validación `if socios <= 0:` antes de la división en la línea 12, o el operador `** 2` en la línea 9).
2. Un **Fallo (Failure)** es un evento en tiempo de ejecución: el desvío observable del comportamiento del sistema respecto al resultado esperado.

Para que un defecto latente se convierta en un fallo, deben cumplirse de manera consecutiva las tres condiciones del modelo de propagación de fallos (*PIE Model - Propagation, Infection, Execution*):
- **Ejecución (Execution):** La ruta de control que contiene el defecto debe ser ejecutada.
- **Infección (Infection):** La ejecución debe provocar un estado interno erróneo en la memoria o variables del programa.
- **Propagación (Propagation):** Dicho estado erróneo debe viajar a través del flujo y alcanzar una salida observable por el usuario o provocar un colapso en el runtime.

**En el contexto de `presupuesto_analisis.py`:**  
Si en la organización o empresa donde opera este script los usuarios siempre registran proyectos con **1 o más socios** (por ejemplo, porque una política de negocio externa o un formulario previo restringe los datos a 2 socios en adelante), la condición `socios = 0` jamás ingresará al programa. En consecuencia, la operación `total / 0` nunca ocurrirá, el estado erróneo jamás se activará y el defecto permanecerá **dormido o latente en el código fuente durante años** sin provocar jamás un fallo visible en producción.

Esto respalda el principio de ISTQB de que *"El testing depende del contexto"* y que el software solo falla cuando las condiciones operativas y las entradas activan el camino defectuoso.

---

### Desafío Lógico 2
> **Pregunta:** Imaginen que corrigen todos los bugs y el script funciona perfecto, pero el cliente afirma que "necesitaba un sistema para calcular nóminas, no presupuestos". ¿Qué principio fundamental del testing de ISTQB se acaba de violar aunque el código esté limpio?

**Respuesta y Justificación Técnica:**  
El principio fundamental violado es la **Falacia de Ausencia de Errores** (*Principio 7 de ISTQB* / *Absence-of-errors fallacy*).

**Análisis Técnico:**
- La **Falacia de Ausencia de Errores** dictamina que: *"Encontrar y corregir defectos no ayuda si el sistema construido es inutilizable y no cumple con las necesidades y expectativas de los usuarios y del negocio"*.
- Este caso ilustra de forma canónica la diferencia epistemológica entre **Verificación** y **Validación**:
  - **Verificación (*¿Construimos el producto correctamente?*):** El equipo de desarrollo y testing logró que el software estuviera 100% libre de fallas técnicas, excepciones, desbordamientos o errores de cálculo aritmético según las especificaciones técnicas del script. La verificación fue exitosa.
  - **Validación (*¿Construimos el producto correcto?*):** El software resuelve un problema que no le interesa al cliente (cálculo presupuestario en vez de liquidación de sueldos y nómina de empleados). La validación falló al 100%.

Un sistema de software completamente libre de bugs técnicos carece de valor si no resuelve el problema real de negocio para el cual se requirió la inversión.

---

## 6. Check-list de Autoevaluación Final

Auditoría previa a la entrega final del taller:

- [x] **¿El repositorio de GitHub es estrictamente público?**  
  *Sí, configurado para acceso público sin restricciones.*
- [x] **¿Contiene el archivo `presupuesto_analisis.py` tal como fue entregado?**  
  *Sí, conservado íntegro con los 3 defectos originales para trazabilidad de pruebas.*
- [x] **¿Contiene el archivo `casos_prueba.md`?**  
  *Sí, con estructura completa de gobernanza, pruebas y diagnóstico.*
- [x] **¿El Markdown incluye evidencia (diagrama/enlace) del mapa conceptual?**  
  *Sí, diagrama formal en Mermaid y conexiones explicadas en detalle.*
- [x] **¿La tabla tiene los 3 casos ejecutados, con columna Estado y líneas de código defectuosas señaladas?**  
  *Sí, CP-01, CP-02 y CP-03 documentados con veredicto `Failed` y causa raíz identificada.*
- [x] **¿El `README.md` contiene las respuestas a los dos desafíos del cierre?**  
  *Sí, Desafíos Lógicos 1 y 2 resueltos con fundamentos técnicos formales de ISTQB.*
- [x] **¿Todos los miembros del equipo participaron y observaron cada actividad, sin importar el rol asignado?**  
  *Sí, dinámica de célula operativa QA completa de inicio a fin.*
