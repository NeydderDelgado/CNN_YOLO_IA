# CNN_YOLO_IA

Proyecto académico que compara una red neuronal convolucional (CNN) con YOLO-cls para clasificar parches histopatológicos de carcinoma ductal invasivo (IDC) en imágenes de cáncer de mama.

Los experimentos se ejecutaron principalmente en Google Colaboratory.

## Entorno utilizado

- Google Colaboratory.
- Python 3.13.16.
- TensorFlow 2.21.0.
- Ultralytics 8.4.216.
- PyTorch 2.14.1+cu130.
- CUDA 13.0.
- GPU utilizada cuando estuvo disponible: NVIDIA Tesla T4.
- Algunas ejecuciones de YOLO se realizaron en CPU.

Los registros del proyecto muestran corridas YOLO ejecutadas tanto con GPU como con CPU. Esta diferencia debe tenerse en cuenta al comparar los tiempos de entrenamiento e inferencia.

## Contenido del repositorio

```text
CNN_YOLO_IA/
└── cancer_de_mama/
    ├── CNN_Breast_Histopathology_Colab.ipynb
    ├── Comparacion_CNN_YOLO_IDC_Colab_original.ipynb
    └── cnn_yolo_idc_results/
        ├── cm_cnn_seed_*.csv
        ├── cm_yolo_seed_*.csv
        ├── metrics_runs.csv
        ├── learning_curves/
        ├── manifests/
        └── yolo_runs/
```

## Descripción de los archivos

### `CNN_Breast_Histopathology_Colab.ipynb`

Notebook básico que implementa una CNN con TensorFlow/Keras para clasificar parches histopatológicos.

Incluye:

- Conexión con Google Drive.
- Carga y exploración del dataset.
- División estratificada de los datos.
- Normalización y aumentación de imágenes.
- Construcción y entrenamiento de la CNN.
- Evaluación con accuracy, precision, recall y F1-score.
- Matriz de confusión.
- Ejemplos de predicciones.

El notebook guarda el modelo entrenado y sus métricas dentro del entorno de Colab.

### `Comparacion_CNN_YOLO_IDC_Colab_original.ipynb`

Notebook principal utilizado para comparar una CNN con YOLO-cls en cinco ejecuciones independientes.

Incluye:

- Instalación de Ultralytics.
- Identificación y eliminación de imágenes duplicadas.
- Creación de una partición agrupada compartida.
- Entrenamiento de cinco CNN.
- Entrenamiento de cinco modelos YOLO-cls.
- Métricas de clasificación.
- Matrices de confusión.
- Tiempos de entrenamiento e inferencia.
- Prueba estadística pareada entre modelos.

Las semillas utilizadas fueron `42`, `123`, `2025`, `7` y `99`.

### `cnn_yolo_idc_results/metrics_runs.csv`

Contiene el conjunto consolidado de las métricas obtenidas en las ejecuciones.

Incluye:

- Tipo de modelo.
- Semilla utilizada.
- Época final.
- Tiempo de entrenamiento.
- Tiempo de inferencia.
- Accuracy.
- Precision.
- Recall.
- F1-score.
- Métricas relacionadas con el costo y la latencia.

### `cnn_yolo_idc_results/cm_cnn_seed_*.csv`

Matrices de confusión generadas por las ejecuciones de la CNN.

Cada nombre incluye la semilla utilizada durante el entrenamiento.

### `cnn_yolo_idc_results/cm_yolo_seed_*.csv`

Matrices de confusión generadas por las ejecuciones de YOLO-cls.

### `cnn_yolo_idc_results/manifests/`

Contiene los archivos utilizados para reproducir y auditar las particiones:

- `train.csv`: conjunto de entrenamiento.
- `val.csv`: conjunto de validación.
- `test.csv`: conjunto reservado para la prueba final.
- `all_with_folds.csv`: asignación de las imágenes a los pliegues.
- `pixel_hashes.csv`: hashes SHA-256 utilizados para identificar imágenes duplicadas.

