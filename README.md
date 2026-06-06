# Modelo predictivo y exploratorio para análisis de estructuras reticulares parametrizadas

## Descripción general

Este proyecto desarrolla un flujo reproducible de análisis de datos y *machine learning* para estudiar estructuras reticulares o *lattice structures* parametrizadas, generadas automáticamente en SolidWorks mediante scripts de Python.

El objetivo principal es construir un sistema de apoyo al diseño que permita:

1. Evaluar si una combinación paramétrica CAD es viable geométricamente antes de intentar reconstruirla en SolidWorks.
2. Predecir propiedades físicas y geométricas de estructuras viables mediante modelos sustitutos.
3. Explorar el espacio de diseño por familia geométrica.
4. Comparar alternativas bajo criterios de ingeniería como área superficial, volumen sólido, volumen envolvente, porosidad, densidad relativa y masa estimada.
5. Identificar configuraciones destacadas y candidatos tipo Pareto para selección multicriterio.

Las familias geométricas analizadas son:

- **BCC lattice**
- **EXP lattice**
- **Kelvin lattice**
- **Schwarz lattice**

---

## Estado actual del notebook

La versión actual del proyecto corresponde a la iteración:

```text
01_lattice_modeling_iteracion7_section1_2A_2B_2C_REVISADA.ipynb
```

Esta iteración cubre:

| Sección | Estado | Descripción |
|---|---|---|
| Sección 1 | Completada | Problema, carga de datos, limpieza mínima, variables derivadas y datasets por familia |
| Sección 2A | Completada | EDA, visualizaciones, análisis de viabilidad, interacciones, benchmarking y Pareto |
| Sección 2B | Completada | Clasificación de viabilidad geométrica |
| Sección 2C | Completada | Regresión de propiedades físicas y geométricas |

Las secciones futuras pueden incluir clustering, reducción de dimensionalidad, selección multicriterio avanzada y despliegue de una herramienta interactiva.

---

## Preguntas de análisis

El proyecto busca responder preguntas como:

- ¿Qué combinaciones paramétricas CAD son viables o no viables geométricamente?
- ¿Qué familias geométricas presentan mayor proporción de fallas de reconstrucción?
- ¿Es posible predecir volumen, área superficial y volumen envolvente sin reconstruir cada geometría en SolidWorks?
- ¿Qué geometría entrega mayor área superficial para un volumen envolvente disponible?
- ¿Qué configuración ofrece menor volumen sólido o menor masa estimada manteniendo alta área superficial?
- ¿Qué diseños se ubican en una frontera de Pareto entre alta área, baja masa y baja densidad relativa?
- ¿Qué variables CAD influyen más en la viabilidad y en las propiedades físicas?
- ¿Qué tan útil es incorporar conocimiento geométrico mediante ratios adimensionales?

---

## Enfoques de machine learning

El proyecto combina varios enfoques:

### 1. Clasificación supervisada

Se modela la viabilidad geométrica mediante la variable:

```text
failure_bin
```

donde:

```text
0 = geometría viable
1 = geometría fallida / no viable
```

Este enfoque permite anticipar combinaciones que probablemente fallarán antes de ejecutar SolidWorks.

### 2. Regresión supervisada

Para geometrías viables, se entrenan modelos sustitutos para predecir:

- `volume_m3`
- `surface_area_m2`
- `envelope_volume_m3`
- `elapsed_s`

Las masas `plastic_mass_kg` y `steel_mass_kg` se conservan como variables de análisis industrial, pero no se modelan como targets principales porque se derivan directamente del volumen sólido y la densidad del material.

### 3. Análisis exploratorio y selección multicriterio

Se calculan métricas derivadas para comparar diseños:

- `relative_density`
- `porosity`
- `surface_to_volume_ratio`
- `surface_to_envelope_volume_ratio`
- `surface_to_plastic_mass`
- `surface_to_steel_mass`

Además, se analizan candidatos tipo Pareto para comparar compromisos entre masa, área superficial y densidad relativa.

---

## Estructura del repositorio

La estructura recomendada del repositorio es:

```text
lattice_strucures_cad-ml/
│
├── data/
│   ├── raw/
│   │   ├── bcc_batch_step_resultsA.xlsx
│   │   ├── bcc_batch_step_resultsB.xlsx
│   │   ├── exp_batch_step_results.xlsx
│   │   ├── kelvin_batch_step_results.xlsx
│   │   └── schwarz_batch_step_results.xlsx
│   │
│   └── processed/
│       └── lattice_processed_by_family.xlsx
│
├── notebooks/
│   └── 01_lattice_modeling_iteracion7_section1_2A_2B_2C_REVISADA.ipynb
│
├── outputs/
│   ├── figures/
│   └── tables/
│
├── README.md
└── requirements.txt
```

> Nota: el nombre actual del repositorio se ha manejado como `lattice_strucures_cad-ml`. Si se corrige posteriormente a `lattice_structures_cad-ml`, se deben actualizar las URLs en el notebook.

---

## Datasets

Los datasets originales se almacenan en:

```text
data/raw/
```

Cada archivo Excel corresponde a una familia geométrica o a un bloque de corridas paramétricas.

| Familia | Archivo |
|---|---|
| BCC | `bcc_batch_step_resultsA.xlsx` |
| BCC | `bcc_batch_step_resultsB.xlsx` |
| EXP | `exp_batch_step_results.xlsx` |
| Kelvin | `kelvin_batch_step_results.xlsx` |
| Schwarz | `schwarz_batch_step_results.xlsx` |

El notebook carga los archivos directamente desde GitHub usando URLs tipo `raw`, lo cual permite ejecutar el proyecto de forma reproducible desde Google Colab.

---

## Variables principales

### Variables de entrada CAD por familia

| Familia | Variables base |
|---|---|
| BCC | `D_toro`, `d_wire`, `n_arms`, `n_radial` |
| EXP | `t`, `square`, `excentricity`, `height` |
| Kelvin | `cell_size`, `alpha`, `wire_diameter`, `beta`, `theta`, `n_circular` |
| Schwarz | `cell_size`, `node_diameter`, `thick` |

### Ratios CAD adimensionales

Para incorporar conocimiento geométrico, se agregaron variables derivadas que existen antes de reconstruir la geometría y no generan fuga de información:

| Familia | Variables derivadas |
|---|---|
| BCC | `d_wire_to_D_toro` |
| EXP | `height_to_square` |
| Kelvin | `wire_diameter_to_cell_size` |
| Schwarz | `thick_to_cell_size`, `node_diameter_to_cell_size` |

### Variables de salida

Las variables de salida principales son:

- `rebuild_ok`
- `rebuild_ok_bin`
- `failure_bin`
- `envelope_volume_m3`
- `volume_m3`
- `surface_area_m2`
- `plastic_mass_kg`
- `steel_mass_kg`
- `elapsed_s`

En esta versión del proyecto, `envelope_volume_m3` se utiliza directamente como variable de salida disponible en los datasets. No se calcula a partir de `pitch`, debido a que `pitch` no está disponible ni aplica de forma uniforme en todas las geometrías.

---

## Criterios de limpieza y preparación

La limpieza de datos se hizo de forma conservadora:

- No se eliminan filas fallidas, porque son necesarias para entrenar el clasificador de viabilidad geométrica.
- Solo se eliminan duplicados estrictos: mismas variables de entrada, mismas variables de salida y misma familia geométrica.
- Se crean dataframes generales por familia:
  - `df_bcc`
  - `df_exp`
  - `df_kelvin`
  - `df_schwarz`
- Se crea un identificador `design_group_id` para evitar fuga de información cuando hay diseños paramétricos repetidos.
- Se usa `GroupShuffleSplit` y `GroupKFold` cuando aplica, para que un mismo diseño paramétrico no aparezca simultáneamente en entrenamiento y prueba.

---

## Resumen del dataset procesado

El dataset consolidado contiene:

```text
3,050 corridas CAD
```

Distribución por familia:

| Familia | Filas | Viables | Fallidas | % fallas |
|---|---:|---:|---:|---:|
| BCC | 512 | 509 | 3 | 0.59% |
| EXP | 1080 | 1003 | 77 | 7.13% |
| Kelvin | 648 | 579 | 69 | 10.65% |
| Schwarz | 810 | 620 | 190 | 23.46% |

Schwarz es la familia con mayor proporción de fallas, mientras que BCC es la más estable. Debido a que BCC solo contiene 3 fallas, no se usa como modelo individual principal de clasificación.

---

## Sección 2A — EDA y visualización

El análisis exploratorio permitió identificar:

- Diferencias importantes entre familias geométricas.
- Desbalance de clases en la variable de viabilidad.
- Relaciones no lineales entre parámetros CAD y propiedades físicas.
- Interacciones geométricas relevantes.
- Redundancia entre `volume_m3`, `plastic_mass_kg` y `steel_mass_kg`.
- Candidatos destacados bajo criterios de área, porosidad, masa y densidad relativa.

Conclusiones principales del EDA:

- **Schwarz** presenta la mayor tasa de fallas geométricas.
- **BCC** tiene alta estabilidad y muy buena eficiencia mediana de área/volumen.
- **EXP** destaca por su alta porosidad mediana.
- No existe una geometría universalmente mejor; la selección depende del criterio industrial.
- Los gráficos Pareto muestran compromisos entre masa, área superficial y densidad relativa.

---

## Sección 2B — Clasificación de viabilidad geométrica

### Objetivo

Predecir si una combinación paramétrica CAD será viable o fallará en la reconstrucción.

### Familias modeladas

Se entrenaron clasificadores para:

- EXP
- Kelvin
- Schwarz

BCC se excluyó como clasificador individual principal por tener solo 3 fallas.

### Modelos comparados

- Dummy Classifier
- Logistic Regression balanceada
- Random Forest balanceado
- XGBoost ponderado

### Métricas usadas

Como el dataset está desbalanceado, no se reporta únicamente accuracy. Se usan:

- Accuracy
- Balanced accuracy
- Precision de fallas
- Recall de fallas
- F1-score de fallas
- ROC-AUC
- PR-AUC
- Matriz de confusión
- Curvas Precision-Recall

### Mejores modelos de clasificación

| Familia | Mejor modelo | Features | F1 fallas holdout | F1 fallas CV | Recall fallas CV |
|---|---|---|---:|---:|---:|
| EXP | Random Forest balanceado | Base | 0.933 | 0.987 | 0.974 |
| Kelvin | XGBoost ponderado | Base | 0.929 | 0.915 | 0.929 |
| Schwarz | Random Forest balanceado | Base + ratios | 1.000 | 1.000 | 1.000 |

### Hallazgos de clasificación

- Los modelos dummy obtienen accuracies aparentemente altos, pero recall de fallas igual a cero.
- EXP y Kelvin presentan excelente detección de fallas, aunque no perfecta.
- Schwarz es perfectamente separable dentro del espacio paramétrico evaluado.
- En Schwarz, la variable más importante para viabilidad fue `node_diameter_to_cell_size`.
- En EXP, las variables más importantes fueron `square` y `excentricity`.
- En Kelvin, las variables más relevantes fueron `cell_size`, `alpha`, `wire_diameter` y `beta`.

---

## Sección 2C — Regresión de propiedades físicas

### Objetivo

Construir modelos sustitutos para estimar propiedades físicas y geométricas de estructuras viables sin reconstruir cada alternativa en SolidWorks.

### Targets modelados

Targets principales:

- `volume_m3`
- `surface_area_m2`
- `envelope_volume_m3`

Target secundario:

- `elapsed_s`

### Modelos comparados

Todos los modelos se implementaron mediante `Pipeline` de Scikit-learn:

- Ridge Pipeline
- Random Forest Pipeline
- XGBoost Pipeline

### Métricas usadas

- MAE
- RMSE
- Median Absolute Error
- MAPE
- R²

`MAPE` se interpreta con cautela porque puede aumentar excesivamente cuando los valores reales son muy pequeños.

### Validación

Se usó:

- `GroupShuffleSplit` para train/test.
- `GroupKFold` para validación cruzada.

Esto evita que diseños paramétricos repetidos aparezcan simultáneamente en entrenamiento y prueba.

### Mejores modelos de regresión

| Familia | Target | Mejor modelo | R² holdout | R² CV |
|---|---|---|---:|---:|
| BCC | `volume_m3` | XGBoost | 0.9995 | 0.9994 |
| BCC | `surface_area_m2` | XGBoost | 0.9994 | 0.9992 |
| BCC | `envelope_volume_m3` | Random Forest | 1.0000 | 1.0000 |
| EXP | `volume_m3` | XGBoost | 0.9999 | 0.9999 |
| EXP | `surface_area_m2` | XGBoost | 0.9999 | 0.9999 |
| EXP | `envelope_volume_m3` | XGBoost | 0.9999 | 0.9999 |
| Kelvin | `volume_m3` | XGBoost | 0.9982 | 0.9984 |
| Kelvin | `surface_area_m2` | XGBoost | 0.9982 | 0.9986 |
| Kelvin | `envelope_volume_m3` | Random Forest | 1.0000 | 1.0000 |
| Schwarz | `volume_m3` | XGBoost | 0.9984 | 0.9980 |
| Schwarz | `surface_area_m2` | Random Forest | 0.9999 | 0.9999 |
| Schwarz | `envelope_volume_m3` | Random Forest | 1.0000 | 1.0000 |

