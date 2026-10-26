# Waze — Documento de Trabajo y Notas del Proyecto

[⬅ Volver al README del Proyecto](README.md) | [Propuesta del Proyecto (Hitos)](Propuesta_del_Proyecto_Waze.md) | [Estrategia PACE](PACE_Strategy_Document_Waze.md) | [Protocolo de Agentes](AGENTS.md)

---

## 1. Visión General y Escenario de Negocio

¡Bienvenido a tu nuevo puesto en el equipo de datos de **Waze**!

### Contexto y Modelo Comunitario
La aplicación de navegación comunitaria de Waze facilita a millones de conductores de todo el mundo llegar a sus destinos de forma rápida y segura. A diferencia de otros sistemas de mapas estáticos, el valor de Waze radica en su comunidad: los conductores comparten datos en tiempo real sobre atascos, accidentes, obras y controles policiales.

### Problema de Negocio: Abandono de Usuarios (*Churn*)
El crecimiento a largo plazo de una aplicación depende tanto de captar nuevos usuarios como de **retener a los existentes**. Cuando los conductores dejan de utilizar la aplicación o la desinstalan (*churn*), se reduce la cantidad de información vial comunitaria en tiempo real, lo que perjudica la experiencia de los demás usuarios.

El equipo directivo de Waze ha encargado el desarrollo de un **modelo predictivo de Machine Learning** para responder a tres preguntas cruciales:
1. *¿Quiénes son los usuarios más propensos a darse de baja o dejar de conducir con Waze?*
2. *¿Por qué razones abandonan la plataforma (patrones de viaje, frustración en trayectos, dispositivo)?*
3. *¿Cuándo abandonan (días tras el registro, frecuencia de sesiones)?*

---

## 2. El Equipo del Proyecto y Partes Interesadas

### Equipo de Análisis de Datos de Waze
| Nombre | Rol | Responsabilidad Clave |
| :--- | :--- | :--- |
| **Harriet Hadzic** | Directora de Análisis de Datos | Dirección estratégica, supervisión de modelos y comunicación ejecutiva. |
| **May Santner** | Responsable de Análisis de Datos | Supervisora directa, planificación PACE, asignación y revisión de entregables. |
| **Chidi Ga** | Analista de Datos Sénior | Mentor técnico, creador del Jupyter Notebook de inicio y análisis de datos. |
| **Sylvester Esperanza** | Gestor de Proyectos Sénior | Cronograma de hitos, gestión de entregables y dashboards en Tableau. |

### Partes Interesadas Interfuncionales
| Nombre | Rol | Enfoque |
| :--- | :--- | :--- |
| **Ursula Sayo** | Directora de Operaciones | Adopción de conductores, impacto en la red vial y fidelización de usuarios. |

---

## 3. Comunicaciones y Notas del Equipo

### Extracto de Notas de la Reunión de Liderazgo (Compartidas por May Santner)
- **Sylvester Esperanza (Senior PM):** Se ha elaborado una propuesta de proyecto que categoriza las tareas por hitos siguiendo las etapas del marco PACE.
- **Chidi Ga (Senior Data Analyst):** Es imprescindible verificar la integridad del conjunto de datos de Waze e identificar sesgos antes de realizar pruebas de hipótesis o modelado.
- **Harriet Hadzic (Directora de Datos):** La identificación temprana de patrones de abandono permitirá al equipo de producto activar campañas de retención proactivas antes de que el conductor desinstale la aplicación.

---

### Correspondencia Interna por Correo Electrónico

#### Correo 1: Asignación de May Santner
> **De:** "May Santner" `<May@waze.com>`  
> **Para:** Nuevo Analista de Datos  
> **Cc:** "Chidi Ga" `<Chidi@waze.com>`  
> **Asunto:** ¿Ayuda con el cuaderno de codificación?  
>
> *Buenos días,*  
> *Te escribo para pedirte apoyo con nuestro nuevo proyecto de predicción de abandono de usuarios. Chidi comenzó un Jupyter Notebook con los datos de Waze (`waze_dataset.csv`). Como está concluyendo otro análisis, agradecerá tu colaboración para estructurar e inspeccionar los datos iniciales.*  
> *Saludos,*  
> *May Santner — Responsable de Análisis de Datos, Waze*

