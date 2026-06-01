# Machine Learning - Student Performance

## Descripción del proyecto

Este proyecto tiene como objetivo desarrollar un modelo de *machine learning* capaz de predecir la calificación final de un estudiante (`final_score`) a partir de variables académicas, hábitos de estudio y uso de herramientas de Inteligencia Artificial.

A través de este modelo se busca identificar patrones relacionados con:

- hábitos de estudio
- dependencia de herramientas de IA
- asistencia
- participación en clase
- desempeño en exámenes
- consistencia académica

## Descripción del dataset

Para este proyecto se utiliza el dataset **Student Performance & Academic Trends Dataset**, enfocado en analizar el impacto del uso de herramientas de Inteligencia Artificial sobre el desempeño académico de estudiantes.

El dataset contiene:

- **8,000 instancias**
- **26 columnas**

| Variable | Descripción |
| --- | --- |
| `student_id` | Identificador único del estudiante |
| `age` | Edad del estudiante |
| `gender` | Género del estudiante |
| `grade_level` | Nivel académico o grado |
| `study_hours_per_day` | Horas de estudio diarias |
| `uses_ai` | Indica si utiliza herramientas de IA |
| `ai_usage_time_minutes` | Tiempo de uso de herramientas de IA |
| `ai_tools_used` | Herramientas de IA utilizadas |
| `ai_usage_purpose` | Propósito de uso de IA |
| `ai_dependency_score` | Nivel de dependencia de IA |
| `ai_generated_content_percentage` | Porcentaje de contenido generado con IA |
| `ai_prompts_per_week` | Cantidad de prompts enviados semanalmente |
| `ai_ethics_score` | Puntaje de uso ético de IA |
| `last_exam_score` | Calificación del último examen |
| `assignment_scores_avg` | Promedio de tareas |
| `attendance_percentage` | Porcentaje de asistencia |
| `concept_understanding_score` | Nivel de comprensión de conceptos |
| `study_consistency_index` | Consistencia de estudio |
| `improvement_rate` | Tasa de mejora académica |
| `sleep_hours` | Horas de sueño |
| `social_media_hours` | Horas en redes sociales |
| `tutoring_hours` | Horas de tutorías |
| `class_participation_score` | Participación en clase |
| `final_score` | Calificación final del estudiante |
| `passed` | Indica si aprobó o no |
| `performance_category` | Categoría de desempeño |

## Proceso

#### Revision de atributos
#### Limpieza y selección de atributos

Antes de entrenar el modelo se realizó una revisión de las columnas del dataset con el objetivo de conservar únicamente las variables que aportaran información útil para predecir la variable objetivo `final_score`.

Primero se eliminaron columnas que no representaban información predictiva válida o que podían causar fuga de información (*data leakage*). La columna `student_id` fue descartada porque corresponde únicamente a un identificador de cada estudiante y no contiene información relacionada con el desempeño académico.

También se eliminaron las columnas `passed` y `performance_category`, ya que ambas dependen directamente de la calificación final. Incluirlas en el entrenamiento provocaría *data leakage*, porque el modelo estaría utilizando información que normalmente solo se conocería después de saber el valor de `final_score`.

Además, se eliminó la columna `uses_ai` debido a inconsistencias con otras variables relacionadas con el uso de inteligencia artificial. Por ejemplo, en algunos registros `uses_ai` indicaba que el estudiante no usaba IA, pero las columnas `ai_tools_used` y `ai_usage_purpose` sí contenían información sobre herramientas o propósitos de uso. Como la descripción del dataset tampoco aclaraba con precisión el significado de `uses_ai`, se decidió descartarla para evitar introducir ruido o contradicciones en el modelo.

#### Revisión de atributos

La selección de atributos se apoyó en un análisis exploratorio de las variables categóricas y numéricas.
<img width="2490" height="2490" alt="image" src="https://github.com/user-attachments/assets/cece260f-29a3-444c-ad0f-f28578dd79ff" />

Para las variables categóricas se utilizaron gráficos de barras con el fin de revisar la distribución de sus clases. Variables como `grade_level`, `ai_tools_used` y `ai_usage_purpose` mostraron distribuciones  balanceadas, por lo que se conservaron. En el caso de `gender`, las categorías `male` y `female` estaban bastante equilibradas, mientras que la categoría `other` tenía una menor cantidad de ejemplos. Sin embargo, se decidió mantenerla porque aún contaba con registros suficientes y eliminarla podría reducir información demográfica relevante.

Para las variables numéricas se utilizaron histogramas para analizar su distribución e identificar posibles valores atípicos. No se observaron outliers extremos que justificaran la eliminación de filas, por lo que se conservaron todos los registros del dataset.

Adicionalmente, se construyó un heatmap de correlaciones para revisar la relación entre las variables numéricas y la variable objetivo `final_score`. 
<img width="1259" height="1059" alt="image" src="https://github.com/user-attachments/assets/413fb53d-b294-4fb0-86cb-55a17965c630" />

Las variables con mayor correlación fueron:

- `last_exam_score`
- `assignment_scores_avg`
- `concept_understanding_score`

Aunque estas variables presentan una relación importante con `final_score`, ninguna superó una correlación de `0.7`. Por esta razón, se consideró que no eran variables directamente dependientes de la variable objetivo y se conservaron para el entrenamiento. 

También se consideró que para el modelo seleccionado, **CatBoost**, se indica que aunque una variable parezca no tener relación individualmente, puede aportar información suplementaria valiosa cuando se combina con otras dentro de modelos complejos.

Después del proceso de limpieza y selección de atributos, el dataset quedó conformado por:

