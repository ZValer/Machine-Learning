# Machine Learning - Student Performance

## Descripción del Proyecto

Este proyecto tiene como objetivo desarrollar un modelo de machine learning capaz de predecir la calificación final de un estudiante a partir de variables académicas, hábitos de estudio y uso de herramientas de Inteligencia Artificial.

A través de este modelo, se busca identificar patrones relacionados con:

- hábitos de estudio
- dependencia de herramientas de IA
- asistencia
- participación en clase
- desempeño en exámenes
- consistencia académica

## Descripción del Dataset

Para este proyecto se utiliza "Student Performance & Academic Trends Dataset" enfocado en el impacto de herramientas de Inteligencia Artificial sobre el desempeño académico de estudiantes.

El dataset contiene:

8,000 instancias
26 columnas (features)

y fue diseñado para ayudar a investigadores, docentes y científicos de datos a analizar cómo las herramientas de aprendizaje impulsadas por IA afectan el rendimiento estudiantil.

Las variables incluyen hábitos de estudio, métricas de uso de IA y desempeño académico.

| Variable                        | Descripción                               |
| ------------------------------- | ----------------------------------------- |
| student_id                      | Identificador único del estudiante        |
| age                             | Edad del estudiante                       |
| gender                          | Género del estudiante                     |
| grade_level                     | Nivel académico o grado                   |
| study_hours_per_day             | Horas de estudio diarias                  |
| uses_ai                         | Indica si utiliza herramientas de IA      |
| ai_usage_time_minutes           | Tiempo de uso de herramientas IA          |
| ai_tools_used                   | Herramientas de IA utilizadas             |
| ai_usage_purpose                | Propósito de uso de IA                    |
| ai_dependency_score             | Nivel de dependencia de IA                |
| ai_generated_content_percentage | Porcentaje de contenido generado con IA   |
| ai_prompts_per_week             | Cantidad de prompts enviados semanalmente |
| ai_ethics_score                 | Puntaje de uso ético de IA                |
| last_exam_score                 | Calificación del último examen            |
| assignment_scores_avg           | Promedio de tareas                        |
| attendance_percentage           | Porcentaje de asistencia                  |
| concept_understanding_score     | Nivel de comprensión de conceptos         |
| study_consistency_index         | Consistencia de estudio                   |
| improvement_rate                | Tasa de mejora académica                  |
| sleep_hours                     | Horas de sueño                            |
| social_media_hours              | Horas en redes sociales                   |
| tutoring_hours                  | Horas de tutorías                         |
| class_participation_score       | Participación en clase                    |
| final_score                     | Calificación final del estudiante         |
| passed                          | Indica si aprobó o no                     |
| performance_category            | Categoría de desempeño                    |


## Proceso
#### Selección de Columnas de Interés

Del dataset original se seleccionaron únicamente las variables más relevantes para el entrenamiento del modelo.

Esto permite:

- reducir ruido innecesario
- disminuir complejidad
- mejorar el rendimiento del modelo
- evitar variables irrelevantes o redundantes
- Limpieza de Datos

#### Limpieza de datos para asegurar la calidad del dataset.

Este proceso incluyó:

- revisión de valores faltantes
- validación de categorías
- corrección de formatos

#### Separación del Set de Entrenamiento y Prueba

Una vez limpios los datos, el dataset fue dividido en dos subconjuntos:

- train_data → utilizado para entrenar el modelo
- test_data → utilizado para evaluar el rendimiento del modelo

La división se realizó utilizando una proporción:

80% entrenamiento x 20% prueba

y aplicando mezcla aleatoria (shuffle) para evitar sesgos derivados del orden original de los datos.

#### Preprocesamiento de Datos

Para las variables categóricas se aplicaron diferentes técnicas de encoding dependiendo de la cantidad de categorías presentes.

*Label Encoding*

Se utilizó Label Encoder para variables binarias o con 2 categorías, transformando texto a valores numéricos.

Categoría	"gender"
Male	0
Female	1

*One-Hot Encoding*

Para variables categóricas con múltiples categorías se utilizó One-Hot Encoding.

Esta técnica transforma cada categoría en una columna binaria independiente, evitando introducir relaciones numéricas artificiales entre categorías que realmente no tienen un orden lógico.

Por ejemplo:

"ai_usage_purpose":
Exam Prep /
Coding

se transforma en:

"ai_usage_purpose_ExamPrep" y	"ai_usage_purpose_Coding" con valores binarios

