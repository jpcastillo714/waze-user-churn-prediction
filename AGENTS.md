<!-- IEF:INICIO v0.20.0 — generado por verify_frame.py; no editar dentro -->
# AGENTS.md: Reglas y Contexto del Proyecto (IEF)

> Esta sección la escribe el motor del IEF y la mantiene al día `--mode migrate`. No la
> edites: se reescribe. Las reglas propias del proyecto van **fuera** de los marcadores.

> **Primero, en cada sesión:** `--mode status --json`. Si `upgrade` no es `null`, el
> bundle cambió desde la última vez. Antes de cualquier otra cosa, muéstrale al usuario
> `--mode upgrade-notes` y propón `--mode migrate` (sin `--yes` solo muestra qué haría).
> La migración la aplica el usuario, no tú.

## 1. Proyecto
* **Nombre:** 03_Waze_Project
* **ID:** 03WAZEPROJECT
* **Preset:** analysis (generic -> analysis)

`$IEF` es la carpeta `core/scripts/` del bundle del IEF. Todos los comandos se ejecutan
desde la raíz de este proyecto.

## 2. La regla que gobierna todo lo demás: pregúntale al motor

```bash
python "$IEF/verify_frame.py" --mode status --json
```

Esa salida dice el foco, el paso en que está cada incremento, su `key` y si lleva
compuerta humana. **El ciclo cambia entre proyectos** (los presets renombran pasos,
mueven compuertas y añaden pasos), así que no supongas nada de eso.

Los artefactos de un incremento viven en su directorio, `initiative/increments/<slug>/`
(`new-increment` lo imprime al abrirlo), con el nombre que el preset declara para el
paso. `--mode verify-step` dice qué archivo busca: si falla con «no existe», el
artefacto está en otro sitio o con otro nombre. No inventes subcarpetas.

## 3. Reglas centrales

* **Anti-alucinación.** No inventes datos, variables, reglas ni esquemas. Lo que falta se
  marca `PENDING` y se pregunta. Una cifra sin una ejecución detrás no es un resultado.
* **Cada cifra del informe cita su evidencia.** Declárala en `claims.yml` del incremento
  (plantilla: `core/templates/claims-template.yml`) y cítala junto a la cifra:
  «4,8% [CLM-001]». Escríbela con la precisión que la evidencia respalda: «4,8%» acepta de
  4,75 a 4,85. Si es una aproximación, declárala (`approx: true`) y márcala en el texto
  («≈4,8%», «cerca de 5%»). Una cifra que no sale de un archivo del proyecto no se
  escribe como resultado.
* **Lo comprobable se comprueba; lo demás se advierte.** Un supuesto que se puede
  comprobar en los datos va como invariante en `data-contract.yml` (`invariants`), que se
  compila a un test. Uno que no, va en «Supuestos y advertencias» del informe. Si los datos
  no restringen un resultado (un parámetro cuyo posterior es el prior, una métrica sobre una
  submuestra o sobre datos sintéticos), no lo reportes como medición: dilo en esa sección.
* **Un criterio tiene que poder fallar.** En los criterios que corren un comando o
  comparan contra un umbral, propón al menos una trampa (`must_fail_on`): una entrada mala
  ante la que el criterio debe fallar.
* **Detenerse es mejor que forzar.** Si un criterio es imposible o contradice la
  especificación, no lo fuerces ni lo rebajes: márcalo `status: disputed` con su
  `dispute_reason`, detente y muéstraselo al usuario. `check-gates` falla hasta que decida.
  Nunca edites los archivos de `tests/generated/`: se regeneran desde el YAML.
* **Deja la nota; retoma desde el traspaso.** Al terminar una sesión con avances, deja una
  línea en el diario: qué se entendió, qué se decidió o qué quedó a medias, en lenguaje
  normal (`--mode log --increment <slug>`; el motor le pone el commit). Al retomar un frente,
  o al recibir uno, empieza por `--mode handoff --increment <slug>`: sale del estado, no de un
  resumen de memoria.
