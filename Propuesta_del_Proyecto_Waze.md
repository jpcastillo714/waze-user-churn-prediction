# Propuesta de Proyecto: Waze (User Churn Prediction Track)

[⬅ Volver al README del Proyecto](README.md) | [Documento de Trabajo (waze.md)](waze.md) | [Protocolo de Agentes](AGENTS.md)

---

## 1. Visión General (*Overview*)

El equipo directivo de **Waze** ha solicitado al equipo de datos construir un modelo predictivo de Machine Learning para anticipar el **abandono de usuarios (*user churn*)**. El modelo se basa en datos operativos de navegación y comportamiento recopilados mensualmente de los usuarios de la aplicación.

---

## 2. Matriz de Hitos, Tareas y Partes Interesadas

Esta tabla establece el cronograma de trabajo acumulativo que abarca los seis cursos del programa:

| Hito (*Milestone*) | Tareas Principales | Etapa PACE | Entregables / Informes | Parte Interesada Relevante |
| :---: | :--- | :---: | :--- | :--- |
| **1** | Establecer la estructura del flujo de trabajo del proyecto | **Plan** | Documento global del proyecto (Estrategia PACE) | **May Santner** — Responsable de Análisis de Datos |
| **1a** | Redactar la propuesta de proyecto formal | **Plan** | Propuesta de proyecto aprobada | **Sylvester Esperanza** — Gestor de Proyectos Sénior |
| **2** | Recopilar información resumida sobre los datos | **Analyze** | Archivos de datos (`waze_dataset.csv`) listos para EDA | **Chidi Ga** — Analista de Datos Sénior |
| **2a** | Iniciar la exploración preliminar de datos | **Analyze** | Resumen ejecutivo de calidad de datos | **May Santner** — Responsable de Análisis de Datos |
| **3** | Exploración y depuración exhaustiva de datos | **Plan / Analyze** | Informe de resultados de EDA | **Chidi Ga** — Analista de Datos Sénior |
| **3a** | Construcción de visualizaciones y cuadros de mando | **Analyze / Construct** | Cuadros de mando y gráficos en Tableau | **Sylvester Esperanza** — Gestor de Proyectos Sénior |
| **4** | Calcular estadísticas descriptivas avanzadas | **Analyze** | Análisis de pruebas entre variables de uso clave | **May Santner** — Responsable de Análisis de Datos |
| **4a** | Realizar pruebas de hipótesis estadísticas | **Analyze / Construct** | Informe formal de pruebas de significancia | **May Santner** — Responsable de Análisis de Datos |
| **5** | Construir modelo de regresión logística de churn | **Analyze / Construct** | Modelo econométrico y probabilidades de abandono | **Harriet Hadzic** — Directora de Análisis de Datos |
| **5a** | Evaluar el desempeño del modelo predictivo | **Execute** | Determinación del éxito del modelo y métricas | **Harriet Hadzic** — Directora de Análisis de Datos |
| **6** | Construir modelos avanzados de Machine Learning | **Construct** | Modelo final (Random Forest / XGBoost) | **Harriet Hadzic** — Directora de Análisis de Datos |
| **6a** | Comunicar los conocimientos finales a las partes interesadas | **Execute** | Informe ejecutivo integral y recomendaciones de retención | **Harriet Hadzic** — Directora de Análisis de Datos |

---

## 3. Estimación de Tiempos Profesionales

- **Hito 1 (Planificación PACE y Propuesta):** 1 a 2 días.
- **Hito 2 (Ingesta e Inspección de Variables):** 2 a 3 semanas.
- **Hito 3 (EDA y Dashboards en Tableau):** 1 semana.
- **Hito 4 (Estadística y Pruebas de Hipótesis):** 1 semana.
- **Hito 5 (Regresión Logística y Probabilidades):** 1 a 2 semanas.
- **Hito 6 (Modelos de ML y Cierre Ejecutivo):** 2 semanas.
