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

