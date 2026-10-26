# Documento de Estrategia PACE: Waze (User Churn Prediction Track)

[⬅ Volver al README del Proyecto](README.md) | [Documento de Trabajo (waze.md)](waze.md) | [Propuesta del Proyecto](Propuesta_del_Proyecto_Waze.md)

Este documento registra las decisiones y reflexiones analíticas para el proyecto Waze siguiendo las 4 fases del marco PACE.

---

## Plan

### 1. ¿Cómo puedes prepararte mejor para comprender y organizar la información proporcionada sobre el negocio y el dataset?
- *Respuesta:* Para prepararse de manera rigurosa y estructurada, el analista de datos debe:
  1. **Interiorizar el modelo de negocio comunitario de Waze:** Comprender que Waze depende de una red colaborativa en tiempo real. La retención de conductores (*driver retention*) es un activo estratégico: una mayor densidad de conductores activos garantiza un flujo continuo de alertas sobre el tráfico, peligros, radares y obras, lo cual alimenta los algoritmos de enrutamiento dinámico. La pérdida de conductores (*churn*) degrada directamente la calidad y frescura de los mapas para el resto de la comunidad.
  2. **Estudiar exhaustivamente el Diccionario de Datos ([`waze.md`](waze.md)):** Analizar las 13 variables del dataset, diferenciando claramente entre métricas correspondientes a la ventana de observación del último mes (`sessions`, `drives`, `driven_km_drives`, `duration_minutes_drives`, `activity_days`, `driving_days`) y métricas acumuladas desde el registro del usuario (`total_sessions`, `n_days_after_onboarding`).
  3. **Mapear a los líderes y partes interesadas clave:** Alinear los entregables con las expectativas de Harriet Hadzic (Directora de Análisis de Datos, visión estratégica y comunicación ejecutiva), May Santner (Responsable de Análisis de Datos, supervisora directa y seguimiento PACE), Chidi Ga (Analista de Datos Sénior y mentor técnico) y Sylvester Esperanza (Gestor de Proyectos Sénior, gestión de cronograma y tableros en Tableau).
  4. **Formular hipótesis de trabajo iniciales:** Plantear interrogantes orientadas al negocio: ¿el abandono se debe a falta de hábito de uso, a sobreexigencia en trayectos largos, o a problemas técnicos según la plataforma móvil?

### 2. ¿Qué recursos, guías o documentación de consulta te ayudarán a ejecutar este trabajo?
- *Respuesta:* Los recursos primarios de soporte analítico y metodológico son:
  1. **Documentación interna del repositorio:** La propuesta oficial de hitos ([`Propuesta_del_Proyecto_Waze.md`](Propuesta_del_Proyecto_Waze.md)), las notas de contexto y correspondencia interna ([`waze.md`](waze.md)), y las directivas operativas para agentes ([`AGENTS.md`](AGENTS.md)).
  2. **Material conceptual del Curso 1:** Notas sobre el marco PACE ([`Modulo_4_Comunicacion_Efectiva_y_Marco_PACE.md`](../../01_Foundations_of_Data_Science/apuntes/Modulo_4_Comunicacion_Efectiva_y_Marco_PACE.md)), estrategia de portafolio y entregables ejecutivos ([`Modulo_5_Estrategia_de_Portafolio_y_Proyectos.md`](../../01_Foundations_of_Data_Science/apuntes/Modulo_5_Estrategia_de_Portafolio_y_Proyectos.md)), y el glosario de términos analíticos ([`Glosario_Terminos_Clave.md`](../../01_Foundations_of_Data_Science/apuntes/Glosario_Terminos_Clave.md)).
  3. **Documentación técnica del ecosistema Python:** Manuales de Pandas y NumPy para la manipulación y exploración de DataFrames, junto con las directrices de buenas prácticas de código PEP 8.

### 3. ¿Qué actividades adicionales realizaría un profesional de datos proactivo antes de comenzar a escribir código?
- *Respuesta:* Un profesional de datos proactivo ejecuta las siguientes actividades preparatorias:
  1. **Validar la procedencia y representatividad de la muestra:** Confirmar si los 14.999 registros representan adecuadamente a la base global de usuarios o si existe un sesgo hacia perfiles con perfiles de conducción particulares (ej. transportistas urbanos vs. conductores particulares de fin de semana).
  2. **Revisar las comunicaciones y expectativas del equipo:** Examinar los correos intercambiados entre May Santner y Chidi Ga para priorizar las necesidades analíticas inmediatas (detección de nulos, distribución de la variable de abandono `label`, y valores atípicos en distancia y tiempo).
  3. **Construir un entorno de desarrollo estructurado y reproducible:** Configurar el cuaderno de Jupyter con celdas organizadas, comentarios explicativos y salidas verificadas, asegurando la consistencia entre el código ejecutado y las respuestas interpretativas.
  4. **Definir criterios de calidad de datos:** Establecer umbrales para evaluar si los valores nulos o atípicos comprometen la representatividad de las conclusiones preliminares.