- **8,000 registros**
- **22 columnas**
- **21 variables predictoras**
- **1 variable objetivo:** `final_score`

### Valores faltantes

Se identificaron valores faltantes que se eliminaron porque en realidad representaban una condición: estudiantes que no usan IA o que no tienen un propósito específico de uso. Por esta razón, se decidió conservar los registros y reemplazar los faltantes con categorías explícitas.

| Variable | Valores faltantes | Categoría agregada |
| --- | ---: | --- |
| `ai_tools_used` | 1,362 | Se reemplazaron por `"Does Not Use AI"`. |
| `ai_usage_purpose` | 1,346 | Se reemplazaron por `"No Specific Purpose"`. |


### Revisión de variable objetivo

Se revisó la distribución de la variable objetivo `final_score`. 

<img width="486" height="360" alt="image" src="https://github.com/user-attachments/assets/b9e5aae1-0012-42a7-84b0-4ba0aaac923c" />


- Final_score tiene una distribución tipo campana de Gauss.
- La mayoría de estudiantes están concentrados alrededor de: 55 - 65. Y conforme te alejas del centro: hay menos scores muy bajos y muy altos.
- No parece haber desbalance extremo. El target no parece sesgado hacia:
   - puros scores altos
   - puros scores bajos

No se detectaron outliers severos que justificaran eliminar registros, por lo que se mantuvieron las **8,000 instancias** originales.


### Separación de conjuntos de entrenamiento, validación y prueba

| Conjunto | Registros | Uso |
| --- | ---: | --- |
| Train | 5,120 | Entrenamiento del modelo |
| Validation | 1,280 | Parte del train set para evaluación inicial |
| Test | 1,600 | Evaluación final con datos no vistos |

Primero se separó el **20% del dataset para prueba**. Después, el conjunto restante se dividió nuevamente para obtener un conjunto de validación. El conjunto de prueba se mantuvo aislado hasta la evaluación final para medir la capacidad de generalización del modelo.

## Preprocesamiento de datos

El preprocesamiento aplicado incluyó:

- relleno de datos faltantes en las variables relacionadas con el uso de IA.
- eliminación de columnas no válidas para predicción
- separación de la variable objetivo `final_score`
- definición de variables categóricas para el modelo CatBoost

Las variables categóricas usadas por el modelo fueron:

- `gender`
- `grade_level`
- `ai_tools_used`
- `ai_usage_purpose`

No fue necesario aplicar manualmente *Label Encoding* ni *One-Hot Encoding*, ya que CatBoost permite trabajar directamente con variables categóricas mediante el parámetro `cat_features`.

No se aplicó escalamiento porque CatBoost no lo necesita. Al ser un modelo basado en árboles, puede trabajar correctamente con variables numéricas en diferentes escalas.


## Modelo seleccionado

El modelo seleccionado fue **CatBoost Regressor**.

Este modelo fue elegido porque:

- es adecuado para datos tabulares
- maneja variables categóricas
- puede capturar relaciones no lineales entre variables académicas, hábitos de estudio y uso de IA
- permite controlar el sobreajuste mediante hiperparámetros y conjunto de validación

El siguiente artículo destaca este modelo como el mejor para datos tabulares:

> Gul, N., Chen, H. & Luo, C. (2025). *Data driven decisions in education using a comprehensive machine learning framework for student performance prediction*. Discover Computing, 28, 153. https://doi.org/10.1007/s10791-025-09585-3

## Implementación del modelo

Parámetros principales utilizados:

| Parámetro | Valor | Descripción |
| --- | ---: | --- |
| `iterations` | 1000 | Número máximo de árboles |
| `learning_rate` | 0.003 | Velocidad de aprendizaje |
| `depth` | 6 | Profundidad de los árboles |
| `loss_function` | RMSE | Función de pérdida para regresión |

El entrenamiento se realizó usando el conjunto de entrenamiento y se monitoreó el desempeño con el conjunto de validación.

## Métricas de evaluación

Como la variable objetivo `final_score` es numérica continua, el problema se abordó como una tarea de **regresión**. Por ello, se utilizaron métricas adecuadas para evaluar error y capacidad explicativa del modelo.

Las métricas seleccionadas fueron:

| Métrica | Interpretación |
| --- | --- |
| MAE | Error absoluto promedio en puntos de calificación. Fácil de interpretar. |
| RMSE | Penaliza con mayor fuerza los errores grandes. |
| R² | Indica qué proporción de la variabilidad de `final_score` explica el modelo. |

Estas métricas están alineadas con el enfoque de evaluación usado en estudios de predicción de desempeño académico, como el artículo de Gul et al. (2025).

## Resultados

<img width="171" height="83" alt="image" src="https://github.com/user-attachments/assets/e04a97cc-35f3-4fbd-8adc-5b3095080c5f" />


El **MAE de 4.186 en test** indica que, en promedio, el modelo se equivoca por aproximadamente 4.2 puntos (más o menos) en la calificación final. El **RMSE de 5.242** muestra que los errores grandes están relativamente controlados. El **R² de 0.852** indica que el modelo explica cerca del 85.2% de la variabilidad del desempeño final en datos no vistos.

<img width="1790" height="490" alt="image" src="https://github.com/user-attachments/assets/6342dd08-b582-4e83-b4d9-4d9e15107dbb" />

El modelo obtuvo resultados muy similares en entrenamiento, validación y prueba.

Como las métricas de train, validation y test son cercanas, no se observa sobreajuste importante. No hay underfitting ni overfitting. Esto sugiere que CatBoost generaliza adecuadamente para este conjunto de datos.

