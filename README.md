# Proyecto Final de Aprendizaje Automático

Proyecto final del curso de **Aprendizaje Automático** utilizando el **Adult Dataset de UCI** para desarrollar y comparar distintos modelos de clasificación.

El proyecto se organiza en Jupyter Notebooks independientes para facilitar el análisis, el preprocesamiento, el entrenamiento de modelos y la comparación de resultados.

---

## Estructura del repositorio

```text
proyecto_final_ml/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── notebooks/
│   ├── 01_EDA_faltantes_outliers.ipynb
│   ├── 02_preprocesado_seleccion_variables.ipynb
│   ├── 03_regresion_logistica.ipynb
│   ├── 04_random_forest.ipynb
│   ├── 05_perceptron.ipynb
│   ├── 06_arbol.ipynb
│   └── 07_knn.ipynb
│
├── models/
│
├── results/
│   ├── figures/
│   └── metrics/
│
├── docs/
│   ├── memoria/
│   └── presentacion/
│
├── requirements.txt
├── .gitignore
└── README.md
```

### Carpetas

- `data/raw/`: datos originales sin modificar.
- `data/processed/`: datos procesados, en caso de que sea necesario almacenarlos.
- `notebooks/`: notebooks principales del proyecto.
- `models/`: modelos entrenados guardados, por ejemplo archivos `.joblib`.
- `results/figures/`: gráficas generadas durante el análisis y evaluación.
- `results/metrics/`: tablas o archivos con métricas de los modelos.
- `docs/memoria/`: memoria escrita del proyecto.
- `docs/presentacion/`: archivos relacionados con la presentación final.

---

## Notebooks

### 01 — EDA, datos faltantes y outliers

`01_EDA_faltantes_outliers.ipynb`

Incluye:

- carga del Adult Dataset;
- exploración inicial;
- dimensiones y tipos de variables;
- estadísticas descriptivas;
- valores faltantes;
- análisis de variables numéricas;
- histogramas;
- boxplots;
- detección de outliers mediante IQR;
- transformaciones necesarias para variables con distribuciones muy sesgadas.

### 02 — Preprocesamiento y selección de variables

`02_preprocesado_seleccion_variables.ipynb`

Incluye:

- tratamiento final de datos;
- codificación de variables categóricas;
- escalado de variables numéricas;
- análisis del desbalance de clases;
- selección de atributos;
- ANOVA;
- chi-cuadrado;
- definición de las variables utilizadas por los modelos.

### 03 — Regresión Logística

`03_regresion_logistica.ipynb`

Incluye:

- división entrenamiento/prueba;
- pipeline de preprocesamiento;
- entrenamiento de Regresión Logística;
- predicciones;
- Accuracy;
- Precision;
- Recall;
- F1;
- AUC-ROC;
- interpretación de resultados.

### 04 — Random Forest

`04_random_forest.ipynb`

Entrena y evalúa un modelo `RandomForestClassifier` sobre `datos_finales.csv`
(datos ya preprocesados, con tratamiento de outliers). Utiliza una estructura
equivalente al Árbol de Decisión (división 70/15/15, modelo base, ajuste de
hiperparámetros, comparación en validación, evaluación final en prueba, matriz
de confusión e importancia de variables), lo que permite comparar los distintos
enfoques del proyecto de forma justa.

### 05 — Perceptrón multicapa

`05_perceptron.ipynb`

Entrena y evalúa un perceptrón multicapa (MLP) como modelo no lineal,
analizando el efecto de la arquitectura y la regularización sobre el desempeño.

### 06 — Árbol de Decisión

`06_arbol.ipynb`

Entrena y evalúa un `DecisionTreeClassifier` con división 70/15/15, estudio de
hiperparámetros (profundidad y mínimo de observaciones por hoja), comparación en
validación y evaluación final en prueba.

### 07 — K-Vecinos más Cercanos (KNN)

`07_knn.ipynb`

Entrena y evalúa un modelo `KNeighborsClassifier` sobre `datos_finales.csv`,
siguiendo la misma estructura que los demás modelos. Estudia el efecto del
número de vecinos (`n_neighbors`) y de la ponderación (`weights`) sobre el
desempeño. El escalado previo de las variables es indispensable para KNN, ya
que el algoritmo se basa en distancias entre observaciones.

---

# Instalación

## 1. Requisitos

Es necesario tener instalado:

- Git
- Python 3
- Visual Studio Code
- extensión de Python para VS Code
- extensión de Jupyter para VS Code

Para verificar Git:

```powershell
git --version
```

Para verificar Python:

```powershell
python --version
```

---

## 2. Clonar el repositorio

Abrir PowerShell y ubicarse en la carpeta donde se desea guardar el proyecto.

