# Waze: Resumen Ejecutivo — Hito Curso 1

[⬅ Volver al README del Proyecto](README.md) | [Documento de Estrategia PACE](PACE_Strategy_Document_Waze.md) | [Documento de Trabajo (waze.md)](waze.md)

**Fecha:** 09 de Septiembre de 2026  
**Elaborado por:** Científico de Datos Sénior (Equipo de Crecimiento y Retención de Conductores, Waze)  
**Destinatarios:** Harriet Hadzic (Directora de Análisis de Datos), May Santner (Responsable de Análisis de Datos), Chidi Ga (Analista de Datos Sénior), Sylvester Esperanza (Gestor de Proyectos Sénior)  

---

## 1. Visión General del Proyecto (*Project Overview*)

### Contexto de Negocio
- **Organización:** Waze (*Google Traffic & Navigation Ecosystem*).
- **Misión del Proyecto:** Predecir de forma proactiva qué conductores presentan un alto riesgo de desinstalar o abandonar la plataforma (*churn*). Dado que Waze opera bajo una dinámica de mapas colaborativos impulsados por la comunidad, cada conductor activo actúa como un sensor telemático: sus reportes de incidentes viales, tráfico, peligros y radares nutren el algoritmo de enrutamiento dinámico en tiempo real. La pérdida de conductores reduce la densidad de alertas y perjudica la experiencia de navegación para millones de usuarios a nivel global.
- **Objetivo Estratégico del Hito (Curso 1):** Auditar la calidad e integridad estructural del conjunto de datos telemático de Waze (`waze_dataset.csv`), documentar las directivas analíticas mediante el marco PACE, identificar patrones preliminares de abandono y estructurar los requerimientos técnicos previos a la fase de Análisis Exploratorio de Datos (EDA) del Curso 2.

---

## 2. Estado Actual del Proyecto e Inspección de Calidad (*Current Project State & Data Quality*)

El proyecto ha completado exitosamente la fase de ingestión, perfilado y análisis preliminar en Python sobre la muestra telemática de 14.999 conductores:

| Dimensión | Métrica / Hallazgo Operativo |
| :--- | :--- |
| **Volumen Total** | **14.999 registros de usuarios únicos** y **13 variables** analizadas. |
| **Estructura de Datos** | 8 variables enteras (`int64`), 3 variables decimales (`float64`) y 2 variables categóricas (`object`: `label` y `device`). |
| **Completitud y Nulos** | **95,33% de completitud global**. Se identificaron **700 valores nulos** concentrados exclusivamente en la variable objetivo `label` (4,67% del dataset). Las restantes 12 variables tienen 0 valores faltantes. |
| **Integridad Jerárquica** | Se corroboró que en el 100% de los casos los días de conducción no superan los días activos en la app (`driving_days <= activity_days`) y que las sesiones mensuales son mayores o iguales al total de viajes (`sessions >= drives`). |
| **Anomalías Críticas** | Detección de **19 registros** donde las sesiones mensuales del último mes rebasan las sesiones históricas acumuladas (`sessions > total_sessions`). |
| **Valores Atípicos (*Outliers*)** | Presencia de kilometrajes extremos de hasta **21.183,4 km** mensuales y duraciones de hasta **15.851,7 minutos** (~264 horas al volante al mes), reflejando conductores profesionales o de flotas de larga distancia. |

---

## 3. Principales Conclusiones Iniciales (*Key Insights*)

> [!TIP]
> **Momento "¡Ajá!" del Análisis Preliminar:**  
> *Los usuarios propensos al abandono no son conductores desinteresados ni inactivos, sino "superconductores" que exhiben una intensidad extrema concentrada en muy pocos días. El usuario que abandona recorre una mediana de **697,5 km por día de conducción** (frente a 289,5 km de los retenidos, un incremento del **~240%**) y realiza **10 viajes diarios** en apenas **6 días al mes**, sugiriendo que la app podría estar fallando en satisfacer las necesidades críticas de transportistas o conductores comerciales.*

