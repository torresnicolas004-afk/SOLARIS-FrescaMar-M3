# Proyecto SOLARIS

Prototipo funcional para estimar el riesgo de mora de clientes de **FrescaMar S.A.S.**, desarrollado para la asignatura Proyecto de Ciencia de Datos 2.

## Objetivo

Construir un modelo reproducible de clasificación binaria que permita estimar el riesgo de mora de los clientes y apoyar las decisiones de crédito de FrescaMar S.A.S.

El modelo funciona como herramienta de apoyo a la decisión y no como un sistema automático de aprobación o negación de crédito.

## Dataset

El dataset original contiene:

- 15.000 registros.
- 11 variables.
- 15.000 identificadores de cliente únicos.
- Clase de interés: `Moroso`.

Para el modelado se excluyeron los registros con estado `Cancelado`, obteniendo:

- 12.300 registros para modelado.
- 10.500 clientes `Al día`.
- 1.800 clientes `Moroso`.
- Proporción de morosos: 14,63 %.

Durante el preprocesamiento también se normalizaron 73 variantes de ciudad en 12 categorías finales y se verificó que no quedaran categorías sin resolver.

## Variables utilizadas

El modelo utiliza únicamente variables consideradas disponibles antes de la decisión de crédito:

- `edad`
- `ingreso_mensual`
- `anio_vinculacion`
- `score_externo`
- `nivel_educativo`
- `ciudad`

Se excluyeron variables como `saldo_actual`, `cuotas_pagadas` y `cuotas_totales` debido al riesgo de data leakage asociado a su temporalidad.

## Modelado

### Línea base

- Regresión Logística.

### Modelo challenger

- HistGradientBoostingClassifier.

### Optimización

- Optuna 5.0.0.
- 50 trials.
- Validación cruzada estratificada de 5 folds.
- Semilla global: 42.

La selección del threshold operativo se realizó exclusivamente mediante predicciones Out-of-Fold sobre el conjunto de entrenamiento.

## Resultados finales

| Métrica | Resultado |
| --- | ---: |
| AUC CV optimizado | 0.8692 |
| AUC Test | 0.8681 |
| Precision Moroso | 0.4470 |
| Recall Moroso | 0.7028 |
| F1 Moroso | 0.5464 |
| Threshold | 0.59 |
| Gap Train-Test | 0.0254 |

El modelo final supera de forma clara la línea base logística y alcanza un recall superior al 70 % para la clase Moroso.

## Estructura del repositorio

```text
SOLARIS-FrescaMar-M3/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── data/
│   ├── README.md
│   └── raw/
│       └── SOLARIS_FrescaMar_cartera_credito.csv
│
├── notebooks/
│   └── M3_PCD2_TorresCastellanos_Prototipo.ipynb
│
├── models/
│   └── solaris_pipeline_v1.pkl
│
└── outputs/
    ├── auditoria_data_leakage.csv
    └── metricas_m3.json
```

## Reproducibilidad

El proyecto fue validado mediante una **ejecución limpia desde el repositorio de GitHub en Google Colab**.

Para realizar la prueba:

1. Se clonó una copia nueva del repositorio desde GitHub.
2. Se instalaron las dependencias declaradas en `requirements.txt`.
3. El notebook encontró automáticamente el dataset almacenado en `data/raw/`.
4. Se ejecutó el notebook completo mediante `nbconvert`.
5. La ejecución finalizó sin errores y reprodujo las métricas finales del modelo.

La prueba ejecutó nuevamente todo el flujo:

```text
Carga de datos
→ limpieza y normalización
→ análisis exploratorio
→ auditoría de data leakage
→ Train/Test split
→ baseline
→ modelo challenger
→ optimización con Optuna
→ selección del threshold
→ evaluación final
→ serialización del modelo
```

### Entorno validado

- Python 3.13.15
- NumPy 2.1.3
- Pandas 2.2.3
- Scikit-learn 1.6.1
- Matplotlib 3.10.0
- Joblib 1.6.0
- Optuna 5.0.0

## Ejecución en Google Colab

Para reproducir el proyecto desde GitHub se recomienda comenzar desde un entorno limpio de Google Colab.

### 1. Clonar el repositorio

```python
!git clone https://github.com/torresnicolas004-afk/SOLARIS-FrescaMar-M3.git
```

### 2. Entrar al repositorio

```python
%cd /content/SOLARIS-FrescaMar-M3
```

### 3. Instalar las dependencias

```python
!pip install -q -r requirements.txt
```

### 4. Ejecutar el notebook completo

```python
!jupyter nbconvert \
    --to notebook \
    --execute notebooks/M3_PCD2_TorresCastellanos_Prototipo.ipynb \
    --output-dir /content \
    --output M3_prueba_reproducibilidad.ipynb \
    --ExecutePreprocessor.timeout=1800
```

Al finalizar correctamente se genera una copia ejecutada del notebook en `/content/M3_prueba_reproducibilidad.ipynb`.

## Ejecución local

Clonar el repositorio:

```bash
git clone https://github.com/torresnicolas004-afk/SOLARIS-FrescaMar-M3.git
```

Entrar a la carpeta del proyecto:

```bash
cd SOLARIS-FrescaMar-M3
```

Instalar las dependencias:

```bash
pip install -r requirements.txt
```

El dataset debe encontrarse en:

```text
data/raw/SOLARIS_FrescaMar_cartera_credito.csv
```

Después se puede abrir el notebook ubicado en:

```text
notebooks/M3_PCD2_TorresCastellanos_Prototipo.ipynb
```

y ejecutarlo completamente.

## Archivos generados

El prototipo genera, entre otros, los siguientes artefactos:

- `models/solaris_pipeline_v1.pkl`: pipeline final serializado.
- `outputs/metricas_m3.json`: métricas y configuración final.
- `outputs/auditoria_data_leakage.csv`: auditoría de variables utilizadas y excluidas.

El archivo `.pkl` fue recargado mediante Joblib y se verificó que reproduce correctamente las predicciones del pipeline original.

## Limitaciones

El dataset actual corresponde a un snapshot y no contiene `fecha_corte`, `fecha_evento` ni snapshots históricos de las variables.

Por esta razón, la validación actual utiliza un split IID estratificado. Antes de una implementación productiva se requiere:

- validación out-of-time;
- definición temporal formal del evento de mora;
- análisis de calibración;
- monitoreo de drift;
- validación del impacto de negocio con FrescaMar.

También se identificó que la ausencia de `score_externo` presenta diferencias importantes entre las clases, por lo que este patrón debe seguir siendo monitoreado.

## Autor

**Nicolás Torres Castellanos**  
Proyecto de Ciencia de Datos 2  
Universidad de La Sabana  
Periodo 2026-2
