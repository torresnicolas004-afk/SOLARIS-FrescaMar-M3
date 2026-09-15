# Proyecto SOLARIS

Prototipo funcional para estimar el riesgo de mora de clientes
de FrescaMar S.A.S.

## Objetivo

Construir un modelo reproducible de clasificación binaria que
permita estimar el riesgo de mora y apoyar las decisiones de crédito.

## Dataset

Dataset original:
- 15.000 registros
- 11 variables
- Clase positiva: Moroso

Para modelado:
- 12.300 registros
- Al día: 10.500
- Moroso: 1.800

## Variables utilizadas

- edad
- ingreso_mensual
- anio_vinculacion
- score_externo
- nivel_educativo
- ciudad

## Modelo

Baseline:
- Regresión Logística

Challenger:
- HistGradientBoostingClassifier

Optimización:
- Optuna
- 50 trials
- Validación cruzada estratificada de 5 folds

## Resultados finales

| Métrica | Resultado |
|---|---:|
| AUC CV optimizado | 0.8692 |
| AUC Test | 0.8681 |
| Precision Moroso | 0.4470 |
| Recall Moroso | 0.7028 |
| F1 Moroso | 0.5464 |
| Threshold | 0.59 |
| Gap Train-Test | 0.0254 |

## Ejecución en Google Colab

1. Abrir el notebook ubicado en `notebooks/`.
2. Cargar `SOLARIS_FrescaMar_cartera_credito.csv`.
3. Seleccionar Entorno de ejecución → Ejecutar todo.
4. Optuna 5.0.0 se instala automáticamente.
5. Las métricas y el modelo se generan al finalizar.

## Ejecución local

```bash
pip install -r requirements.txt