* **Propón una sesión nueva cuando ayuda, sin imponerla:** si corregiste lo mismo más de dos
  veces, si se acumulan intentos fallidos, si el trabajo cambia a algo sin relación o si la
  sesión se alarga sin avanzar. El motor también lo sugiere al entrar a implementar y al
  informe. No la propongas si están a fondo en un problema cuyo historial vale. Antes de
  reiniciar, deja la nota del diario con los requisitos tal como se pidieron.
* **Sin archivos sueltos.** Todo va a la carpeta de su rol; la ruta la da el motor.
* **`state.yml` no se edita a mano, nunca.** Es la máquina de estados: `verify_frame.py`
  escribe de forma atómica y deja historial. Todo cambio tiene su modo:

  | Quiero... | Modo |
  |---|---|
  | Dar un paso por terminado (re-verifica el artefacto) | `--mode complete-step` |
  | Registrar la firma de una compuerta (la da el usuario) | `--mode approve-step --increment <slug> --by "<usuario>"` |
  | Pasar al siguiente paso | `--mode advance` |
  | Volver atrás porque la especificación estaba mal | `--mode rewind --to-step <ref> --reason "..."` |
  | Pausar, bloquear, completar o abandonar un incremento | `--mode set-status --increment <slug> --status <X>` |
  | Cambiar el incremento sobre el que operan los comandos | `--mode focus --increment <slug>` |
  | Dejar la nota de una sesión en un incremento | `--mode log --increment <slug> --message "..."` |
  | Retomar un frente (lo que sabe el motor y lo que se entendió) | `--mode handoff --increment <slug>` |
  | Ver qué cambió al actualizarse el IEF | `--mode upgrade-notes` |
  | Actualizar el proyecto a la versión del motor (lo aplica el usuario) | `--mode migrate`, y después `--mode migrate --yes` |

  Si crees que necesitas editarlo a mano, falta un modo: repórtalo en vez de abrir el archivo.

## 4. Protocolo de un paso

1. `--mode status --json` para saber el foco y el paso actual.
2. Leer **solo** las instrucciones de ese paso.
3. Producir el artefacto en el directorio del incremento (ver §2).
4. `--mode verify-step` y después `--mode complete-step`.
5. Si el paso lleva compuerta: presentar el artefacto al usuario y **esperar su
   aprobación explícita**. Solo entonces `--mode approve-step --increment <slug> --by "<usuario>"`.
6. `--mode advance --increment <slug>`.

Sin `--increment`, los comandos caen sobre el incremento **enfocado**. Con varios frentes
abiertos, pásalo siempre.

## 5. Compuertas humanas

Las compuertas son del usuario, no tuyas. **Pedirte que hagas algo no es aprobar**:
«hazlo» o «dale» son encargos de trabajo; una aprobación es posterior al artefacto y se
refiere a él. Nunca apruebes en nombre del usuario.

Una compuerta detiene el *avance*, no el *trabajo*: redacta lo que se pidió y preséntalo
listo para que diga «sí» o «cambia esto».

Qué pasos llevan compuerta lo decide el preset: consúltalo en `status --json`
(`human_gate`), no lo supongas.

**Una firma dice «aprobé esto».** Guarda la huella del artefacto: si el artefacto cambia
después, la firma se **vence**, y `check-gates`, `doctor` y `advance` lo detienen. Ante una
aprobación vencida, muéstrale al usuario qué cambió y pídele que lo revise y vuelva a
firmar (`--mode approve-step --increment <slug> --step <ref>`). No vuelvas a firmar tú.
Firmar los criterios de aceptación los congela: si hay que cambiarlos, se vuelven a firmar.

Si el proyecto exige firmar desde una terminal (`--mode gate-policy`), `approve-step`
se negará a firmar desde tu entorno. Es a propósito: pídele al usuario que lo ejecute él.

## 6. Ciclos

El rigor se elige **por incremento**, no por proyecto. En el ciclo base:

| Ciclo | Cuándo | Pasos | Compuertas |
|---|---|---|---|
| `task` | Código pequeño; nadie hereda decisiones nuevas | 2 | ninguna |
| `exploration` | Entender algo antes de decidir qué construir | 4 (`1`, `2`, `2b`, `3`) | ninguna |
| `prototype` | Hay una hipótesis que puede fallar | 4 | `1` |
| `build` | Otros dependerán de esto | 7 | `1`, `4`, `5` |

