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

### Revision de atributos
### Limpieza y selección de atributos

Antes de entrenar el modelo se realizó una revisión de las columnas del dataset con el objetivo de conservar únicamente las variables que aportaran información útil para predecir la variable objetivo `final_score`.

Primero se eliminaron columnas que no representaban información predictiva válida o que podían causar fuga de información (*data leakage*). La columna `student_id` fue descartada porque corresponde únicamente a un identificador de cada estudiante y no contiene información relacionada con el desempeño académico.

También se eliminaron las columnas `passed` y `performance_category`, ya que ambas dependen directamente de la calificación final. Incluirlas en el entrenamiento provocaría *data leakage*, porque el modelo estaría utilizando información que normalmente solo se conocería después de saber el valor de `final_score`.

Además, se eliminó la columna `uses_ai` debido a inconsistencias con otras variables relacionadas con el uso de inteligencia artificial. Por ejemplo, en algunos registros `uses_ai` indicaba que el estudiante no usaba IA, pero las columnas `ai_tools_used` y `ai_usage_purpose` sí contenían información sobre herramientas o propósitos de uso. Como la descripción del dataset tampoco aclaraba con precisión el significado de `uses_ai`, se decidió descartarla para evitar introducir ruido o contradicciones en el modelo.

### Revisión de atributos

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

Aunque estas variables presentan una relación importante con `final_score`, ninguna superó una correlación de `0.7`. Por esta razón, se consideró que no eran variables directamente dependientes de la variable objetivo y se conservaron para el entrenamiento. Además, si todas las correlaciones fueran bajas entonces el modelo probablemente no aprendería mucho.

También se consideró que aunque una variable parezca no tener gran relación individualmente, puede aportar información suplementaria valiosa cuando se combina con otras dentro de modelos complejos. El artículo de Ahmed et al. (2025) reportó mejores resultados al mantener todas las variables.

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

- Relleno de datos faltantes en las variables relacionadas con el uso de IA.
- Eliminación de columnas no válidas para predicción
- Separación de la variable objetivo `final_score`
  
**Modelo de red neuronal**
- *One-Hot Encoding*: El modelo base de red neuronal solo acepta números, por lo que debemos pasar columnas a binario. Usamos pd.get_dummies, que aplica one-hot encoding a las variables categóricas con más de dos categorías.
- *Escalamiento*: Las variables tienen escalas muy distintas
  
| Variable            | Rango  |
| ------------------- | ------ |
| sleep_hours         | 0-10   |
| ai_prompts_per_week | 0-1000 |

Para el modelo base de red neuronal, sin scaling, las variables grandes dominarían el entrenamiento.


**Modelo CatBoost**
- Definición de variables categóricas para el modelo CatBoost

Las variables categóricas usadas por el modelo fueron:

- `gender`
- `grade_level`
- `ai_tools_used`
- `ai_usage_purpose`

No fue necesario aplicar manualmente *Label Encoding* ni *One-Hot Encoding*, ya que CatBoost permite trabajar directamente con variables categóricas mediante el parámetro `cat_features`.

No se aplicó escalamiento porque CatBoost no lo necesita. Al ser un modelo basado en árboles, puede trabajar correctamente con variables numéricas en diferentes escalas.

## Modelo base - Red Neuronal
El modelo base implementado fue una **Red Neuronal densa** construida con Keras para resolver un problema de regresión, ya que la variable objetivo `final_score` es numérica continua.

Este modelo recibe como entrada las variables del dataset después del preprocesamiento, incluyendo las variables categóricas transformadas mediante *One-Hot Encoding* y las variables numéricas escaladas con `StandardScaler`.

La arquitectura utilizada está compuesta por una capa de entrada, dos capas densas ocultas con función de activación `relu` y una capa de salida con una sola neurona y activación lineal. Esta salida lineal permite predecir directamente el valor de la calificación final del estudiante.

