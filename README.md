# Modelo predictivo y exploratorio para análisis de estructuras reticulares parametrizadas

## Descripción del proyecto

Este proyecto desarrolla un flujo de análisis de datos y machine learning para estudiar estructuras reticulares o *lattice structures* parametrizadas, generadas automáticamente en SolidWorks mediante scripts de Python.

Las geometrías analizadas son:

* BCC lattice
* Kelvin lattice
* Schwarz lattice
* EXP lattice

Cada dataset contiene combinaciones paramétricas de diseño, estado de reconstrucción CAD y propiedades físicas o geométricas calculadas, tales como volumen sólido, área superficial, masa estimada para plástico, masa estimada para acero, volumen envolvente y tiempo de generación.

El objetivo principal es construir modelos sustitutos que permitan predecir propiedades geométricas y físicas sin tener que reconstruir manualmente cada geometría en SolidWorks. Además, el proyecto busca explorar el espacio de diseño, agrupar configuraciones similares y seleccionar geometrías bajo criterios industriales.

## Preguntas de análisis

El proyecto busca responder preguntas como:

* ¿Es posible predecir propiedades físicas y geométricas de estructuras lattice parametrizadas usando machine learning?
* ¿Qué geometría entrega mayor área superficial para un volumen envolvente disponible?
* ¿Qué configuración ofrece menor masa manteniendo alta área superficial?
* ¿Qué diseños se ubican en una frontera de Pareto entre alta área, baja masa y bajo volumen sólido?
* ¿Existen grupos naturales de diseños con desempeño similar?
* ¿Qué variables geométricas influyen más en las propiedades de salida?

## Enfoques de machine learning

El proyecto combina tres enfoques:

1. Aprendizaje supervisado: modelos de regresión para predecir propiedades geométricas y físicas.
2. Aprendizaje no supervisado: clustering para agrupar diseños según desempeño.
3. Reducción de dimensionalidad: PCA, t-SNE o UMAP para visualizar el espacio de diseño.

## Estructura del repositorio

```text
PROYECTO_FINAL_CURSO_3/
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
│
├── notebooks/
│   └── 01_lattice_modeling_section1.ipynb
│
├── outputs/
│   ├── figures/
│   └── tables/
│
├── README.md
└── requirements.txt
```

## Datasets

Los datos se encuentran en la carpeta:

```text
data/raw/
```

Cada archivo Excel corresponde a una familia geométrica o a un bloque de corridas paramétricas. Los datasets son cargados directamente desde GitHub usando URLs tipo `raw`, lo que permite ejecutar el notebook de forma reproducible desde Google Colab.

## Variables principales

### Variables de entrada

Las variables de entrada dependen de cada geometría. Algunos ejemplos son:

* BCC: `D_toro`, `d_wire`, `n_arms`, `n_radial`
* Kelvin: `cell_size`, `alpha`, `wire_diameter`, `beta`, `theta`, `n_circular`
* Schwarz: `cell_size`, `node_diameter`, `thick`
* EXP: `t`, `square`, `excentricity`, `height`

### Variables de salida

Las variables de salida principales son:

* `envelope_volume_m3`
* `volume_m3`
* `surface_area_m2`
* `plastic_mass_kg`
* `steel_mass_kg`
* `elapsed_s`

En esta versión del proyecto, `envelope_volume_m3` se utiliza directamente como variable de salida disponible en los datasets. No se calcula a partir de `pitch`, debido a que `pitch` no está disponible ni aplica de forma uniforme en todas las geometrías.

## Cómo ejecutar el proyecto en Google Colab

1. Abrir el notebook ubicado en:

```text
notebooks/01_lattice_modeling_section1.ipynb
```

2. Ejecutar las celdas en orden.

3. Verificar que en el notebook estén configurados correctamente:

```python
GITHUB_USER = "TU_USUARIO_GITHUB"
REPO_NAME = "PROYECTO_FINAL_CURSO_3"
BRANCH = "main"
```

4. Confirmar que los archivos Excel estén cargados en:

```text
data/raw/
```

5. Ejecutar el notebook completo para generar el dataset limpio y los primeros reportes exploratorios.

## Instalación de dependencias

En Google Colab, las principales librerías ya están disponibles. Sin embargo, para reproducir el entorno se incluye el archivo:

```text
requirements.txt
```

También se pueden instalar las dependencias con:

```bash
pip install -r requirements.txt
```

## Librerías principales

* pandas
* numpy
* matplotlib
* seaborn
* openpyxl
* scikit-learn
* xgboost
* shap
* umap-learn

## Resultados esperados

El proyecto generará:

* Dataset consolidado y limpio.
* Reporte de calidad de datos.
* Análisis exploratorio por geometría.
* Modelos de regresión para propiedades geométricas.
* Comparación de modelos mediante MAE, RMSE, MAPE y R².
* Visualizaciones de clustering y reducción de dimensionalidad.
* Análisis de importancia de variables.
* Selección de diseños destacados bajo criterios industriales.
* Identificación de diseños candidatos en frontera de Pareto.

## Autor

Diego Andrés Rivera
Juan Sebastian Pulido

Proyecto final del Curso III: Modelado predictivo, automatización y proyectos inteligentes.
