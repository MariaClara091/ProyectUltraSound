# Ultrasound Nerve Segmentation
## Deep Learning Project — Brachial Plexus Segmentation
María Clara Ávila Chinchia, Mateo José Giraldo Castillo, Miguel Angel Lugo Cozzarelli, Hector David SanJuan Fábregas

```{admonition} Contexto clínico
:class: tip
La segmentación automática del Plexo Braquial en imágenes de ultrasonido es un paso crítico para la anestesia regional guiada por imagen. Una identificación precisa del nervio permite insertar catéteres de manejo del dolor con mayor exactitud, reduciendo la dependencia de narcóticos y acelerando la recuperación post-quirúrgica.
```

---

## ¿Qué hace este proyecto?

Este proyecto implementa un **benchmark completo de 5 modelos de Deep Learning** para segmentación semántica binaria del Plexo Braquial en imágenes de ultrasonido, utilizando el dataset público de la competencia [Kaggle Ultrasound Nerve Segmentation (2016)](https://www.kaggle.com/c/ultrasound-nerve-segmentation).

**Problema:** dado un frame de ultrasonido del cuello (580×420 px, escala de grises), predecir a nivel de píxel si contiene el Plexo Braquial y, si es así, qué región lo contiene.

**Desafío principal:** el 60% de las imágenes no contienen el nervio — un desbalance severo que penaliza fuertemente el Dice coefficient global (métrica oficial de Kaggle).

---

## Dataset

| Característica | Valor |
|---|---|
| Fuente | Kaggle Ultrasound Nerve Segmentation (2016) |
| Imágenes de entrenamiento | 5,635 frames |
| Imágenes de test | 5,508 frames |
| Pacientes únicos | 47 |
| Resolución | 580 × 420 px, escala de grises |
| Con nervio presente | ~40% (2,269 imágenes) |
| Sin nervio presente | ~60% (3,366 imágenes) |
| Pares contradictorios detectados | 33 (SSIM > 0.90, etiqueta distinta) |

---

## Estructura del libro

Este libro está organizado en cuatro capítulos que siguen el flujo completo del proyecto:

::::{grid} 2
:::{grid-item-card} Capítulo 1 — EDA
:link: Notebooks/01_EDA
:link-type: doc
Análisis Exploratorio exhaustivo: calidad del dataset, detección de contradictorios SSIM, distribución espacial del nervio, split por paciente sin data leakage, y parámetros de normalización.
:::
:::{grid-item-card} Capítulo 2 — Estado del Arte
:link: Notebooks/02_literatura
:link-type: doc
Revisión de 10+ papers con fichas técnicas del Top 5, comparación crítica estructurada, e identificación del gap en la literatura.
:::
:::{grid-item-card} Capítulo 3 — Modelos
:link: Notebooks/02_models
:link-type: doc
Implementación de 5 benchmarks bajo el mismo entorno experimental: U-Net, Attention U-Net, ResU-Net, Dual-head U-Net y TU-Net.
:::
:::{grid-item-card} Capítulo 4 — Benchmark
:link: Notebooks/03_benchmark
:link-type: doc
Análisis estadístico riguroso: Friedman test, Nemenyi post-hoc, Wilcoxon con corrección de Bonferroni, IC 95%, análisis cualitativo y discusión crítica.
:::
::::

---

## Resultado principal

```{admonition} Hallazgo central
:class: important
El **Dual-head U-Net** obtuvo **Dice = 0.6032 ± 0.17**, siendo estadísticamente superior a los otros 4 modelos en todas las comparaciones pareadas (Friedman χ² = 469.63, p ≈ 10⁻¹⁰⁰). 

El cuello de botella del problema **no es la arquitectura del segmentador** sino el desbalance entre imágenes con y sin nervio. La rama clasificadora explícita (que predice presencia/ausencia del nervio antes de segmentar) produjo un salto del +235% en Dice respecto al U-Net baseline.
```

| Ranking | Modelo | Dice (media ± std) | Mejora vs baseline |
|:---:|---|---|---|
| 🥇 1 | Dual-head U-Net | 0.6032 ± 0.17 | +235% |
| 🥈 2 | Attention U-Net | 0.2567 ± 0.29 | +43% |
| 🥉 3 | ResU-Net | 0.2345 ± 0.26 | +30% |
| 4 | TU-Net | 0.1971 ± 0.29 | +10% |
| 5 | U-Net (baseline) | 0.1798 ± 0.29 | — |

---

## Hallazgos metodológicos

**1. La estrategia dual supera a la innovación arquitectónica pura.**
Cuatro modelos con arquitecturas radicalmente distintas (U-Net, Attention, Residual, Transformer) quedaron en el rango 0.18–0.26, mientras que el modelo que ataca directamente el desbalance del dataset llegó a 0.60. Esto cuantifica empíricamente que el problema dominante es el desbalance, no la capacidad del segmentador.

**2. Atención > Profundidad > Transformers (para datasets pequeños).**
Para este problema con target pequeño (~2,000 px²) en imágenes ruidosas, los attention gates son más efectivos que los bloques residuales o los mecanismos de self-attention global. Los Transformers requieren datos masivos que este dataset (3,836 imágenes de train) no puede proveer.

**3. Split por paciente ≠ split aleatorio.**
Los scores de la literatura (65–79% Dice) usan splits aleatorios donde frames del mismo paciente caen en train y test, generando data leakage. Nuestro split por paciente completo es metodológicamente más estricto y refleja el escenario clínico real.

---

## Gap identificado para trabajo futuro

Ningún paper publicado ha combinado el principio del clasificador explícito (Van Boxtel et al., 2021) con arquitecturas modernas como HA-SAM (MICCAI 2025) o MedSAM-2. Esta combinación — dual-head + foundation model preentrenado en imágenes médicas — representa la dirección más prometedora para superar el 79.59% reportado como SOTA.

---

## Autores y reproducibilidad

- **Dataset:** [Kaggle Ultrasound Nerve Segmentation](https://www.kaggle.com/c/ultrasound-nerve-segmentation)
- **Semillas fijas:** 42, 123, 777 (3 runs por modelo)
- **Split:** 70/15/15 por paciente completo, estratificado
- **Normalización:** mean=0.3877, std=0.2181 (calculados solo en train)
- **Hardware:** documentado en `Notebooks/02_models.ipynb`
- **Código:** `docs/Notebooks/` — EDA, modelos, benchmark
- **Resultados:** `docs/results/` — JSON por modelo, CSVs, figuras

```{tableofcontents}
```