El modelo se utilizó como punto de comparación inicial para evaluar posteriormente el desempeño de CatBoost y del CatBoost optimizado mediante ajuste de hiperparámetros.


## Modelo 2 - CatBoost Regressor

El modelo seleccionado fue **CatBoost Regressor**.

Este modelo fue elegido porque:

- es adecuado para datos tabulares
- maneja variables categóricas
- puede capturar relaciones no lineales entre variables académicas, hábitos de estudio y uso de IA
- permite controlar el sobreajuste mediante hiperparámetros y conjunto de validación

El artículo **Data driven decisions in education using a comprehensive machine learning framework for student performance predictino** destaca a CatBoost como el modelo más sobresaliente para datos tabulares en su investigación. El cual obtuvo las mayor precisión, menor tasa de error y superó a otros algoritmos potentes como Gradient Boosting y Random Forest.


> Gul, N., Chen, H. & Luo, C. (2025). *Data driven decisions in education using a comprehensive machine learning framework for student performance prediction*. Discover Computing, 28, 153. https://doi.org/10.1007/s10791-025-09585-3

**Implementación del modelo CatBoost Regressor**

Parámetros principales utilizados:

| Parámetro | Valor | Descripción |
| --- | ---: | --- |
| `iterations` | 1000 | Número máximo de árboles |
| `learning_rate` | 0.003 | Velocidad de aprendizaje |
| `depth` | 6 | Profundidad de los árboles |
| `loss_function` | RMSE | Función de pérdida para regresión |

El entrenamiento se realizó usando el conjunto de entrenamiento y se monitoreó el desempeño con el conjunto de validación.


## Modelo 3 mejorado - CatBoost Regressor con optimización de hiperparámetros

CatBoost permite optimizar sus hiperparámetros mediante métodos como **grid_search**. El cual se usa para probar distintas combinaciones de parámetros y seleccionar la que ofrezca el mejor desempeño predictivo.

Este enfoque se respalda en el estudio **Fine Tuned CatBoost and Nature-Inspired Algorithms with Explainable AI**, donde Grid Search fue utilizado como parte del ajuste fino del modelo CatBoost, contribuyendo a mejorar su rendimiento y alcanzar altos niveles de precisión.


> Haque, M. E., Islam, S. M. J., Maliha, J., Sumon, M. S. H., Sharmin, R., & Rokoni, S. (2025). *Improving chronic kidney disease detection efficiency: Fine tuned CatBoost and nature-inspired algorithms with explainable AI.* In 2025 IEEE 14th International Conference on Communication Systems and Network Technologies (CSNT). IEEE. https://doi.org/10.1109/CSNT64827.2025.10968421

**Hiperparámetros**

- `iterations`: número máximo de árboles.
- `learning_rate`: velocidad de aprendizaje.
- `depth`: complejidad de los árboles.
- `l2_leaf_reg`: parámetro de regularización. Evita que el modelo se ajuste demasiado a los datos de entrenamiento.

## Métricas de evaluación

Como la variable objetivo `final_score` es numérica continua, el problema se abordó como una tarea de **regresión**. Por ello, se utilizaron métricas adecuadas para evaluar error y capacidad explicativa del modelo siguiendo el enfoque reportado por Gul et al. (2025).

Las métricas seleccionadas fueron:

| Métrica | Interpretación |
| --- | --- |
| MAE | Error absoluto promedio en puntos de calificación. Fácil de interpretar. |
| RMSE | Penaliza con mayor fuerza los errores grandes. |
| R² | Indica qué proporción de la variabilidad de `final_score` explica el modelo. |

Estas métricas están alineadas con el enfoque de evaluación usado en estudios de predicción de desempeño académico, como el artículo de Gul et al. (2025).

## Resultados 


**Modelo red neuronal**

<img width="604" height="352" alt="image" src="https://github.com/user-attachments/assets/4eb5c6ea-dd63-4ac1-8f86-01097fec9b0d" />