Los presets modifican esta tabla. Por ejemplo, el mixin `modeling` inserta en `build` el
paso `6b` (Evaluación del Modelo) **con compuerta**. Por eso la fuente de verdad es
`status --json`.

Si el trabajo ni siquiera merece un incremento (un gráfico, un arreglo de diez minutos),
anótalo con `--mode log --message "..."`.

Un `task` **no promueve reglas**: no pasó por compuerta. Si aparece una decisión que
otros van a heredar, cierra el `task` y abre un `prototype` o un `build`.

## 7. Estados

* **Incremento:** `ACTIVE`, `PAUSED`, `BLOCKED`, `COMPLETED`, `MERGED`, `ABANDONED`.
* **Paso:** `PENDING`, `IN_PROGRESS`, `COMPLETED`, `APPROVED`, `NEEDS_REVISION`.

## 8. Cuando la especificación está mal

Si la implementación revela que una regla es inviable, no parchees el código para que
quepa: `--mode rewind --to-step <ref> --reason "..."`. El retroceso marca
`NEEDS_REVISION` en el paso destino **y en todos los posteriores** que tenían trabajo.

Un criterio que no se puede medir hoy se marca `blocked`; no se rebaja el umbral.

## 9. Cerrar un incremento

```bash
python "$IEF/verify_frame.py" --mode check-gates
python "$IEF/verify_frame.py" --mode merge-increment --increment <slug> --dry-run
python "$IEF/verify_frame.py" --mode merge-increment --increment <slug>
```

`merge-increment` promueve reglas, contrato y criterios a la especificación viva del
proyecto. Si el ciclo no tenía compuerta sobre las reglas, exige `--by "<nombre>"`: esa
firma es de una persona, no tuya.

## 10. Al llegar a este proyecto

```bash
python "$IEF/verify_frame.py" --mode status
python "$IEF/verify_frame.py" --mode doctor
```

`doctor` revela lo que `status` no muestra: estado ilegible, bloqueos vencidos,
compuertas terminadas sin aprobar, firmas vencidas, afirmaciones que su fuente ya no
respalda, informes sin supuestos y advertencias, entradas externas que invalidan reglas vigentes y si el proyecto está
desactualizado respecto del motor. Si una afirmación ya no está respaldada, no cambies la
cifra por tu cuenta: muéstrale al usuario qué cambió en la evidencia.
Si reporta `FAIL`, arréglalo antes de avanzar ningún paso.

## 11. Reglas específicas del preset
* **Estructura:** notebooks en `exploracion`, codigo reutilizable en `codigo`, figuras en `resultados`, reportes cortos en `avances`.
* **Solo dos compuertas: la pregunta y los criterios de validez.** Un analisis que no construye nada permanente no necesita aprobar su implementacion. Es una decision del preset, no un descuido.
* **Las definiciones se fijan ANTES de medir.** El paso 4 define que cuenta como "usuario activo", "sesion" o "conversion". Cambiar una definicion despues de ver el resultado invalida la comparacion y es la forma mas comun de enganarse a uno mismo.
* **Los supuestos son parte del contrato.** Lo que se puede comprobar en los datos (ausencia de duplicados, rangos, parametros fijos, tamano de la muestra) va en `invariants` de `data-contract.yml` y se compila a un test. Lo que no (independencia, estacionariedad) va en «Supuestos y advertencias» del reporte. Un supuesto sin verificar ni declarar es una conclusion sin base.
* **De notebook a codigo:** un notebook es un borrador de razonamiento, no un resultado. Nada se cita sin haber migrado a `src/` con un test.
* **Toda cifra del reporte es regenerable con un comando.** Si no lo hay, es `PENDING`.
<!-- IEF:FIN -->

﻿# AGENTS.md — Protocolo de Agentes: Waze (User Churn Prediction Track)

[⬅ Volver al README del Proyecto](README.md) | [MOC del Curso 1](../../01_Foundations_of_Data_Science/README.md)

---

## 1. Misión e Identidad del Agente