---

## Analyze

### 1. ¿La información disponible en el dataset parece suficiente para alcanzar el objetivo comercial según tu intuición y el análisis preliminar de las variables?
- *Respuesta:* Sí, la información disponible proporciona un punto de partida cuantitativo sólido y muy relevante. El dataset captura dimensiones cruciales del comportamiento del usuario: volumen de uso mensual (`sessions`, `drives`), esfuerzo físico y temporal (`driven_km_drives`, `duration_minutes_drives`), arraigo o fidelidad en destinos frecuentes (`total_navigations_fav1`, `total_navigations_fav2`), nivel de interacción mensual (`activity_days`, `driving_days`), antigüedad de la cuenta (`n_days_after_onboarding`), plataforma tecnológica (`device`) y la variable objetivo de retención (`label`).  
Sin embargo, para construir modelos predictivos de alto desempeño en etapas posteriores (Cursos 4 y 5), la información podría resultar insuficiente para capturar las causas profundas del descontento del usuario. Faltan variables cualitativas y de experiencia en ruta, tales como: índices de retraso por congestión, quejas por desvíos incorrectos de navegación, frecuencia de interacción comunitaria (envío o confirmación de alertas viales), y métricas de consumo de batería o estabilidad de la aplicación.

### 2. ¿Cómo construirías estadísticas descriptivas del DataFrame (`df.describe()`, `df.info()`) y evaluarías el rango mínimo y máximo de las variables clave?
- *Respuesta:*
  - **Auditoría Estructural con `df.info()`:** Permite verificar las dimensiones totales (14.999 filas x 13 columnas), el uso eficiente de memoria (1.7 MB) y la integridad de los tipos de datos: 8 variables enteras (`int64`), 3 decimales (`float64`) y 2 de texto (`object`). Asimismo, revela de inmediato que la columna `label` es la única que contiene registros faltantes (14.299 valores no nulos, lo que equivale a 700 valores nulos o 4,67%), mientras que las 12 restantes están 100% completas.
  - **Distribución Estadística y Rangos con `df.describe()`:** Permite contrastar las medidas de tendencia central (media vs. percentil 50 / mediana) con las medidas de dispersión (desviación estándar, cuartiles 25% y 75%, mínimos y máximos). Al evaluar los extremos de las variables clave:
    - `driven_km_drives`: Presenta un valor mínimo de 60,44 km, una mediana de 3.493,86 km y un máximo atípico extremo de 21.183,40 km (más de la mitad de la circunferencia terrestre en un mes).
    - `duration_minutes_drives`: Mínimo de 18,28 min, mediana de 1.478,25 min (~24,6 horas) y un máximo de 15.851,73 min (~264,2 horas de conducción, equivalentes a 11 días completos al volante en un mes de 30 días).
    - `sessions`: Mínimo de 0, mediana de 56 y máximo de 743 sesiones en el mes.
    - `activity_days` y `driving_days`: Se ubican en rangos estrictamente lógicos de 0 a 31 y de 0 a 30 días respectivamente. No existen usuarios donde `driving_days > activity_days`, lo que valida la jerarquía operativa esperada.

### 3. ¿Los promedios o distribuciones de alguna de las variables numéricas parecen inusuales o inconsistentes? ¿Cómo describirías las variables de intervalo o temporales?
- *Respuesta:*
  - **Asimetría Positiva Pronunciada:** Las variables de kilometraje (`driven_km_drives`), duración (`duration_minutes_drives`) y sesiones mensuales (`sessions`) muestran una fuerte asimetría a la derecha (*right-skewed*), donde las medias aritméticas son significativamente más elevadas que las medianas debido a la presencia de un segmento de conductores de uso intensivo (*super-drivers*).
  - **Inconsistencia Lógica Detectada:** Se identificaron **19 usuarios** en los cuales el número de sesiones del último mes (`sessions`) es superior al total de sesiones acumuladas desde el registro (`total_sessions`). Dado que `total_sessions` representa teóricamente el acumulador histórico, esta discrepancia señala un fallo de sincronización en el registro telemático de la app o un desajuste en el modelo de estimación del historial del usuario.
  - **Variables Temporales y de Intervalo:**
    - `n_days_after_onboarding`: Variable discreta de intervalo que refleja la antigüedad del usuario. Abarca desde 4 hasta 3.500 días (~9,6 años), con una media de 1.749,8 días y una mediana de 1.741 días, exhibiendo una distribución balanceada y uniforme de cohortes de conductores a lo largo del ciclo de vida de la aplicación.
    - `activity_days` y `driving_days`: Variables discretas de intervalo acotadas entre 0 y 31 días. Muestran que un usuario típico interactúa con la aplicación 16 días al mes y conduce efectivamente durante 12 días al mes.

