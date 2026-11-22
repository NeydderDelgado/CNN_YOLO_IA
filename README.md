# Clasificación y detección de cáncer de mama mediante CNN y YOLO-cls

Este proyecto académico implementa, compara y evalúa dos enfoques de aprendizaje profundo para la clasificación binaria de parches histopatológicos asociados con el Carcinoma Ductal Invasivo (IDC):

- Una red neuronal convolucional personalizada construida con TensorFlow/Keras.
- Un clasificador ultraligero basado en YOLO-cls.

El conjunto de datos utilizado es *Breast Histopathology Images*, disponible en Kaggle. Contiene parches histopatológicos etiquetados según la presencia o ausencia de IDC.
Al ser bastante pesada la base de datos subo en este espacio el link a drive de donde se encuentra el .zip de la base de datos : https://drive.google.com/file/d/1H_TNhX5i_2ky6SR5KNTohO4ArWMNap0-/view?usp=sharing


> Proyecto con fines exclusivamente académicos y de investigación experimental. No constituye un sistema de diagnóstico médico.

## Tabla de contenidos

- [Estructura del repositorio](#estructura-del-repositorio)
- [Dataset](#dataset)
- [Integridad y partición de los datos](#integridad-y-partición-de-los-datos)
- [Integración entre componentes](#integración-entre-componentes)
- [Modelos e hiperparámetros](#modelos-e-hiperparámetros)
- [Entorno utilizado](#entorno-utilizado)
- [Dependencias](#dependencias)
- [Ejecución](#ejecución)
- [Resultados experimentales](#resultados-experimentales)
- [Consideraciones](#consideraciones)
- [Aviso médico](#aviso-médico)

## Estructura del repositorio

```text
CNN_YOLO_IA/
└── cancer_de_mama/
    ├── CNN_Breast_Histopathology_Colab.ipynb
    ├── Comparacion_CNN_YOLO_IDC_Colab_original.ipynb
    └── cnn_yolo_idc_results/
        ├── metrics_runs.csv
        ├── cm_cnn_seed_7.csv
        ├── cm_cnn_seed_42.csv
        ├── cm_cnn_seed_99.csv
        ├── cm_cnn_seed_123.csv
        ├── cm_cnn_seed_2025.csv
        ├── cm_yolo_seed_7.csv
        ├── cm_yolo_seed_42.csv
        ├── cm_yolo_seed_99.csv
        ├── cm_yolo_seed_123.csv
        ├── cm_yolo_seed_2025.csv
        ├── learning_curves/
        ├── manifests/
        │   ├── all_with_folds.csv
        │   ├── pixel_hashes.csv
        │   ├── train.csv
        │   ├── val.csv
        │   └── test.csv
        └── yolo_runs/
            ├── seed_7/
            ├── seed_42/
            ├── seed_99/
            ├── seed_123/
            └── seed_2025/
```

## Descripción de los archivos

### `CNN_Breast_Histopathology_Colab.ipynb`

Notebook introductorio que implementa el flujo completo de una CNN con TensorFlow/Keras:

- Montaje de Google Drive.
- Carga y exploración del dataset.
- División estratificada del conjunto de datos.
- Redimensionamiento y normalización de imágenes.
- Aumentación de datos.
- Construcción, entrenamiento y evaluación de la CNN.
- Cálculo de accuracy, precision, recall y F1-score.
- Generación de matriz de confusión y curvas de aprendizaje.

### `Comparacion_CNN_YOLO_IDC_Colab_original.ipynb`

Notebook principal del experimento comparativo. Ejecuta cinco corridas de CNN y cinco corridas de YOLO-cls utilizando las semillas `7`, `42`, `99`, `123` y `2025`.

Incluye:

- Eliminación de imágenes duplicadas mediante hashes de píxeles.
- Partición agrupada y estratificada por paciente o muestra.
- Entrenamiento de TensorFlow/Keras.
- Entrenamiento de YOLO-cls con Ultralytics.
- Registro de métricas, épocas, tiempos y latencia.
- Comparación estadística pareada.
- Matrices de confusión por modelo y semilla.

### `cnn_yolo_idc_results/metrics_runs.csv`

Archivo consolidado que reúne las métricas obtenidas por las diferentes corridas. Puede incluir modelo, semilla, época final, tiempos de entrenamiento e inferencia, accuracy, precision, recall, F1-score y latencia.

### `cnn_yolo_idc_results/cm_cnn_seed_*.csv`

Matrices de confusión generadas por las ejecuciones de la CNN. El sufijo identifica la semilla utilizada.

### `cnn_yolo_idc_results/cm_yolo_seed_*.csv`

Matrices de confusión generadas por las ejecuciones de YOLO-cls. El sufijo identifica la semilla utilizada.

### `cnn_yolo_idc_results/manifests/`

Contiene los archivos utilizados para auditar y reproducir la partición:

- `train.csv`: imágenes asignadas al entrenamiento.
- `val.csv`: imágenes asignadas a la validación.
- `test.csv`: imágenes reservadas para la prueba final.
- `all_with_folds.csv`: partición completa con el pliegue asignado a cada parche.
- `pixel_hashes.csv`: huellas SHA-256 utilizadas para encontrar duplicados exactos.

### `cnn_yolo_idc_results/learning_curves/`

Curvas de aprendizaje generadas durante las ejecuciones de YOLO-cls. Permiten analizar la evolución del entrenamiento y posibles señales de sobreajuste.

### `cnn_yolo_idc_results/yolo_runs/`

Registros y artefactos producidos por las ejecuciones de YOLO. Ultralytics puede crear ejecuciones adicionales con nombres como `seed_42-2`; esto ocurre cuando varias ejecuciones usan el mismo nombre de proyecto.

## Dataset

El conjunto **Breast Histopathology Images** contiene parches histopatológicos de 50 × 50 píxeles extraídos de imágenes mamarias.

Las clases son:

- `0`: IDC negativo, tejido sin presencia de carcinoma ductal invasivo.
- `1`: IDC positivo, tejido con presencia de carcinoma ductal invasivo.

La distribución publicada del dataset contiene aproximadamente:

- 198.738 parches negativos, alrededor del 71,6%.
- 78.786 parches positivos, alrededor del 28,4%.

El repositorio no incluye las imágenes originales ni el archivo ZIP del dataset.

## Integridad y partición de los datos

Para reducir el riesgo de fuga de datos, el experimento utiliza identificadores agrupados derivados del nombre de las imágenes. Los parches que pertenecen a la misma muestra o biopsia deben permanecer en un único conjunto:

- Entrenamiento.
- Validación.
- Prueba.

La partición agrupada evita que parches prácticamente iguales provenientes de la misma muestra aparezcan simultáneamente en el entrenamiento y en la evaluación.

Además, se calculó una huella de los píxeles para identificar imágenes duplicadas exactas. Los hashes permiten verificar la integridad de los datos y evitar que los mismos píxeles influyan en múltiples particiones.

Las particiones creadas por el notebook se conservan en:

```text
cancer_de_mama/cnn_yolo_idc_results/manifests/
```

## Integración entre componentes

El flujo experimental está organizado de la siguiente manera:

1. Las imágenes se indexan y verifican.
2. Se eliminan duplicados exactos.
3. Se crea una partición agrupada compartida.
4. Los manifiestos alimentan las canalizaciones de TensorFlow/Keras y YOLO.
5. Las ejecuciones utilizan semillas fijas para evaluar la variabilidad.
6. Las matrices de confusión y métricas se consolidan en `metrics_runs.csv`.

Ambos modelos reciben la misma partición base. Solo cambia la semilla de cada ejecución para analizar la estabilidad del rendimiento.

## Modelos e hiperparámetros

### CNN personalizada con TensorFlow/Keras

- Resolución de entrada: 50 × 50 × 3 en el notebook básico.
- Resolución usada en la comparación: 64 × 64 × 3.
- Tres bloques convolucionales con 32, 64 y 128 filtros.
- Kernel convolucional de 3 × 3.
- Activación ReLU.
- Batch normalization y max pooling.
- Capa densa de 128 neuronas.
- Dropout de 0.5.
- Capa de salida sigmoide para clasificación binaria.
- Optimizador Adam con tasa de aprendizaje de 0.001.
- Función de pérdida binary crossentropy.
- Hasta 20 épocas.
- Early stopping con paciencia de 4 épocas.
- Reducción automática de la tasa de aprendizaje cuando la pérdida de validación se estanca.

Aumentación utilizada:

- Giro horizontal aleatorio.
- Rotación moderada.
- Zoom aleatorio.

### YOLO-cls

- Arquitectura: `yolo26n-cls`.
- Resolución de entrada: 64 × 64.
- Tamaño de lote: 64.
- Optimizador: Adam.
- Tasa de aprendizaje inicial: 0.001.
- Hasta 20 épocas.
- Paciencia de 4 épocas.
- Pesos iniciales no preentrenados para mantener una condición comparable con la CNN.

## Entorno utilizado

Los experimentos se ejecutaron principalmente en Google Colab:

- Google Colaboratory.
- Python 3.13.16.
- TensorFlow 2.21.0.
- Ultralytics 8.4.216.
- PyTorch 2.14.1+cu130.
- CUDA 13.0.
- NVIDIA Tesla T4 cuando la GPU estuvo disponible.

Las ejecuciones registradas muestran que parte de YOLO-cls se ejecutó en GPU y otra parte en CPU. Por este motivo, los tiempos de entrenamiento e inferencia deben compararse con atención a la plataforma utilizada en cada corrida.

## Dependencias

Dependencias principales:

```text
numpy
pandas
matplotlib
seaborn
pillow
scikit-learn
scipy
tqdm
tensorflow==2.21.0
torch==2.14.1+cu130
ultralytics==8.4.216
```

Instalación general:

```bash
pip install numpy pandas matplotlib seaborn pillow scikit-learn scipy tqdm
pip install tensorflow==2.21.0
pip install torch==2.14.1+cu130
pip install ultralytics==8.4.216
```

## Ejecución

### 1. Abrir Google Colab

Abre <https://colab.research.google.com/> y selecciona uno de los notebooks ubicados en:

```text
cancer_de_mama/
```

### 2. Seleccionar GPU

Usa:

```text
Entorno de ejecución → Cambiar tipo de entorno de ejecución → GPU
```

La ejecución en GPU no es obligatoria, pero recomienda tiempos menores.

### 3. Preparar el dataset

Descarga el archivo ZIP de *Breast Histopathology Images* desde Kaggle y colócalo en Google Drive. Después, descomprímelo o configura `DATA_DIR` con la ruta que contiene los directorios del dataset.

Ejemplo para el notebook básico:

```python
DATA_DIR = "/content/drive/MyDrive/breast-histopathology-images"
```

Ejemplo para el notebook comparativo:

```python
DATA_DIR = "/content/base_datos_extracted"

OUTPUT_DIR = Path("/content/drive/MyDrive/cnn_yolo_idc_results")
```

No publiques en el repositorio el token o las credenciales de Kaggle.

### 4. Ejecutar el notebook básico

Abre:

```text
CNN_Breast_Histopathology_Colab.ipynb
```

Ejecuta:

```text
Entorno de ejecución → Ejecutar todas
```

El notebook construye la CNN, la entrena y genera métricas y visualizaciones.

### 5. Ejecutar la comparación

Abre:

```text
Comparacion_CNN_YOLO_IDC_Colab_original.ipynb
```

Instala Ultralytics, monta Google Drive, descomprime el dataset y ejecuta las celdas en orden.

Si el archivo `metrics_runs.csv` ya contiene una semilla determinada, el notebook omite automáticamente esa corrida y continúa con las pendientes.

### 6. Entrenamiento YOLO independiente

Ejemplo de configuración:

```bash
yolo classify train \
  model=yolo26n-cls.yaml \
  data=/content/idc_yolo_cls \
  epochs=20 \
  batch=64 \
  imgsz=64 \
  optimizer=Adam \
  lr0=0.001 \
  patience=4 \
  seed=42 \
  project=/content/drive/MyDrive/cnn_yolo_idc_results/yolo_runs \
  name=seed_42
```

Los pesos y registros se pueden guardar dentro de:

```text
cnn_yolo_idc_results/yolo_runs/
```

## Resultados experimentales

Los resultados consolidados se encuentran en:

```text
cancer_de_mama/cnn_yolo_idc_results/metrics_runs.csv
```

Las matrices de confusión individuales permiten revisar específicamente los verdaderos positivos, verdaderos negativos, falsos positivos y falsos negativos.

Debido al desequilibrio entre clases, las métricas de la clase IDC positivo, especialmente recall y F1-score, son especialmente importantes para interpretar los modelos. Accuracy por sí sola puede ocultar un rendimiento deficiente sobre la clase minoritaria.

Los experimentos utilizan cinco semillas. Una prueba estadística pareada compara las métricas correspondientes a las mismas semillas. Con cinco pares, la potencia estadística es limitada y los resultados deben interpretarse junto con los intervalos y la variación observada.

## Consideraciones

- Las latencias pueden variar según CPU, GPU, sesión de Colab y carga del entorno.
- Las corridas registradas no siempre se ejecutaron bajo el mismo hardware.
- Las métricas individuales deben compararse únicamente con su conjunto de datos y partición correspondientes.
- El dataset desbalanceado puede requerir técnicas adicionales para optimizar recall sobre IDC positivo.
- Los archivos generados pueden ser recreados ejecutando los notebooks.
- Los modelos no están preparados para uso clínico directo.

## Aviso médico

Este proyecto fue desarrollado exclusivamente con fines académicos y de investigación experimental.

Ninguno de los modelos, métricas o predicciones contenidos en este repositorio constituye un sistema de diagnóstico médico o un sustituto de la evaluación realizada por profesionales sanitarios cualificados.