### `cnn_yolo_idc_results/learning_curves/`

Imágenes con las curvas de aprendizaje de las ejecuciones YOLO.

Permiten inspeccionar visualmente la evolución del modelo y posibles señales de sobreajuste.

### `cnn_yolo_idc_results/yolo_runs/`

Contiene los resultados y artefactos generados por las ejecuciones YOLO.

Las carpetas están organizadas por semilla, por ejemplo:

```text
seed_42/
seed_123/
seed_2025/
seed_7/
seed_99/
```

Algunas carpetas poseen sufijos como `seed_42-2`, `seed_42-3`, etc., debido a ejecuciones o entrenamientos repetidos por Ultralytics.

## Dataset

El proyecto utiliza el conjunto **Breast Histopathology Images** obtenido de Kaggle.

No se incluyen las imágenes originales dentro del repositorio.

Las imágenes representan parches histopatológicos:

- Clase `0`: IDC negativo.
- Clase `1`: IDC positivo.

El nombre de cada archivo permite obtener la clase:

```text
10253_idx5_x1_y1_class0.png
```

## Ejecución en Google Colab

1. Abre Google Colab:
   <https://colab.research.google.com/>.

2. Sube el notebook deseado desde `cancer_de_mama/`.

3. Descarga el dataset de Kaggle.

4. Sube el archivo ZIP a Google Drive, por ejemplo:

   ```text
   /MyDrive/cancer_de_mama/base_datos.zip
   ```

5. Cambia el tipo de entorno:

   ```text
   Entorno de ejecución → Cambiar tipo de entorno de ejecución → GPU
   ```

6. Ajusta las rutas utilizadas por el notebook.

### CNN básica

```python
DATA_DIR = "/content/drive/MyDrive/breast-histopathology-images"
```

### Comparación CNN-YOLO

```python
DATA_DIR = "/content/base_datos_extracted"

OUTPUT_DIR = Path(
    "/content/drive/MyDrive/cnn_yolo_idc_results"
)
```

7. Ejecuta todas las celdas:

   ```text
   Entorno de ejecución → Ejecutar todas
   ```

## Dependencias

```text
tensorflow==2.21.0
ultralytics==8.4.216
torch==2.14.1+cu130
numpy
pandas
matplotlib
seaborn
pillow
scikit-learn
scipy
tqdm
```

Instalación general:

```bash
pip install tensorflow==2.21.0 ultralytics==8.4.216 numpy pandas matplotlib seaborn pillow scikit-learn scipy tqdm
```

Para YOLO también se utiliza:

```bash
pip install ultralytics
```

## Conservar resultados en Google Drive

La comparación utiliza una carpeta de salida persistente:

```python
OUTPUT_DIR = Path(
    "/content/drive/MyDrive/cnn_yolo_idc_results"
)
```

Dentro se almacenan:

- Métricas consolidadas.
- Matrices de confusión.
- Particiones.
- Hashes de imágenes.
- Curvas de aprendizaje.
- Pesos y registros de YOLO.

## Consideraciones al interpretar los tiempos

Las corridas de YOLO no fueron realizadas siempre bajo el mismo hardware:

- Algunas se ejecutaron con GPU Tesla T4.
- Otras se ejecutaron con CPU.
- Por ello, los tiempos de entrenamiento e inferencia no siempre representan una comparación estrictamente homogénea.

Las diferencias pueden deberse al hardware disponible, a cambios en la versión de las bibliotecas o a reinicios de las sesiones de Colab.

La partición de datos sí fue compartida entre las comparaciones correspondientes.

## Alcance del proyecto

El proyecto tiene carácter académico y experimental.

Los resultados no deben utilizarse como herramienta de diagnóstico médico sin una validación clínica independiente. No se deben publicar tokens de Kaggle, credenciales ni archivos privados dentro del repositorio.