---

## Construct

> [!NOTE]
> La fase Construct no aplica para el Curso 1. Se abordará en los cursos de Regresión (Curso 4) y Machine Learning (Curso 5).

---

## Execute

### 1. Dado tu conocimiento actual de los datos, ¿qué recomendarías inicialmente a tu responsable directo que investigue más a fondo antes de pasar al análisis exploratorio profundo (EDA)?
- *Respuesta:* Se recomienda a May Santner elevar a la mesa técnica los siguientes puntos de acción antes del EDA del Curso 2:
  1. **Política de Manejo de los 700 Valores Nulos en `label`:** Se ha comprobado que los 700 registros sin etiqueta siguen una distribución idéntica a la población completa (MCAR). Por tanto, para el entrenamiento de modelos de clasificación en los Cursos 4 y 5 se recomienda excluirlos del conjunto de entrenamiento y evaluación supervisada, o bien preservarlos como conjunto ciego de inferencia.
  2. **Investigación del Segmento de "Superconductores":** Los conductores que abandonan (*churned*) registran una mediana de **697,5 km por día de conducción** y **10 viajes por día conducido** en apenas **6 días de conducción al mes**, frente a **289,5 km** y **4,06 viajes/día** en **14 días** de los conductores retenidos. Se debe investigar con el equipo de producto si Waze está perdiendo a transportistas comerciales, repartidores o conductores de flotas debido a la carencia de funciones para vehículos pesados (restricciones de altura, rutas de carga o advertencias específicas).
  3. **Corrección de la Anomalía de Sesiones:** Coordinar con el equipo de ingeniería de datos para auditar los 19 casos donde `sessions > total_sessions` y verificar el mecanismo de cálculo antes de generar ratios de uso en el Curso 2.

### 2. ¿Qué datos o variables presentan inicialmente señales de contener anomalías, valores negativos o registros atípicos?
- *Respuesta:*
  - `driven_km_drives`: Valores extremos de hasta 21.183,4 km mensuales, alejados del patrón de conducción de usuarios particulares convencionales.
  - `duration_minutes_drives`: Valores máximos de hasta 15.851,7 minutos (~264 horas), lo que sugiere que algunos conductores mantienen la app activa durante jornadas completas continuas.
  - `sessions` vs. `total_sessions`: 19 instancias inconsistentes donde las sesiones mensuales superan a las sesiones históricas acumuladas.
  - `label`: Presencia de 700 valores nulos (4,67%) que carecen de clasificación de estado.
  - *Comportamiento Favorable:* No se registraron valores negativos en ninguna de las variables numéricas, lo que demuestra un adecuado filtrado inicial en las tuberías de datos de Waze.

### 3. ¿Qué tipos de datos adicionales o fuentes externas podrían enriquecer y fortalecer este conjunto de datos para los siguientes cursos?
- *Respuesta:*
  1. **Interacción y Contribución Comunitaria:** Datos sobre el envío y confirmación de alertas (accidentes, tráfico, policía, peligros en la calzada) y el nivel o puntaje de usuario (*Waze Score / Level*), para correlacionar la pertenencia comunitaria con la fidelidad a la plataforma.
  2. **Calidad de Enrutamiento y Desvíos de Ruta:** Número de recálculos de ruta forzados por el usuario, tasa de aceptación de rutas alternativas sugeridas y desviación entre el tiempo estimado de llegada (ETA) y el tiempo real de llegada.
  3. **Métricas de Rendimiento Técnico:** Tasa de cierres forzados (*app crashes*), sobrecalentamiento del terminal, consumo de batería por hora de navegación y versiones de la aplicación instalada.
  4. **Contexto Geográfico y Tipología de Vía:** Proporción de kilómetros transitados en autopistas de peaje vs. arterias urbanas congestionadas, y país o área metropolitana de residencia del conductor.
