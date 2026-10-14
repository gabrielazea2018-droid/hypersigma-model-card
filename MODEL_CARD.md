# Model Card for HyperSIGMA

HyperSIGMA es un modelo fundacional basado en Vision Transformer para la interpretación de imágenes hiperespectrales (HSI). Escala a más de mil millones de parámetros, se preentrena con el conjunto global HyperGlobal-450K y se ajusta (*fine-tuning*) para tareas de alto nivel (clasificación, detección de objetivos, anomalías y cambios) y de bajo nivel (desmezclado, eliminación de ruido y superresolución).

> \*\*Nota:\*\* esta ficha documenta un modelo desarrollado por terceros (Wang et al.). Fue elaborada por Gabriela Zea con fines académicos, a partir del artículo (IEEE TPAMI 2025 / arXiv 2406.11519, incluido su apéndice), el repositorio oficial de GitHub y el repositorio de pesos en Hugging Face. Las cifras de rendimiento son las reportadas por los autores y no fueron reproducidas de forma independiente.

## Model Details

### Model Description

HyperSIGMA (*HyperSpectral IntelliGence coMprehension foundAtion model*) está compuesto por dos subredes paralelas preentrenadas por separado:

* **SpatViT (subred espacial):** tokeniza parches espaciales de la imagen (tamaño de parche 8 sobre entradas de 64×64, es decir, 64 tokens).
* **SpecViT (subred espectral):** tokeniza canales espectrales agrupando bandas adyacentes y proyectándolas a un espacio de embeddings.

Ambas se preentrenan con *Masked Autoencoder* (MAE) y luego se les incorpora la **atención de muestreo disperso (SSA)**: se conserva la autoatención completa solo en las capas i·(n/4) (i = 1, 2, 3, 4) y el resto se sustituye por SSA, que aprende posiciones de muestreo propias para cada consulta. Las características de ambas subredes se combinan con el **módulo de mejora espectral (SEM)**, que calibra las características espaciales con información espectral. Cada subred existe en versiones ViT-Base, ViT-Large y ViT-Huge; con ViT-Huge en ambas el modelo supera los 1.000 millones de parámetros. La subred espacial por sí sola se denomina **SpatSIGMA**.

* **Developed by:** Di Wang, Meiqi Hu, Yao Jin, Yuchun Miao, Jiaqi Yang, Yichu Xu, Xiaolei Qin, Jiaqi Ma, Lingyu Sun, Chenxing Li, Chuan Fu, Hongruixuan Chen, Chengxi Han, Naoto Yokoya, Jing Zhang, Minqiang Xu, Lin Liu, Lefei Zhang, Chen Wu, Bo Du, Dacheng Tao y Liangpei Zhang. Afiliaciones: Wuhan University, Chongqing University, The University of Tokyo, National Engineering Research Center of Speech and Language Information Processing y Nanyang Technological University.
* **Funded by:** Las fuentes revisadas no detallan subvenciones. Los agradecimientos indican que el preentrenamiento contó con el apoyo de Dawning Information Industry Co., Ltd. y que el ajuste fino se realizó en el sistema de supercomputación del Supercomputing Center of Wuhan University.
* **Shared by:** Organización WHU-Sigma (GitHub y Hugging Face)
* **Model type:** Modelo fundacional visual (Vision Transformer de doble rama espacial-espectral con atención de muestreo disperso) preentrenado de forma autosupervisada
* **Language(s) (NLP):** No aplica (modelo de visión; no procesa lenguaje natural)
* **License:** Apache-2.0 (según el repositorio de GitHub y la página del modelo en Hugging Face)
* **Finetuned from model:** No aplica. Los pesos se obtienen mediante preentrenamiento MAE sobre HyperGlobal-450K y luego se ajustan a cada tarea.

### Model Sources 

* **Repository:** https://github.com/WHU-Sigma/HyperSIGMA
* **Weights:** https://huggingface.co/WHU-Sigma/HyperSIGMA
* **Paper:** https://arxiv.org/abs/2406.11519 | IEEE TPAMI: https://ieeexplore.ieee.org/document/10949864
* **Demo:** Página del proyecto: https://whu-sigma.github.io/HyperSIGMA/ (no se encontró demo interactiva)