Este archivo define las directivas operativas para cualquier agente de IA (incluyendo **Antigravity**) que trabaje dentro del proyecto **Waze**.
- **Rol del Agente:** Actuar como Científico de Datos Sénior en el equipo de *Growth & Driver Retention* de Waze.
- **Objetivo Principal:** Analizar los patrones de navegación y comportamiento de los conductores para predecir la probabilidad de abandono (*churn*) de usuarios de la aplicación y orientar estrategias de retención proactivas.

---

## 2. Directiva Crítica: Acceso a Contenido Externo

> [!IMPORTANT]
> **AUTORIZACIÓN Y MANDATO DE LECTURA EXTERNA:**
> Este proyecto **NO ESTÁ AISLADO** de la estructura general del curso. Los agentes tienen autorización expresa y la obligación de leer y referenciar activamente documentación y notas ubicadas fuera de este directorio:
> 
> 1. **Notas Teóricas del Curso 1:**
>    - [Marco PACE en Profundidad](../../01_Foundations_of_Data_Science/apuntes/Modulo_4_Comunicacion_Efectiva_y_Marco_PACE.md) — Metodología de 4 fases para estructurar todo el trabajo.
>    - [Estrategia de Portafolio y Plantilla PACE](../../01_Foundations_of_Data_Science/apuntes/Modulo_5_Estrategia_de_Portafolio_y_Proyectos.md) — Plantilla requerida para los entregables.
>    - [Herramientas y Stack de Python](../../01_Foundations_of_Data_Science/apuntes/Modulo_3_Toma_de_Decisiones_y_Herramientas.md) — Manipulación de DataFrames y análisis exploratorio.
>    - [Glosario de Términos Clave](../../01_Foundations_of_Data_Science/apuntes/Glosario_Terminos_Clave.md) — Definiciones oficiales de métricas y conceptos.
> 2. **Cursos Futuros en el Ecosistema:**
>    - Cuando el usuario avance a cursos posteriores (EDA en Curso 2, Pruebas de Hipótesis en Curso 3, Regresión Logística en Curso 4 y Modelado de Churn en Curso 5), los agentes deben consultar las notas de dichos cursos ubicadas en `../../../` para alinear los notebooks con el temario oficial.

---

## 3. Hoja de Ruta Acumulativa del Proyecto

A lo largo del certificado de Google, este proyecto progresa a través de las siguientes etapas:

| Curso | Fase PACE | Entregable Técnico / Analítico |
| :---: | :---: | :--- |
| **Curso 1** | **Plan** | Documento de Estrategia PACE y Resumen Ejecutivo para la dirección de producto de Waze. |
| **Curso 2** | **Analyze** | Cuaderno de EDA en Python: estructura del dataset (`waze_dataset.csv`), días activos, kilómetros recorridos, alertas reportadas y valores atípicos. |
| **Curso 3** | **Analyze / Construct** | Pruebas de hipótesis estadísticas sobre la diferencia en hábitos de conducción entre usuarios de Android y usuarios de iOS. |
| **Curso 4** | **Construct** | Modelo de Regresión Logística Binaria para estimar las probabilidades individuales de abandono (*churn vs. retained*). |
| **Curso 5** | **Construct** | Modelos de Machine Learning basados en árboles (Random Forest y XGBoost) con optimización de hiperparámetros y feature importance. |
| **Curso 6** | **Execute** | Síntesis final, informe gerencial con recomendaciones de producto para retener conductores y documentación para portafolio en GitHub. |

---

## 4. Estándares de Calidad y Convenciones

1. **Ingeniería de Características:** Evaluar ratios predictivos (ej. kilómetros por día de conducción, proporción de viajes completados sobre cancelados).
2. **Formato de Enlaces:** Utilizar **enlaces Markdown nativos y relativos**. Queda estrictamente prohibido el uso de prefijos absolutos `file:///` conforme a las reglas de Obsidian.
3. **Métricas de Negocio:** Conectar el rendimiento técnico de los modelos (curva ROC-AUC, Precision-Recall) con el coste comercial de perder conductores activos frente al coste de lanzar campañas de retención.