Las gráficas muestran que:

- El modelo aprende muy rápido: la pérdida inicial es alta, pero cae drásticamente durante las primeras épocas.
- Después de esa caída inicial, las curvas de `train loss` y `validation loss` se mantienen relativamente cercanas, por lo que no se observa overfitting.
- Entrenar durante muchas épocas no parece aportar una mejora grande después de que la pérdida se estabiliza.
- En la gráfica de valores reales vs predichos, los puntos siguen una tendencia cercana a la diagonal, lo que indica que el modelo predice razonablemente bien el `final_score`.

**Comparación de los modelos**

<img width="1790" height="590" alt="image" src="https://github.com/user-attachments/assets/d5b9b2c3-805c-45ec-b840-48d4c3b65dac" />
<img width="1989" height="690" alt="image" src="https://github.com/user-attachments/assets/0fd9603a-2e97-41c1-98de-73cf98a0f299" />

Los 3 modelos obtuvieron resultados muy similares en entrenamiento, validación y prueba, indicando que ninguno tuvo overfitting. Como las métricas de train, validation y test son cercanas, no se observa sobreajuste importante. No hay underfitting ni overfitting. Esto sugiere que los modelos generalizan adecuadamente para este conjunto de datos.

Aun teniendo predicciones con poco margen de error, el modelo de Catboost no superó la Red Neuronal. Esto podría sugerir que el CatBoost está subentrenado o con hiperparámetros muy conservadores. Por lo que la mejora posterior se enfocó en ajustar los hiperparámetros.

El modelo de CatBoost mejorado muestra valores más bajos de MAE y RMSE que todos, lo que quiere decir que las predicciones están más cerca de los valores reales. Mientras que el R² subió, indicando que los valores son mejor representados por este último modelo.

En el conjunto de prueba, el modelo CatBoost mejorado obtuvo **MAE de 3.91**, indicando que, en promedio, el modelo 2 se equivoca por aproximadamente 0.092 puntos menos que el modelo inicial de red neuronal y 0.276 puntos menos que el modelo Catboost inicial. Un **RMSE de 4.92**, mostrando que los errores grandes están en promedio 0.152 puntos más controlados que el modelo red neuronal y 0.322 puntos más controlados que el modelo Catboost inicial. El **R² de 0.87** indica que el modelo explica cerca de 1% mejor que el modelo de red neuronal y 1.8% mejor que el modelo Catboost inicial, la variabilidad del desempeño final en datos no vistos.

Las mejoras fueron resultado del ajuste de hiperparámetros mediante **Grid Search**. Sin embargo, las mejoras obtenidas fueron moderadas y no drásticas, lo que sugiere que el modelo inicial ya presentaba un buen desempeño y que el proceso de optimización permitió refinarlo, más que transformar significativamente su capacidad de predicción.


------
## Referencias

Gul, M. N., Abbasi, W., Babar, M. Z., Aljohani, A., & Arif, M. (2025). *Data driven decisions in education using a comprehensive machine learning framework for student performance prediction*. Discover Computing, 28, Article 153. https://doi.org/10.1007/s10791-025-09585-3


 Haque, M. E., Islam, S. M. J., Maliha, J., Sumon, M. S. H., Sharmin, R., & Rokoni, S. (2025). *Improving chronic kidney disease detection efficiency: Fine tuned CatBoost and nature-inspired algorithms with explainable AI.* In 2025 IEEE 14th International Conference on Communication Systems and Network Technologies (CSNT). IEEE. https://doi.org/10.1109/CSNT64827.2025.10968421

Ahmed, W., Wani, M. A., Plawiak, P., Meshoul, S., Mahmoud, A., & Hammad, M. (2025). *Machine learning-based academic performance prediction with explainability for enhanced decision-making in educational institutions*. Scientific Reports, 15, 26879. https://doi.org/10.1038/s41598-025-12353-4