## Uses

### Direct Use

Extracción de representaciones espaciales y espectrales de imágenes hiperespectrales de observación de la Tierra para investigación y desarrollo en teledetección, usando los pesos preentrenados como punto de partida.

### Downstream Use

Tras el ajuste fino, los autores lo evalúan en: clasificación de imágenes hiperespectrales (HIC), detección de objetivos (HTD), detección de anomalías (HAD), detección de cambios hiperespectrales (HCD), desmezclado espectral (HIU), eliminación de ruido (HID) y superresolución (HSR), además de clasificación colaborativa hiperespectral-multiespectral y detección de cambios en imágenes multiespectrales (Sentinel-2). Como caso de aplicación real, evalúan la detección de derrames de petróleo en el Golfo de México. Las aplicaciones mencionadas son planificación urbana, agricultura de precisión y monitoreo ambiental.

### Out-of-Scope Use

* Decisiones de alto impacto sobre personas o comunidades (vigilancia, aplicación de la ley, evaluación de predios) basadas únicamente en las salidas del modelo.
* Uso con sensores, rangos espectrales o condiciones de adquisición muy distintos de EO-1 y GF-5 sin validación previa.
* Aplicaciones críticas (gestión de emergencias, seguridad, cumplimiento normativo ambiental) sin verificación independiente con datos de campo.
* Despliegue en entornos con poco cómputo sin adaptación: las versiones grandes son pesadas (el checkpoint de SpatViT-Huge pesa unos 8 GB).

## Bias, Risks, and Limitations

* **Cobertura de sensores y geografía:** el preentrenamiento usa solo dos sensores satelitales, EO-1 (imágenes de 2011-2017) y GF-5 (imágenes de China). El rendimiento puede degradarse con otros sensores, regiones, coberturas o épocas poco representadas.
* **Tipo de dato:** el preentrenamiento usa valores digitales o de radiancia, no de reflectancia. Los autores sostienen que el modelo igualmente captura relaciones espectrales en datos de reflectancia, pero conviene validarlo.
* **Desajuste de canales y tamaños:** como el número de bandas varía entre conjuntos de datos, la capa de embedding espacial suele reinicializarse y reentrenarse en el ajuste fino. Los embeddings posicionales se interpolan, de modo que no se aprovecha por completo lo preentrenado en esa capa.
* **Aporte espectral limitado en algunos casos:** los propios autores reconocen que HyperSIGMA mejora poco a SpatSIGMA en ciertos escenarios, y lo atribuyen a la dificultad de recuperar canales completos en el preentrenamiento espectral.
* **Escasez de anotaciones:** las tareas hiperespectrales suelen contar con pocas muestras etiquetadas, lo que condiciona el ajuste fino y la evaluación; muchos resultados usan 10-50 muestras por clase.
* **Costo computacional y ambiental:** entrenar y ajustar un modelo de miles de millones de parámetros requiere hardware especializado.
* **Interpretabilidad limitada:** como la mayoría de modelos fundacionales, sus decisiones son difíciles de explicar.
* **Seguridad de los archivos de pesos:** los checkpoints (.pth) están en formato pickle de PyTorch y Hugging Face lo marca como potencialmente inseguro. Descárguelos solo de fuentes oficiales y cárguelos con precaución (por ejemplo, `torch.load(..., weights\_only=True)`).
* **Uso dual:** la teledetección hiperespectral (detección de objetivos y anomalías) puede emplearse en contextos sensibles.

### Recommendations

Validar el modelo con datos propios de la región y el sensor de interés, reportar el desempeño por subgrupos (zonas, coberturas, sensores) y no utilizarlo como única base para decisiones de alto impacto. Contrastar los resultados con datos de campo.

## How to Get Started with the Model

Instrucciones oficiales en https://github.com/WHU-Sigma/HyperSIGMA. El repositorio incluye carpetas con instrucciones de ajuste fino por tarea: `ImageClassification`, `HyperspectralDetection`, `ChangeDetection`, `HyperspectralUnmixing`, `ImageDenoising`, `ImageSuperResolution`, `MultispectralCD` y `Pretrain`.

