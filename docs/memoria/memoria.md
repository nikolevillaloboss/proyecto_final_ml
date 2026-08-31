# Memoria — Predicción de nivel de ingresos con el dataset Adult

**Curso:** Aprendizaje Automático — Trabajo Práctico 2
**Institución:** Instituto Tecnológico de Costa Rica, Programa Ciencia de Datos
**Estudiantes:** Jose Pablo Ruiz Myrie · Nikole Villalobos Lopez
**Audiencia:** profesor evaluador del curso
**Dataset:** https://archive.ics.uci.edu/dataset/2/adult

---

## 1. Introducción y planteamiento del problema

Este trabajo predice si una persona gana más de $50 000 al año a partir de sus datos demográficos y laborales. El dataset es Adult (Census Income), construido con el censo de Estados Unidos de 1994 y publicado en el UCI Machine Learning Repository con el id 2. Contiene 48 842 observaciones, 14 variables predictoras y la variable objetivo `income`.

La variable objetivo tiene dos categorías: `<=50K` y `>50K`. El problema es una clasificación binaria supervisada. El proyecto entrena cinco modelos, ajusta sus hiperparámetros y elige el de mejor desempeño según el objetivo de negocio.

La pregunta de trabajo tiene dos partes: predecir la clase de ingreso y medir qué variables se asocian con ganar más de $50K. El objetivo de negocio prioriza accuracy y precision sin abandonar la detección de la clase `>50K`. Las secciones siguientes documentan cada decisión que sostiene esa respuesta.

---

## 2. Análisis del dataset basado en conocimiento del dominio

El dataset combina seis variables numéricas y ocho categóricas. Las numéricas son `age`, `education-num`, `hours-per-week`, `capital-gain`, `capital-loss` y `fnlwgt`. Las categóricas son `workclass`, `education`, `marital-status`, `occupation`, `relationship`, `race`, `sex` y `native-country`.

`capital-gain` y `capital-loss` concentran casi todos sus valores en 0. La mayoría de las personas no reporta ganancias ni pérdidas de capital, y los valores positivos son escasos pero muy altos. Esta distribución genera una asimetría fuerte que exige un tratamiento específico.

`education` y `education-num` codifican la misma información en texto y en número. Esta duplicación crea redundancia entre columnas. El proyecto elimina `education` y conserva `education-num`.

El dataset presenta un desequilibrio de clases de 3:1. El 76% de las observaciones pertenece a `<=50K` y el 24% a `>50K`. Este desequilibrio invalida la accuracy como métrica única de evaluación.

---

## 3. Selección de atributos y reducción de dimensionalidad

El proyecto selecciona atributos con dos pruebas de filtro sobre `income`. El ANOVA F-test evalúa las variables numéricas y el Chi-cuadrado evalúa las categóricas. Ambas pruebas miden la relación estadística entre cada predictor y la variable objetivo.

El ANOVA F-test confirma relación significativa para `age`, `education-num`, `capital-gain-log`, `capital-loss-log` y `hours-per-week`. `fnlwgt` obtiene un p-value mayor que 0.05, por lo que el proyecto la descarta. El Chi-cuadrado confirma asociación significativa para las siete variables categóricas restantes, que se conservan.

El proyecto codifica las categóricas con One-Hot y `drop_first=True`, lo que produce 81 variables predictoras. No se aplicó PCA porque 81 columnas permiten entrenar los modelos sin costo excesivo y conservar la interpretación de cada atributo. La reducción se limitó a eliminar `fnlwgt` y `education`.

---

## 4. Tratamiento de datos faltantes y de outliers

Los valores faltantes aparecen en `workclass`, `occupation` y `native-country`, marcados con `?`. La estrategia depende del patrón de ausencia de cada variable. El proyecto comparó el perfil de las observaciones con y sin dato antes de decidir.