- **Concentración Extrema en el Uso Diario:**
  - Los conductores retenidos abren la aplicación más del doble de días al mes (mediana de **17 días activos** y **14 días de conducción**) en comparación con los que abandonan (**8 días activos** y **6 días de conducción**).
  - No obstante, la distancia promedio por viaje individual es casi indistinguible entre ambos grupos (mediana de **75,0 km/viaje** en retenidos vs. **74,1 km/viaje** en abandonados), demostrando que la diferencia radica en la **frecuencia de viajes diarios por jornada de trabajo**.
- **Neutralidad Absoluta del Dispositivo Móvil:**
  - El 64,48% de los usuarios de la base utiliza iPhone (9.672) y el 35,52% utiliza Android (5.327).
  - La tasa de abandono observada en iPhone (**17,83%**) es prácticamente idéntica a la tasa de abandono en Android (**17,56%**), con una brecha insignificante de 0,27 puntos porcentuales. El sistema operativo queda descartado como factor causal o diferenciador de churn.
- **Naturaleza de los 700 Valores Faltantes en `label`:**
  - La comparación de promedios, medianas y cuartiles entre los 700 registros sin etiqueta y los 14.299 registros etiquetados confirma que los datos faltantes son **Completamente Aleatorios (*Missing Completely at Random - MCAR*)** (ej. medias de sesiones de 80,8 vs 80,6; medias de kilometraje de 4.039,3 km vs 4.039,3 km; proporción de iPhone del 63,86% vs 64,48%). Esto autoriza su exclusión segura del entrenamiento supervisado sin introducir sesgos de selección.
- **Fidelidad y Antigüedad de Cuenta (*Tenure*):**
  - Los usuarios retenidos presentan una mayor antigüedad histórica en Waze (mediana de **1.843 días** desde el registro, aprox. 5 años) frente a los que abandonan (mediana de **1.321 días**, aprox. 3,6 años).

---

## 4. Recomendaciones y Próximos Pasos (*Next Steps*)

Para la siguiente etapa del marco PACE (**Fase Analyze: EDA Profundo y Visualización en el Curso 2**), se recomienda al equipo técnico y de gestión:

1. **Protocolo de Limpieza y Tratamiento de Nulos:**
   - Excluir del conjunto de entrenamiento y evaluación de los modelos de clasificación (Cursos 4 y 5) los 700 registros con `label` nulo, reservándolos potencialmente como conjunto ciego de inferencia futura.
   - Tratar o capar (*clipping/winsorization*) los valores extremos de `driven_km_drives` (>15.000 km) y `duration_minutes_drives` (>10.000 min) para evitar distorsiones en modelos lineales.
2. **Ingeniería de Características Predictivas:**
   - Construir formalmente en el pipeline de datos las variables derivadas analizadas en este hito:
     - `km_per_driving_day` ($\text{driven\_km\_drives} / \text{driving\_days}$)
     - `drives_per_driving_day` ($\text{drives} / \text{driving\_days}$)
     - `km_per_drive` ($\text{driven\_km\_drives} / \text{drives}$)
     - `percent_sessions_monthly` ($\text{sessions} / \text{total\_sessions}$)
   - Crear una bandera de segmentación (*heavy_driver_flag*) para conductores que superan los 500 km por día de conducción.
3. **Investigación de Producto con Operaciones (Ursula Sayo):**
   - Compartir los hallazgos con el área de operaciones para determinar si los "superconductores" en riesgo son conductores de transporte de carga pesada que abandonan la app debido a la falta de rutas especializadas (puentes bajos, restricciones de tonelaje o peajes comerciales).
4. **Construcción de Tableros en Tableau (Sylvester Esperanza):**
   - Diseñar visualizaciones interactivas de distribución bivariada (días de conducción vs. kilómetros totales) segmentadas por estado de retención para socializar los patrones con las partes interesadas no técnicas.