```bash
git clone https://github.com/WHU-Sigma/HyperSIGMA.git
cd HyperSIGMA
# Descargue los pesos desde Hugging Face (p. ej. SpatViT-Base):
# https://huggingface.co/WHU-Sigma/HyperSIGMA/blob/main/spat-vit-base-ultra-checkpoint-1599.pth
# Luego siga el README de la carpeta de la tarea que le interese.
```

Pesos preentrenados disponibles (Spatial\_MAE y Spectral\_MAE, cada uno en versiones ViT-B, ViT-L y ViT-H) en Hugging Face y Baidu Drive. También hay checkpoints para eliminación de ruido.

## Training Details

### Training Data

**HyperGlobal-450K**, conjunto global construido por los autores a partir de los satélites **EO-1** (imágenes adquiridas en 2011-2017) y **GF-5** (imágenes de China). Contiene **447.072 parches hiperespectrales de 64×64 píxeles**: 247.072 de EO-1 y 200.000 de GF-5. Según el artículo, equivale a más de 20 millones de imágenes de tres bandas sin solapamiento de canales. El repositorio indica que los parches provienen de 1.701 imágenes (1.486 de EO-1 y 215 de GF-5B), con cientos de bandas espectrales, distribuidas globalmente. Los autores seleccionaron las imágenes según contenido de nubes, ubicación y bandas. El conjunto está disponible en https://huggingface.co/datasets/WHU-Sigma/HyperGlobal-450K.

### Training Procedure

#### Preprocessing

* Recorte en parches de 64×64.
* Reducción de dimensionalidad mediante selección aleatoria de canales continuos, que preserva el orden espectral y aumenta la diversidad de los datos. Se usa el mismo número de canales para todas las imágenes, ya que los sensores difieren en número de bandas.
* Para la subred espectral, las bandas adyacentes se agrupan por promedio y se tokenizan por canal.

#### Training Hyperparameters

* **Training regime:** Preentrenamiento autosupervisado MAE, 1.600 epochs, proporción de enmascarado 0,75 (espacial y espectral), tamaño de parche espacial 8. SSA con 8 puntos de muestreo (ablación del artículo). Ejemplos de comandos del repositorio: `blr 1.5e-4`, batch size 32 (SpatViT-L) y 16 (SpecViT-H), con `--norm\_pix\_loss`. La precisión numérica (fp32, fp16, bf16) no se reporta en las fuentes revisadas.
* Ajuste fino (ejemplo de la prueba de robustez adversarial): AdamW, tasa de aprendizaje inicial 0,0005, weight decay 0,00005, 1.000 epochs.

#### Speeds, Sizes, Times

* Más de 1.000 millones de parámetros con ViT-Huge en ambas ramas.
* Tamaños de checkpoint en Hugging Face: SpatViT-B ≈ 1,44 GB, SpatViT-L ≈ 4,07 GB, SpatViT-H ≈ 8,03 GB, SpecViT-B ≈ 1,39 GB.
* Los tiempos de entrenamiento y el coste de preentrenamiento se detallan en el apéndice del artículo (Sec. C), que no se reproduce aquí.

## Evaluation

### Testing Data, Factors \& Metrics

#### Testing Data

Conjuntos públicos de referencia por tarea. Entre los usados: Indian Pines, Pavia University, HanChuan, HongHu, Houston y Yellow River Delta (ZY) para clasificación; Xiongan para escalabilidad; Mosaic, AVIRIS y Renourban (detección de objetivos); Pavia, Cri y Viareggio (detección de anomalías); Hermiston, Farmland, Bay Area y Santa Barbara (detección de cambios); Urban (desmezclado); WDC Mall (eliminación de ruido); Houston a escala 8× (superresolución); YRE\_2 (clasificación colaborativa); OSCD (cambios multiespectrales, Sentinel-2) y un conjunto de derrames de petróleo en el Golfo de México. El repositorio resume el trabajo como 16 conjuntos de datos y 7 tareas, mientras que la versión publicada del artículo menciona 20 conjuntos.

#### Factors

