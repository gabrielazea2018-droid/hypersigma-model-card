# Model Card for HyperSIGMA

<!-- Basado en la plantilla de Hugging Face y en Mitchell et al. (2019) "Model Cards for Model Reporting" -->

## Model Details

### Descripción del Modelo

HyperSIGMA es el primer modelo fundacional (foundation model) a escala de mil millones de parámetros diseñado específicamente para la interpretación de imágenes hiperspectrales (HSI). Utiliza una arquitectura basada en Vision Transformer (ViT) e introduce un novedoso mecanismo de atención de muestreo disperso (Sparse Sampling Attention, SSA) que promueve eficazmente el aprendizaje de diversas características contextuales, sirviendo como bloque básico del modelo. HyperSIGMA integra características espaciales y espectrales mediante un módulo de mejora espectral especialmente diseñado. Fue pre-entrenado con HyperGlobal-450K, un conjunto de datos hiperspectrales a gran escala que contiene aproximadamente 450,000 imágenes hiperspectrales (más de 20 millones de imágenes de tres bandas) recolectadas de sensores EO-1 y GF-5B alrededor del mundo. El modelo es escalable a más de mil millones de parámetros.

- **Desarrollado por:** Di Wang, Meiqi Hu, Yao Jin, Yuchun Miao, Jiaqi Yang, Yichu Xu, Xiaolei Qin, Jiaqi Ma, Lingyu Sun, Chenxing Li, Chuan Fu, Hongruixuan Chen, Chengxi Han, Naoto Yokoya, Jing Zhang, Minqiang Xu, Lin Liu, Lefei Zhang, Chen Wu, Bo Du, Dacheng Tao, Liangpei Zhang
- **Tipo de modelo:** Vision Transformer (ViT) - Modelo Fundacional
- **Lenguaje(s):** No aplica (modelo de visión por computador)
- **Licencia:** Apache 2.0 (ver repositorio oficial)
- **Modelo base:** No aplica (pre-entrenamiento desde cero con HyperGlobal-450K)
- **Instituciones:** Wuhan University, University of Technology Sydney, RIKEN Center for Advanced Intelligence Project, entre otras

### Fuentes del Modelo

