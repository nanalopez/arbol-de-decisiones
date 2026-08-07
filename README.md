# Árbol de Decisión para Predicción de Riesgo de Infarto Cardíaco

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1mHE1kzMRHLN-8k0zNjQrgVVbun52kfvK?usp=sharing)
> **Sugerencia:** abre el notebook en una nueva pestaña (Ctrl+clic o ⌘+clic) para conservar este repositorio abierto.

Árbol de decisión construido con scikit-learn para estimar el riesgo de infarto cardíaco a partir de variables clínicas de rutina (edad, tipo de dolor de pecho, presión arterial, colesterol, entre otras).

## Dataset

Versión del conjunto de datos clásico de enfermedad cardíaca de Cleveland: 294 pacientes, 14 variables originales.

| Variable objetivo | Significado |
| ------------------ | ------------ |
| `num` = 0 | Sin riesgo de infarto (188 pacientes, 63.9%) |
| `num` = 1 | Con riesgo de infarto (106 pacientes, 36.1%) |

Fuente del dataset:

- Kaggle: `https://www.kaggle.com/datasets/imnikhilanand/heart-attack-prediction`
- El notebook incluye una celda para subir el archivo `data.csv` desde tu equipo o desde el panel de archivos de Google Colab.

**Nota sobre los datos:** el archivo original marca los valores faltantes con el símbolo `?`. Las columnas `slope`, `ca` y `thal` tenían más del 60% de valores faltantes (64.6%, 99.0% y 90.5% respectivamente) y se descartaron del análisis. Las demás columnas con valores faltantes menores se completaron con la mediana.

## Variables utilizadas

Tras la limpieza, el modelo usa 10 variables predictoras: `age`, `sex`, `cp`, `trestbps`, `chol`, `fbs`, `restecg`, `thalach`, `exang`, `oldpeak`.

## Modelo

```
DecisionTreeClassifier
Ajuste de hiperparámetros vía GridSearchCV (5-fold), optimizando F1-score
```

| Hiperparámetro | Valores explorados | Mejor valor encontrado |
| --- | --- | --- |
| `max_depth` | 3, 4, 5, 6, 8, None | 8 |
| `min_samples_split` | 2, 5, 10 | 2 |
| `min_samples_leaf` | 1, 2, 4 | 2 |
| `criterion` | gini, entropy | gini |

Se eligió un árbol de decisión en lugar de una red neuronal por dos razones: con un dataset de apenas 294 pacientes, un árbol bien ajustado iguala o supera a una red neuronal; y su estructura es directamente interpretable, algo especialmente valioso en un contexto clínico.

## Requisitos

Instala las dependencias ejecutando:

```
pip install scikit-learn pandas numpy matplotlib seaborn
```

Versión recomendada de Python: 3.8 o superior.

## Cómo usarlo

1. Abre `arbol_decision_infarto.ipynb` en [Google Colab](https://colab.research.google.com/) o en Jupyter Notebook.
2. Ejecuta las celdas en orden, de arriba hacia abajo.
3. En el Bloque 2, sube tu archivo `data.csv`, ya sea arrastrándolo al panel de archivos de Colab (recomendado) o con el botón **"Choose Files"**.
4. El modelo se entrenará automáticamente y mostrará:

   - Accuracy, precisión, recall, F1-score y AUC-ROC sobre el conjunto de prueba.
   - Matriz de confusión y curva ROC.
   - Visualización del árbol e importancia de cada variable.
   - Análisis de equidad del modelo por sexo.
   - Predicción de ejemplo para un paciente nuevo.

## Flujo del pipeline

| Bloque | Descripción |
| --- | --- |
| 1 | Instalar e importar librerías |
| 2 | Subir el dataset (`data.csv`) |
| 3 | Exploración inicial de los datos |
| 4 | Limpieza y preprocesamiento |
| 5 | División entrenamiento / validación / prueba (70/15/15) |
| 6 | Entrenamiento con ajuste de hiperparámetros (`GridSearchCV`) |
| 7 | Evaluación sobre el conjunto de prueba |
| 8 | Matriz de confusión y curva ROC |
| 9 | Visualización del árbol e importancia de variables |
| 10 | Evaluación de equidad por sexo |
| 11 | Prueba con un paciente nuevo |

## Resultados

Sobre el conjunto de prueba (45 pacientes, nunca vistos durante el entrenamiento):

| Métrica | Valor |
| --- | --- |
| Accuracy | 77.8% |
| Precisión | 66.7% |
| Recall | 75.0% |
| F1-score | 70.6% |
| AUC-ROC | 0.873 |

La variable más importante para el modelo fue la angina inducida por ejercicio (`exang`), que además es la pregunta raíz del árbol, seguida de la frecuencia cardíaca máxima (`thalach`) y el tipo de dolor de pecho (`cp`).

**Equidad por sexo:** el accuracy fue de 81.8% en mujeres (n=11) y 76.5% en hombres (n=34). El subgrupo de mujeres en el conjunto de prueba es muy pequeño, por lo que este resultado no debe interpretarse como evidencia sólida de que el modelo es equitativo; sería necesario repetirlo sobre un conjunto de datos considerablemente más grande.

## Estructura del proyecto

```
arbol-decision-infarto/
├── arbol_decision_infarto.ipynb   # Notebook con el código completo
├── data.csv                       # Dataset (no incluido en el repo, ver sección Dataset)
└── README.md                      # Este archivo
```