Tarea, escena/conjunto de datos y tamaño de backbone (Base, Large, Huge). Además, pruebas de robustez (pocas muestras de entrenamiento, ataques adversariales FGSM y PGD, degradación por compresión JPEG y ruido gaussiano), transferencia entre modalidades y eficiencia computacional (tiempo de inferencia y memoria de GPU).

#### Metrics

* Clasificación: exactitud global (OA), exactitud promedio (AA) y Kappa.
* Detección de objetivos y anomalías: área bajo la curva ROC (AUC).
* Detección de cambios: F1 (y OA, Kappa, precisión y recall en OSCD).
* Desmezclado: MSE (abundancias) y mSAD (endmembers); menor es mejor.
* Eliminación de ruido: PSNR, SSIM y SAM.
* Superresolución: PSNR, SSIM, SAM, CC, RMSE y ERGAS.

### Results

Valores reportados por los autores para HyperSIGMA (la comparación completa con métodos de referencia está en el artículo):

|Tarea|Conjunto|Métrica|HyperSIGMA|
|-|-|-|-|
|Clasificación (ViT-H + ViT-H)|Indian Pines|OA|87,70 %|
|Clasificación (ViT-H + ViT-H)|Xiongan|OA|95,78 %|
|Detección de objetivos|Mosaic / AVIRIS / Renourban|AUC|99,56 / 99,83 / 99,86|
|Detección de anomalías|Pavia / Cri / Viareggio|AUC|99,96 / 99,32 / 98,32|
|Detección de cambios|Hermiston / Farmland / BA / SB|F1|91,74 / 95,49 / 98,93 / 99,04|
|Desmezclado|Urban|MSE abund. / mSAD endm.|0,0157 / 0,0584|
|Eliminación de ruido|WDC Mall (ruido mixto)|PSNR / SSIM / SAM|28,503 / 0,961 / 0,106|
|Cambios multiespectrales|OSCD|F1|59,28 %|
|Robustez adversarial|Indian Pines (limpio / FGSM / PGD)|OA|96,76 / 96,62 / 95,92 %|

Otros resultados destacados del artículo: con solo 10 muestras por clase en HanChuan alcanza 79,10 % de exactitud (unos 8 puntos por encima del segundo mejor método), y en la superresolución 8× sobre Houston supera a MSDFormer en todas las métricas. Los backbones más grandes mejoran el rendimiento, lo que evidencia escalabilidad.

#### Summary

Según los autores, HyperSIGMA supera a los métodos de referencia, incluido SpectralGPT, en la mayoría de las tareas y conjuntos evaluados, y muestra buena escalabilidad, robustez y transferencia a datos multiespectrales. Son resultados auto-reportados; se recomienda validación independiente.

## Model Examination 

El artículo incluye ablaciones (número de puntos de muestreo de SSA, efecto del preentrenamiento sobre HyperGlobal-450K, proporción de enmascarado espectral) y visualizaciones cualitativas por tarea en el apéndice. No incluye un análisis formal de interpretabilidad.

## Environmental Impact