`workclass` y `occupation` faltan según otras variables observadas. Las observaciones sin dato registran menos horas trabajadas (31.6 frente a 40.6) y menor nivel educativo (9.23 frente a 10.1). Este patrón corresponde a MAR, por lo que el proyecto aplica imputación predictiva con un Random Forest entrenado sobre las variables observadas.

`native-country` muestra grupos casi idénticos con y sin dato: la edad promedio difiere en 0.23 años y el porcentaje de `>50K` en 0.09 puntos. El proyecto imputa esta variable con la moda. La imputación se ajustó solo con el conjunto de entrenamiento para evitar fuga de información.

El proyecto conserva los outliers detectados por IQR porque representan valores válidos. `age` llega a 90 años y `education-num` cubre niveles educativos reales, valores posibles dentro del dominio. Eliminar estos casos descartaría información que separa ingresos altos de bajos.

`capital-gain` y `capital-loss` reciben una transformación logarítmica `log1p` en lugar de eliminación. Su concentración en 0 produce un IQR de 0, lo que marca todos los valores positivos como atípicos de forma incorrecta. La transformación comprime la escala y reduce el peso de los valores extremos sin borrar observaciones. El proyecto escala `age`, `education-num`, `capital-gain-log` y `capital-loss-log` con `StandardScaler`, `hours-per-week` con `RobustScaler`, y deja las variables binarias sin escalar.

---

## 5. Modelos utilizados y fundamentación teórica

El proyecto entrena cinco modelos e incluye un modelo neuronal. Cada modelo aporta un enfoque distinto de clasificación. La comparación entre enfoques lineales, de árboles, de distancia y de red neuronal responde a la pregunta de trabajo.

La Regresión Logística estima la probabilidad de pertenecer a `>50K` con una combinación lineal de las variables. Sus coeficientes indican la dirección y la fuerza de cada predictor. Actúa como línea base interpretable.

El Árbol de Decisión divide los datos con reglas del tipo "variable ≤ umbral". El Random Forest combina cientos de árboles entrenados sobre muestras y variables distintas, lo que reduce la varianza y captura relaciones no lineales. El KNN clasifica cada caso según la clase mayoritaria de sus vecinos más cercanos y exige variables escaladas porque depende de distancias.

El Perceptrón Multicapa (MLP) es el modelo neuronal. Ajusta los pesos de una o más capas ocultas para aprender relaciones no lineales entre las variables. El proyecto divide los datos en entrenamiento, validación y prueba, selecciona cada modelo con el F1 de validación y reserva la prueba para la evaluación final.

---

## 6. Ajuste de hiperparámetros

El proyecto probó al menos tres combinaciones de hiperparámetros por modelo. La tabla resume las combinaciones y la configuración elegida. Cada configuración final se seleccionó con el F1 de validación, salvo el Random Forest, que priorizó precision y accuracy.

| Modelo | Combinaciones probadas | Configuración elegida |
|---|---|---|
| Regresión Logística | baseline, `C=0.1`, `C=10`, penalización `L1`, `class_weight=balanced` | Balanced |
| Árbol de Decisión | baseline, profundidad 5, profundidad 10, árbol regularizado | Profundidad 5 |
| Random Forest | 12 configuraciones (`n_estimators`, `max_depth`, `min_samples_split/leaf`, `max_features`, `class_weight`, `criterion`) más comparación One-Hot vs Ordinal | Ordinal Encoding, 400 árboles, `max_depth=16` |
| KNN | `k` = 5, 15, 25, 35, 51, 75 con ponderación `uniform` y `distance` | k = 51, `uniform` |
| Perceptrón MLP | [64] ReLU, [64,32] ReLU, [64] tanh (`lr=0.001`, `L2=0.001`, `sgd`) | [64, 32] ReLU |

El Random Forest incluyó un experimento de codificación. El Ordinal Encoding funciona en este modelo porque los árboles usan cortes por umbral y no interpretan los enteros como magnitudes. El Ordinal alcanzó una precision de 0.775 frente a 0.576 del modelo balanceado, con una caída de F1 de 0.012, por lo que el proyecto lo eligió como modelo final.