### Interpretación de regresión

Los resultados muestran que los modelos sustitutos predicen con desempeño excelente las propiedades físicas principales. XGBoost fue dominante para `volume_m3` y `surface_area_m2`, mientras que Random Forest fue especialmente efectivo para `envelope_volume_m3`, que se comporta casi de forma determinística a partir de los parámetros CAD.

El target `elapsed_s` fue más difícil de modelar, con desempeño bueno o aceptable según la familia. Esto es esperable porque el tiempo de reconstrucción CAD puede depender de factores computacionales externos además de la geometría.

---

## Flujo propuesto de uso

El flujo completo del sistema es:

```text
1. Ingresar parámetros CAD de una familia geométrica.
2. Clasificar la viabilidad geométrica.
3. Si la geometría es no viable:
      descartar o ajustar la combinación paramétrica.
4. Si la geometría es viable:
      predecir propiedades físicas con modelos de regresión.
5. Calcular masas estimadas a partir del volumen predicho.
6. Evaluar métricas derivadas:
      densidad relativa, porosidad, área/volumen, área/masa.
7. Comparar diseños y seleccionar candidatos destacados.
```

---

## Cómo ejecutar el proyecto en Google Colab

1. Abrir el notebook principal:

```text
notebooks/01_lattice_modeling_iteracion7_section1_2A_2B_2C_REVISADA.ipynb
```

2. Verificar la configuración de GitHub:

```python
GITHUB_USER = "diego-rivera-eng"
REPO_NAME = "lattice_strucures_cad-ml"
BRANCH = "main"
```

3. Confirmar que los datasets estén cargados en:

```text
data/raw/
```

4. Ejecutar las celdas en orden.

5. Revisar los resultados exportados en:

```text
outputs/tables/
outputs/figures/
```

---

## Instalación de dependencias

En Google Colab, la mayoría de librerías principales ya están disponibles. Para reproducir el entorno localmente, usar:

```bash
pip install -r requirements.txt
```

---

## Librerías principales

- pandas
- numpy
- matplotlib
- seaborn
- openpyxl
- scikit-learn
- xgboost
- shap
- umap-learn

---

## Resultados generados

El proyecto genera:

- Dataset consolidado procesado por familia.
- Reportes de calidad de datos.
- Gráficos de viabilidad geométrica.
- Heatmaps de correlación.
- Pairplots opcionales de diagnóstico.
- Gráficos de interacción CAD-física.
- Gráficos de benchmarking interfamilia.
- Gráficos Pareto absoluto y normalizado.
- Reportes de clasificación de viabilidad.
- Matrices de confusión.
- Curvas Precision-Recall.
- Reportes de threshold tuning.
- Importancias por permutación.
- Reportes de regresión por familia y target.
- Gráficos observado vs predicho.
- Tablas de conclusiones ejecutivas.

---

## Limitaciones

- Los modelos solo son confiables dentro del rango paramétrico evaluado.
- Schwarz presentó clasificación perfecta dentro del dataset, pero esto no garantiza extrapolación fuera del espacio muestreado.
- `elapsed_s` puede depender de factores computacionales externos y debe interpretarse como una aproximación de costo de reconstrucción.
- Las masas se derivan de `volume_m3` y densidad del material, por lo que no se modelan como targets independientes.
- El análisis aún puede ampliarse con clustering, reducción de dimensionalidad, SHAP y despliegue interactivo.

---

## Próximos pasos

Posibles extensiones del proyecto:

1. Aplicar PCA, t-SNE o UMAP para visualizar el espacio de diseño.
2. Automatizar la recomendación de diseños bajo criterios multicriterio.
3. Conectar los modelos sustitutos con una etapa de optimización paramétrica.

---

## Autores

- Diego Andrés Rivera
- Juan Sebastian Pulido

Proyecto final del Curso III: Modelado predictivo, automatización y proyectos inteligentes.