- **Repositorio:** [https://github.com/WHU-Sigma/HyperSIGMA](https://github.com/WHU-Sigma/HyperSIGMA)
- **Paper:** [https://arxiv.org/abs/2406.11519](https://arxiv.org/abs/2406.11519)
- **Sitio web del proyecto:** [https://whu-sigma.github.io/HyperSIGMA](https://whu-sigma.github.io/HyperSIGMA)

## Uses

### Uso Directo

HyperSIGMA puede utilizarse directamente para la interpretación de imágenes hiperspectrales sin necesidad de ajuste fino (fine-tuning) adicional. Las tareas soportadas incluyen:

- **Tareas de alto nivel:**
  - Clasificación de imágenes hiperspectrales (image classification)
  - Detección de objetivos (target detection)
  - Detección de anomalías (anomaly detection)
  - Detección de cambios (change detection)

- **Tareas de bajo nivel:**
  - Unmixing espectral (spectral unmixing)
  - Eliminación de ruido (denoising)
  - Super-resolución (super-resolution)

### Uso Downstream

HyperSIGMA puede ser ajustado (fine-tuned) para aplicaciones específicas en:
- Observación de la Tierra y monitoreo ambiental
- Agricultura de precisión
- Planificación urbana
- Cartografía catastral y geomática
- Detección de cambios de cobertura terrestre

### Usos Fuera de Alcance (Out-of-Scope)

- Uso con imágenes RGB naturales (el modelo está diseñado específicamente para datos hiperspectrales)
- Aplicaciones en tiempo real sin hardware adecuado (el modelo requiere GPUs de alta capacidad)
- Uso con datos de sensores no hiperspectrales sin adaptación previa

## Bias, Risks, and Limitations

### Limitaciones Técnicas

- **Requisitos computacionales:** Al ser un modelo a escala de mil millones de parámetros, requiere GPUs de alta memoria (se recomienda al menos 80GB de VRAM para el modelo más grande).
- **Dependencia del sensor:** El rendimiento puede variar significativamente dependiendo del sensor utilizado para capturar las imágenes hiperspectrales (EO-1, GF-5B, etc.).
- **Banda espectral:** El modelo fue entrenado con rangos específicos de bandas espectrales; sensores con configuraciones muy diferentes pueden requerir re-entrenamiento.
- **Tamaño de entrada:** Las imágenes deben ser redimensionadas o divididas en parches (patches) para su procesamiento.

### Limitaciones Sociotécnicas

- **Datos de entrenamiento sesgados:** El dataset HyperGlobal-450K está compuesto principalmente por imágenes de sensores EO-1 y GF-5B, lo que puede introducir sesgos hacia ciertas regiones geográficas o tipos de cobertura terrestre.
- **Aplicaciones sensibles:** El modelo no debe utilizarse como única fuente para decisiones críticas sin validación humana (por ejemplo, en aplicaciones legales o de seguridad).

### Recomendaciones

- Los usuarios deben validar el rendimiento del modelo con datos representativos de su región de interés antes de su implementación.
- Se recomienda realizar ajuste fino (fine-tuning) con datos locales para obtener mejores resultados en aplicaciones específicas.
- El uso del modelo debe ir acompañado de un análisis de incertidumbre y validación por expertos.

## How to Get Started with the Model

```python
# Ejemplo básico de uso (ver repositorio oficial para código completo)
# from hyperSIGMA import HyperSIGMA

# Cargar el modelo pre-entrenado
# model = HyperSIGMA.from_pretrained("WHU-Sigma/HyperSIGMA")

# Para más información, visitar:
# https://github.com/WHU-Sigma/HyperSIGMA
```

Para instrucciones detalladas de instalación y uso, consultar el repositorio oficial: [https://github.com/WHU-Sigma/HyperSIGMA](https://github.com/WHU-Sigma/HyperSIGMA)

## Training Details

### Datos de Entrenamiento

- **Dataset:** HyperGlobal-450K
- **Descripción:** Conjunto de datos hiperspectrales a gran escala recolectado de imágenes de teledetección alrededor del mundo
- **Composición:** 1,701 imágenes (1,486 de EO-1 y 215 de GF-5B) con cientos de bandas espectrales
- **Tamaño:** ~450,000 imágenes hiperspectrales (más de 20 millones de imágenes de tres bandas)
- **Pre-procesamiento:** Las imágenes son divididas en parches (patches) de tres bandas para el pre-entrenamiento

### Procedimiento de Entrenamiento

#### Preprocesamiento

Las imágenes hiperspectrales originales son procesadas para generar parches de tres bandas espectrales, que sirven como entrada al modelo. Este proceso permite manejar la alta dimensionalidad de los datos hiperspectrales.

#### Hiperparámetros de Entrenamiento

- **Arquitectura:** Vision Transformer con Sparse Sampling Attention (SSA)
- **Escalabilidad:** El modelo es escalable a más de 1B de parámetros
- **Optimizador:** AdamW (ver paper para detalles completos)
- **Learning rate:** Ver documentación del repositorio oficial
- **Batch size:** Ver documentación del repositorio oficial
- **Entrenamiento distribuido:** Utiliza múltiples GPUs para el pre-entrenamiento

#### Tamaños, Tiempos y Velocidades

- **Tamaño del modelo:** Múltiples variantes disponibles (escalable hasta 1B+ parámetros)
- **Tiempo de entrenamiento:** Ver paper para detalles completos
- **Checkpoint size:** Ver repositorio oficial

## Evaluation

### Datos de Prueba, Factores y Métricas

#### Datos de Prueba

HyperSIGMA fue evaluado en múltiples benchmarks hiperspectrales estándar para cada tarea soportada (ver paper para detalles de datasets específicos).

#### Factores

- Tipo de sensor (EO-1, GF-5B, y otros sensores hiperspectrales)
- Tipo de cobertura terrestre
- Resolución espacial y espectral

#### Métricas

- **Clasificación:** Overall Accuracy (OA), Average Accuracy (AA), Kappa coefficient
- **Detección:** F1-score, Precision, Recall
- **Denoising:** PSNR, SSIM
- **Super-resolución:** PSNR, SSIM
- **Unmapping espectral:** RMSE (Root Mean Square Error)

### Resultados

HyperSIGMA demuestra rendimiento superior o competitivo comparado con métodos del estado del arte (SOTA) en todas las tareas evaluadas. Para resultados numéricos detallados, consultar el paper en la Sección V (Evaluation).

**Tareas evaluadas:**
1. Image Classification
2. Target Detection & Anomaly Detection
3. Change Detection
4. Spectral Unmixing
5. Image Denoising
6. Image Super-Resolution

### Summary

HyperSIGMA muestra ventajas significativas en escalabilidad, robustez, capacidad de transferencia cross-modal, aplicabilidad en el mundo real y eficiencia computacional.

## Model Examination

- **Interpretabilidad:** El mecanismo SSA permite visualizar las regiones de la imagen que el modelo considera más relevantes para cada tarea.
- **Transferencia cross-modal:** El modelo demuestra capacidad de transferencia entre diferentes sensores hiperspectrales.
- **Análisis de escalabilidad:** Se realizaron estudios de escalabilidad desde millones hasta más de 1B de parámetros.

## Environmental Impact

Las emisiones de carbono pueden calcularse utilizando el [calculador de impacto de Machine Learning](https://mlco2.github.io/impact#impact) presentado en Lacoste et al. (2019).

- **Tipo de hardware:** GPUs de alta gama (ver paper para detalles)
- **Horas de uso:** Ver paper para detalles completos
- **Proveedor de nube:** No especificado
- **Región de cómputo:** No especificada
- **Carbono emitido:** No cuantificado en el paper

## Technical Specifications

### Arquitectura del Modelo y Objetivo

HyperSIGMA está basado en la arquitectura Vision Transformer (ViT) con las siguientes innovaciones clave:

1. **Sparse Sampling Attention (SSA):** Mecanismo de atención de muestreo disperso que promueve el aprendizaje de características contextuales diversas, sirviendo como bloque básico del modelo.
2. **Spectral Enhancement Module:** Módulo especialmente diseñado para integrar características espaciales y espectrales.
3. **Escalabilidad:** El modelo es escalable a más de mil millones de parámetros, apoyado por el dataset HyperGlobal-450K.

### Infraestructura de Cómputo

#### Hardware

- GPUs de alta capacidad de memoria (mínimo 80GB recomendado para el modelo más grande)
- Entrenamiento distribuido en múltiples nodos (ver paper para detalles)

#### Software

- PyTorch
- CUDA
- Ver repositorio oficial para dependencias específicas

## Citation

**BibTeX:**

```bibtex
@article{wang2025hypersigma,
  title={HyperSIGMA: Hyperspectral Intelligence Comprehension Foundation Model},
  author={Wang, Di and Hu, Meiqi and Jin, Yao and Miao, Yuchun and Yang, Jiaqi and Xu, Yichu and Qin, Xiaolei and Ma, Jiaqi and Sun, Lingyu and Li, Chenxing and Fu, Chuan and Chen, Hongruixuan and Han, Chengxi and Yokoya, Naoto and Zhang, Jing and Xu, Minqiang and Liu, Lin and Zhang, Lefei and Wu, Chen and Du, Bo and Tao, Dacheng and Zhang, Liangpei},
  journal={IEEE Transactions on Pattern Analysis and Machine Intelligence},
  volume={47},
  number={8},
  pages={6427--6444},
  year={2025},
  doi={10.1109/TPAMI.2025.3557581}
}
```

**APA:**

Wang, D., Hu, M., Jin, Y., Miao, Y., Yang, J., Xu, Y., Qin, X., Ma, J., Sun, L., Li, C., Fu, C., Chen, H., Han, C., Yokoya, N., Zhang, J., Xu, M., Liu, L., Zhang, L., Wu, C., Du, B., Tao, D., & Zhang, L. (2025). HyperSIGMA: Hyperspectral Intelligence Comprehension Foundation Model. *IEEE Transactions on Pattern Analysis and Machine Intelligence*, 47(8), 6427–6444.

## Glossary

- **HSI (Hyperspectral Image):** Imagen hiperspectral que contiene información en cientos de bandas espectrales contínuas.
- **SSA (Sparse Sampling Attention):** Mecanismo de atención de muestreo disperso introducido en HyperSIGMA.
- **ViT (Vision Transformer):** Arquitectura de transformer adaptada para tareas de visión por computador.
- **Foundation Model:** Modelo fundacional pre-entrenado a gran escala que puede adaptarse a múltiples tareas.
- **HyperGlobal-450K:** Dataset hiperspectral a gran escala utilizado para el pre-entrenamiento de HyperSIGMA.
- **Spectral Unmixing:** Técnica para descomponer una imagen hiperspectral en sus componentes espectrales puroos (endmembers) y sus proporciones (abundances).

## More Information

- **Repositorio oficial:** [https://github.com/WHU-Sigma/HyperSIGMA](https://github.com/WHU-Sigma/HyperSIGMA)
- **Paper en arXiv:** [https://arxiv.org/abs/2406.11519](https://arxiv.org/abs/2406.11519)
- **Sitio web del proyecto:** [https://whu-sigma.github.io/HyperSIGMA](https://whu-sigma.github.io/HyperSIGMA)
- **Dataset:** HyperGlobal-450K (ver repositorio oficial para acceso)

## Model Card Authors

| Nombre | Correo | Institución | Programa |
|--------|---------|-------------|----------|
| Gabriela Zea Montoya | gabrielazea2018@gmail.com | Universidad Distrital Francisco José de Caldas | Ingeniería Catastral y Geodesia |

## Model Card Contact

- **Nombre:** Gabriela Zea Montoya
- **Email:** gabrielazea2018@gmail.com
- **GitHub:** [@gabrielazea2018-droid](https://github.com/gabrielazea2018-droid)