Por ejemplo:

```powershell
cd C:\Users\TU_USUARIO
```

Clonar el repositorio:

```powershell
git clone https://github.com/nikolevillaloboss/proyecto_final_ml.git
```

Entrar al proyecto:

```powershell
cd proyecto_final_ml
```

Abrirlo en Visual Studio Code:

```powershell
code .
```

---

## 3. Crear un entorno virtual

Se recomienda que cada integrante utilice un entorno virtual propio.

Desde la carpeta principal del proyecto:

```powershell
python -m venv .venv
```

Activarlo en PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Si se activó correctamente, la terminal debería mostrar algo parecido a:

```text
(.venv) PS C:\Users\TU_USUARIO\proyecto_final_ml>
```

---

## 4. Instalar las dependencias

Si el repositorio contiene un archivo `requirements.txt`:

```powershell
pip install -r requirements.txt
```

Si todavía no existe, las librerías principales utilizadas en el proyecto pueden instalarse con:

```powershell
pip install numpy pandas matplotlib scikit-learn jupyter ucimlrepo joblib
```

Después de agregar nuevas dependencias al proyecto, actualizar `requirements.txt`:

```powershell
pip freeze > requirements.txt
```

---

# Uso de Git

## Antes de comenzar a trabajar

Siempre actualizar primero la copia local:

```powershell
git pull
```

Esto es especialmente importante cuando dos personas trabajan sobre el mismo repositorio.

---

## Revisar cambios

Para ver qué archivos fueron modificados:

```powershell
git status
```

---

## Agregar cambios

Agregar todos los cambios:

```powershell
git add .
```

También se puede agregar un único archivo:

```powershell
git add notebooks\03_regresion_logistica.ipynb
```

---

## Crear un commit

Después de agregar los archivos:

```powershell
git commit -m "Descripcion breve del cambio"
```

Ejemplos:

```powershell
git commit -m "Add EDA notebook"
```

```powershell
git commit -m "Update logistic regression model"
```

```powershell
git commit -m "Add Random Forest evaluation"
```

Los mensajes de commit deben describir brevemente qué se modificó.

---

## Subir los cambios a GitHub

```powershell
git push
```

Flujo habitual:

```powershell
git pull
git status
git add .
git commit -m "Descripcion del cambio"
git push
```

---

# Trabajo colaborativo

Cada integrante debe clonar el repositorio en su propia computadora.

No es necesario compartir manualmente carpetas o archivos por WhatsApp, Drive u otros medios: GitHub mantiene una copia central del proyecto.

Antes de comenzar:

```powershell
git pull
```

Después de trabajar:

```powershell
git add .
git commit -m "Descripcion de los cambios"
git push
```

## Importante con Jupyter Notebooks

Es recomendable evitar que ambas personas modifiquen simultáneamente el mismo `.ipynb`, ya que los notebooks pueden producir conflictos difíciles de resolver.

Una forma sencilla de organizarse es repartir notebooks. Por ejemplo:

```text
Persona A:
01_EDA_faltantes_outliers.ipynb
03_regresion_logistica.ipynb

Persona B:
02_preprocesado_seleccion_variables.ipynb
04_random_forest.ipynb
```

Si ambos necesitan modificar el mismo notebook, conviene hacer primero:

```powershell
git pull
```

y avisar al compañero antes de comenzar a editarlo.

---

# Guardar modelos entrenados

Los notebooks pertenecen a:

```text
notebooks/
```

Los modelos ya entrenados pueden guardarse en:

```text
models/
```

Por ejemplo:

```python
import joblib

joblib.dump(
    modelo,
    "../models/random_forest.joblib"
)
```

Para volver a cargarlo:

```python
modelo = joblib.load(
    "../models/random_forest.joblib"
)
```

---

# Archivos que no deberían subirse

Se recomienda utilizar `.gitignore` para evitar subir archivos innecesarios.

Ejemplo:

```gitignore
.venv/
__pycache__/
*.pyc
.ipynb_checkpoints/
.env
.DS_Store
```

Si los datasets o modelos llegan a ser demasiado grandes, también pueden excluirse del repositorio.

---

# Flujo recomendado del proyecto

```text
Adult Dataset
      ↓
EDA inicial
      ↓
Datos faltantes y outliers
      ↓
Preprocesamiento
      ↓
Selección de variables
      ↓
Train / Test
      ↓
Modelos de clasificación
      ↓
Ajuste de hiperparámetros
      ↓
Evaluación
      ↓
Comparación de resultados
      ↓
Conclusiones
```

---

## Repositorio

GitHub:

`https://github.com/nikolevillaloboss/proyecto_final_ml`
