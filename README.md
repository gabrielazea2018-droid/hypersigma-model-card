# HyperSIGMA: Modelo Fundacional para la Comprensión de la Inteligencia Hiperespectral

## Descripción general

HyperSIGMA (*HyperSpectral IntelliGence coMprehension foundAtion model*) es un modelo fundacional basado en Vision Transformer, diseñado específicamente para interpretar imágenes hiperespectrales (HSI) de teledetección. Escala a más de mil millones de parámetros y fue preentrenado de forma autosupervisada (Masked Autoencoder) con HyperGlobal-450K, un conjunto global de 447.072 parches hiperespectrales de 64×64 píxeles obtenidos de los satélites EO-1 y GF-5. Incorpora un mecanismo de atención de muestreo disperso (*sparse sampling attention*, SSA) para reducir la redundancia espacial y espectral, y un módulo de mejora espectral que fusiona las características de dos subredes, una espacial y una espectral. Con un ajuste fino por tarea, ofrece una solución unificada para clasificación, detección de objetivos y de anomalías, detección de cambios, desmezclado espectral, eliminación de ruido y superresolución, con aplicaciones en planificación urbana, agricultura de precisión y monitoreo ambiental. Fue desarrollado por el grupo WHU-Sigma (Universidad de Wuhan y colaboradores) y publicado en IEEE TPAMI (2025). Este repositorio contiene la ficha del modelo (model card) elaborada con fines académicos.

## Ficha del modelo (Model Card)

La ficha completa, elaborada siguiendo a Mitchell et al. (2019) y la plantilla de Hugging Face, está en este mismo repositorio:

[MODEL\_CARD.md](./MODEL_CARD.md)

## Autora de la ficha

* **Nombre:** Gabriela Zea
* **Contacto:** gabrielazea2018@gmail.com
* **GitHub:** \[@gabrielazea2018-droide](https://github.com/gabrielazea2018-droide)
* **Curso / institución: Inteligencia artificial aplicada al territorio / Universidad Distrital Francisco José de Caldas**

## Fuentes del modelo

* Artículo (IEEE TPAMI, 2025): https://ieeexplore.ieee.org/document/10949864
* Preprint: https://arxiv.org/abs/2406.11519
* Repositorio oficial (licencia Apache-2.0): https://github.com/WHU-Sigma/HyperSIGMA
* Página del proyecto: https://whu-sigma.github.io/HyperSIGMA/
* Pesos preentrenados: https://huggingface.co/WHU-Sigma/HyperSIGMA
* Conjunto de preentrenamiento HyperGlobal-450K: https://huggingface.co/datasets/WHU-Sigma/HyperGlobal-450K

## Referencias

* Wang, D., Hu, M., Jin, Y., et al. (2025). *HyperSIGMA: Hyperspectral Intelligence Comprehension Foundation Model*. IEEE TPAMI, 47(8), 6427-6444. https://doi.org/10.1109/TPAMI.2025.3557581
* Mitchell, M., et al. (2019). *Model cards for model reporting*. FAT\* 2019, 220-229. https://arxiv.org/abs/1810.03993
* Plantilla de Hugging Face: https://github.com/huggingface/huggingface\_hub/tree/main/src/huggingface\_hub/templates