#### Correo 2: Indicaciones Técnicas de Chidi Ga
> **De:** "Chidi Ga" `<Chidi@waze.com>`  
> **Para:** Nuevo Analista de Datos  
> **Cc:** "May Santner" `<May@waze.com>`  
> **Asunto:** RE: ¿Ayuda con el cuaderno de codificación?  
>
> *¡Encantado de conocerte (virtualmente)!*  
> *Por favor, ayúdanos inspeccionando los datos de Waze: incluye un resumen de tipos de datos (`dtypes`), recuentos de nulos, valores atípicos en kilometraje y minutos de conducción, y la distribución preliminar de la variable de abandono (`label`).*  
> *Muchas gracias,*  
> *Chidi Ga — Analista de Datos Sénior, Waze*

---

## 4. Diccionario de Datos: `waze_dataset.csv`

El conjunto de datos contiene información de **14.999 usuarios únicos** y consta de **13 columnas**:

| Columna | Tipo de Dato | Descripción Oficial |
| :--- | :---: | :--- |
| `ID` | Entero (*int*) | Índice numérico secuencial único asignado a cada usuario. |
| `label` | Objeto (*string*) | **Variable objetivo binaria**: indica si el usuario permaneció activo (`retained`) o abandonó el servicio (`churned`) durante el mes evaluado. |
| `sessions` | Entero (*int*) | Número total de sesiones iniciadas por el conductor en la aplicación durante el mes. |
| `drives` | Entero (*int*) | Número de viajes completados de al menos 1 kilómetro durante el mes. |
| `device` | Objeto (*string*) | Tipo de dispositivo principal con el que el usuario utiliza la app (`Android` o `iPhone`). |
| `total_sessions` | Decimal (*float*) | Estimación proyectada del número acumulado de sesiones desde el registro del usuario (*onboarding*). |
| `n_days_after_onboarding` | Entero (*int*) | Número de días transcurridos desde que el usuario creó su cuenta en Waze. |
| `total_navigations_fav1` | Entero (*int*) | Número de viajes dirigidos al primer lugar favorito guardado por el usuario (ej. casa). |
| `total_navigations_fav2` | Entero (*int*) | Número de viajes dirigidos al segundo lugar favorito guardado por el usuario (ej. trabajo). |
| `driven_km_drives` | Decimal (*float*) | Total acumulado de kilómetros conducidos durante el mes. |
| `duration_minutes_drives` | Decimal (*float*) | Duración total de conducción registrada en minutos durante el mes. |
| `activity_days` | Entero (*int*) | Número de días en los que el usuario abrió la aplicación Waze durante el mes. |
| `driving_days` | Entero (*int*) | Número de días en los que el usuario realizó al menos un trayecto de conducción durante el mes. |

---

## 5. Tareas del Laboratorio y Entregables del Curso 1

1. **Cuaderno de Trabajo:** [`Activity_Course 2 Waze project lab.ipynb`](Activity_Course%202%20Waze%20project%20lab.ipynb)
   - Cargar `waze_dataset.csv` con `pandas`.
   - Inspeccionar dimensiones (14.999 filas x 13 columnas), tipos de datos y valores nulos.
   - Analizar estadísticas descriptivas de `driven_km_drives`, `activity_days` y la proporción de `churned` vs `retained`.
2. **Documento de Estrategia PACE:** [`PACE_Strategy_Document_Waze.md`](PACE_Strategy_Document_Waze.md)
   - Responder las preguntas de las fases *Plan*, *Analyze* y *Execute*.
3. **Resumen Ejecutivo:**
   - Elaborar informe de 1 página con base en la [Plantilla de Resumen Ejecutivo](../Plantilla_Resumen_Ejecutivo.md).