Las emisiones de carbono pueden estimarse con la calculadora [Machine Learning Impact](https://mlco2.github.io/impact#compute) presentada en [Lacoste et al. (2019)](https://arxiv.org/abs/1910.09700).

* **Hardware Type:** GPU NVIDIA V100 (según el artículo). El repositorio muestra comandos de preentrenamiento con SLURM en un clúster con aceleradores DCU (4 por nodo, hasta 128 tareas).
* **Hours used:** No reportado en el texto principal (ver apéndice, Sec. C)
* **Cloud Provider:** No aplica (infraestructura de supercomputación académica e industrial)
* **Compute Region:** China
* **Carbon Emitted:** No reportado

## Technical Specifications 

### Model Architecture and Objective

Vision Transformer de doble rama (SpatViT y SpecViT) con atención de muestreo disperso (SSA) y fusión espacial-espectral mediante SEM, más cabezales específicos para cada tarea. Objetivo de preentrenamiento: reconstrucción de regiones enmascaradas (MAE) sobre HyperGlobal-450K. La complejidad de SSA es comparable a la de la atención por ventanas, y los autores reportan tiempo de inferencia y memoria similares o menores a ViT-B+RVSA y SpectralGPT.

### Compute Infrastructure

#### Hardware

GPU NVIDIA V100 (según el artículo). Preentrenamiento apoyado por Dawning Information Industry Co., Ltd. y ajuste fino en el Supercomputing Center of Wuhan University.

#### Software

Python y PyTorch. El proyecto se basa en MMCV, MAE, Swin Transformer, VSA, RVSA y DAT, entre otros. Los requisitos exactos están en el README de cada carpeta de tarea del repositorio oficial.

## Citation 

**BibTeX:**

```bibtex
@ARTICLE{hypersigma,
  author={Wang, Di and Hu, Meiqi and Jin, Yao and Miao, Yuchun and Yang, Jiaqi and Xu, Yichu and Qin, Xiaolei and Ma, Jiaqi and Sun, Lingyu and Li, Chenxing and Fu, Chuan and Chen, Hongruixuan and Han, Chengxi and Yokoya, Naoto and Zhang, Jing and Xu, Minqiang and Liu, Lin and Zhang, Lefei and Wu, Chen and Du, Bo and Tao, Dacheng and Zhang, Liangpei},
  journal={IEEE Transactions on Pattern Analysis and Machine Intelligence},
  title={HyperSIGMA: Hyperspectral Intelligence Comprehension Foundation Model},
  year={2025},
  volume={47},
  number={8},
  pages={6427-6444},
  doi={10.1109/TPAMI.2025.3557581}
}
```

**APA:**

Wang, D., Hu, M., Jin, Y., Miao, Y., Yang, J., Xu, Y., Qin, X., Ma, J., Sun, L., Li, C., Fu, C., Chen, H., Han, C., Yokoya, N., Zhang, J., Xu, M., Liu, L., Zhang, L., Wu, C., Du, B., Tao, D., \& Zhang, L. (2025). HyperSIGMA: Hyperspectral intelligence comprehension foundation model. *IEEE Transactions on Pattern Analysis and Machine Intelligence, 47*(8), 6427-6444. https://doi.org/10.1109/TPAMI.2025.3557581

Formato de la ficha: Mitchell, M., Wu, S., Zaldivar, A., Barnes, P., Vasserman, L., Hutchinson, B., Spitzer, E., Raji, I. D., \& Gebru, T. (2019). Model cards for model reporting. *Proceedings of the Conference on Fairness, Accountability, and Transparency*, 220-229. https://arxiv.org/abs/1810.03993

## Glossary 

* **HSI (imagen hiperespectral):** imagen con cientos de bandas espectrales contiguas por píxel.
* **Modelo fundacional:** modelo de gran escala preentrenado con grandes volúmenes de datos y adaptable a múltiples tareas.
* **MAE (Masked Autoencoder):** técnica de preentrenamiento que reconstruye regiones enmascaradas de la entrada.
* **SSA (Sparse Sampling Attention):** atención que aprende posiciones de muestreo de claves y valores para cada consulta, reduciendo la redundancia.
* **SEM (Spectral Enhancement Module):** módulo que calibra las características espaciales con información espectral.
* **SpatSIGMA / SpatViT / SpecViT:** subred espacial (modelo por sí solo), y backbones ViT espacial y espectral.
* **HIC / HTD / HAD / HCD / HIU / HID / HSR:** clasificación, detección de objetivos, detección de anomalías, detección de cambios, desmezclado, eliminación de ruido y superresolución hiperespectrales.
* **OA / AA / Kappa / AUC / F1 / PSNR / SSIM / SAM:** métricas de evaluación usadas en las tareas.
* **EO-1 / GF-5:** satélites de observación de la Tierra que aportan las imágenes de HyperGlobal-450K.

## More Information 

* Contacto de los autores originales (según el repositorio oficial): Di Wang (d\_wang@whu.edu.cn) y Chengxi Han (chengxihan@whu.edu.cn).
* Nota sobre cifras del conjunto de datos: el artículo habla de unas 450 mil imágenes (447.072 parches de 64×64), mientras que el repositorio describe 1.701 imágenes de origen recortadas en parches. Ambas cifras se refieren al mismo conjunto, contado en unidades distintas.

## Model Card Authors

Gabriela Zea

## Model Card Contact

gabrielazea2018@gmail.com