El proyecto no aplicó sobremuestreo ni submuestreo. El ratio de 3:1 queda por debajo del umbral de 10:1 que justificaría remuestreo. En su lugar, probó `class_weight` en los modelos que lo admiten para medir su efecto sobre la clase `>50K`.

---

## 7. Evaluación comparativa de resultados

La tabla presenta las métricas de prueba de cada modelo. Los valores provienen del conjunto de prueba de cada notebook. Las particiones difieren entre notebooks (80/20 y 70/15/15), por lo que la comparación orienta la decisión sin constituir un ranking exacto sobre un único conjunto.

| Modelo | Accuracy | Precision | Recall | F1 | AUC-ROC |
|---|---|---|---|---|---|
| Random Forest (Ordinal, final) | 0.864 | 0.775 | 0.607 | 0.681 | 0.920 |
| Perceptrón MLP ([64,32]) | 0.853 | 0.727 | 0.619 | 0.669 | 0.911 |
| KNN (k=51) | 0.852 | 0.710 | 0.646 | 0.677 | 0.905 |
| Regresión Logística (Balanced) | 0.800 | 0.553 | 0.853 | 0.671 | 0.899 |
| Árbol de Decisión (prof. 5) | 0.848 | 0.788 | 0.501 | 0.613 | 0.886 |

El Random Forest lidera en accuracy, precision y AUC-ROC. Alcanza 0.864 de accuracy y 0.920 de AUC-ROC, las cifras más altas de la tabla. Este modelo ofrece el mejor equilibrio para el objetivo de negocio.

La Regresión Logística con `balanced` maximiza el recall con 0.853. Detecta la mayor proporción de personas `>50K`, pero su precision de 0.553 refleja muchos falsos positivos. El Árbol de Decisión ocupa el extremo opuesto: precision de 0.788 y recall de 0.501, que deja sin detectar la mitad de la clase `>50K`.

---

## 8. Discusión

El ingreso es predecible con las variables disponibles. El Random Forest alcanza un AUC-ROC de 0.920, lo que confirma su capacidad para separar las dos clases. Este resultado responde de forma afirmativa la primera parte de la pregunta de trabajo.

Los modelos exponen un intercambio entre precision y recall. El Random Forest y el Árbol aciertan cuando predicen `>50K`, mientras la Regresión Logística balanceada recupera más casos de esa clase a costa de precision. La elección del modelo depende de la métrica que priorice la aplicación real.

Cuatro variables concentran la asociación con ingresos altos. La Regresión Logística y el Random Forest coinciden en `education-num`, `marital-status_Married-civ-spouse`, `age` y `capital-gain-log` como predictores dominantes, seguidos por `hours-per-week`. Más educación, matrimonio civil, mayor edad y ganancias de capital elevan la probabilidad de superar los $50K. Este resultado responde la segunda parte de la pregunta de trabajo.

El desequilibrio confirma la necesidad de varias métricas. El Árbol registra 0.848 de accuracy pero solo 0.501 de recall, una brecha que la accuracy sola oculta. El F1, el recall y el AUC-ROC completan la evaluación.

---

## 9. Conclusiones

El preprocesamiento produjo un conjunto coherente para los cinco modelos. La imputación diferenciada por patrón de ausencia, la transformación logarítmica de las variables de capital, el escalado por tipo de variable y la selección por ANOVA y Chi-cuadrado sostienen cada decisión con evidencia estadística.

El proyecto cumplió los requisitos del trabajo. Comparó cinco modelos, incluyó un modelo neuronal y probó al menos tres combinaciones de hiperparámetros por modelo. El Random Forest quedó como modelo final por sus cifras de accuracy, precision y AUC-ROC.

El recall de la clase `>50K` marca la principal oportunidad de mejora. Cuatro de los cinco modelos dejan escapar entre el 35% y el 50% de las personas de ingreso alto. Un trabajo futuro puede ajustar el umbral de decisión o aplicar técnicas de balanceo para elevar ese recall sin sacrificar precision.
