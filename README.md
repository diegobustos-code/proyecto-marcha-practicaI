# Análisis de marcha y clasificación de riesgo de caída en adultos mayores

Línea 1, Proyecto 1 · Ingeniería Civil Eléctrica, UNAB

## Notebooks

| Notebook | Sub-etapa | Qué hace |
|---|---|---|
| `notebooks/01_EDA_LabWalks.ipynb` | 1.2 | Carga las caminatas de laboratorio de LTMM (PhysioNet), control de calidad, limpieza, variables de marcha y análisis exploratorio. Genera el dataset limpio. |
| `notebooks/02_EDA_CASIA.ipynb` | 1.2 | Revisa las siluetas de CASIA A, B y C: inventario, completitud, calidad y cadencia estimada desde la imagen. |
| `notebooks/03_Modelos_RF_XGBoost.ipynb` | 1.3 | Entrena y valida Random Forest y XGBoost, y los compara con tres referencias simples (sin modelo, una variable y TUG). |
| `notebooks/04_Modelo_LSTM.ipynb` | 1.3 | Entrena y valida una red CNN + LSTM con las señales, y compara los tres modelos en las mismas particiones. |

El significado de cada columna de los datos está en [`DICCIONARIO_DE_DATOS.md`](DICCIONARIO_DE_DATOS.md).

## Cómo ejecutarlo

1. Instalar las librerías (una vez, en la terminal de Anaconda, dentro de esta carpeta):
```
   pip install -r requirements.txt
```
2. Copiar los datos crudos (no se suben a GitHub):
   - LabWalks: los `.hea` y `.dat` en `datos/crudos/LabWalks/` y `ClinicalDemogData_COFL.xlsx` en `datos/crudos/`. Se descargan con *LabWalks Explorer* o desde <https://physionet.org/content/ltmm/1.0.0/>.
   - CASIA: las siluetas de A, B y C en `datos/crudos/CASIA/`.
3. Abrir Jupyter (`jupyter lab`) y ejecutar los notebooks **en orden (01 → 04)** con *Kernel → Restart Kernel and Run All Cells*. Cada uno usa lo que generó el anterior.

## Estructura

```
notebooks/          notebooks de análisis y modelos
datos/crudos/       datos originales (no se suben)
datos/procesados/   dataset limpio, señales limpias y tablas del EDA
modelos/            modelos entrenados (checkpoints) y su información
resultados/         métricas de validación de los modelos
figuras/            gráficos para los informes
informes/           documentos de avance (PDF) y resultados notebooks
```

## Reproducibilidad

- Semilla fija `SEMILLA = 42` en todos los notebooks.
- Los parámetros de cada etapa están al inicio de cada notebook.
- Cada modelo guardado tiene un archivo `.json` con sus variables de entrada, parámetros, métricas y versiones de las librerías.

## Bitácora

### Semanas 4-5 · Sub-etapa 1.2: obtención y preparación de datos

**Avanzado**
- Descarga de LTMM/LabWalks (73 registros) y de CASIA A, B y C (20, 124 y 153 personas).
- Control de calidad, limpieza (recorte de 3 s, filtro de 20 Hz, eliminación de la gravedad) y 17 variables de marcha por persona.
- EDA: quienes caen muestran menor frecuencia dominante (1,78 vs 1,93 Hz; p Holm = 0,007).
- Cadencia estimada desde siluetas de CASIA: distingue marcha lenta, normal y rápida.

**Decisiones**
- `n_pasos` no se usa como variable, porque depende de la duración del registro.
- Se descartan los intervalos entre pasos fuera de 0,5–1,5 veces la mediana (pausas y giros) antes de calcular el CV.
- Se excluye un registro si se descarta más del 20 % de sus intervalos: se excluyó fl015 (28 %). Muestra final: 72 personas (38 no caen, 34 caen).
- 14 variables no redundantes para los modelos (`labwalks_modelo.csv`).
- CASIA no se usa para entrenar; se reserva para probar la estimación de pose en la Etapa 2.

### Semanas 6-9 · Sub-etapa 1.3: implementación y entrenamiento de modelos

**Avanzado**
- Random Forest y XGBoost con validación cruzada estratificada 5 × 10 (50 pruebas), separando por persona.
- Red CNN + LSTM con ventanas de 5 s de las señales (1150 ventanas), validada en las mismas 15 primeras particiones.
- Referencias: sin modelo, solo frecuencia dominante y solo TUG.
- Modelos guardados en `modelos/` con su información en `.json`.

**Resultados (AUC, mismas 15 pruebas)**
- Solo frecuencia dominante 0,75 · Random Forest 0,68 · XGBoost 0,67 · TUG 0,66 · LSTM 0,55.
- Con 72 personas, el desempeño empeora a medida que aumenta la complejidad del modelo (sobreajuste). La LSTM memoriza a las personas de entrenamiento.

**Decisiones**
- Se usó PyTorch en vez de TensorFlow, que no tiene versión para Python 3.14.
- Se agregaron dos capas convolucionales antes de la LSTM, porque una LSTM sola sobre la señal cruda no aprendía.
- La red entrena al menos 40 épocas antes de aplicar la parada temprana.

**Pendiente (sub-etapa 1.4)**
- Comparación formal de los modelos partición por partición y selección del modelo final.
- Probar modelos con menos variables, eligiéndolas dentro de la validación cruzada.
- Evaluar el uso de los registros de 3 días de LTMM (más marcha por persona).

## Fuentes de datos

**PhysioNet – Long Term Movement Monitoring (LTMM)**
- A. Weiss *et al.*, "Does the evaluation of gait quality during daily life provide insight into fall risk? A novel approach using 3-day accelerometer recordings," *Neurorehabil. Neural Repair*, vol. 27, no. 8, pp. 742–752, 2013, doi: 10.1177/1545968313491004.
- J. M. Hausdorff, "Long Term Movement Monitoring Database" (versión 1.0.0), PhysioNet, 2016, doi: 10.13026/C2S59C.
- A. L. Goldberger *et al.*, "PhysioBank, PhysioToolkit, and PhysioNet," *Circulation*, vol. 101, no. 23, pp. e215–e220, 2000, doi: 10.1161/01.CIR.101.23.e215.

**CASIA Gait Database**
- Base A: L. Wang, T. Tan, H. Ning y W. Hu, "Silhouette analysis-based gait recognition for human identification," *IEEE Trans. Pattern Anal. Mach. Intell.*, vol. 25, no. 12, pp. 1505–1518, 2003.
- Base B: S. Yu, D. Tan y T. Tan, "A framework for evaluating the effect of view angle, clothing and carrying condition on gait recognition," en *Proc. 18th ICPR*, 2006, vol. 4, pp. 441–444.
- Base C: D. Tan, K. Huang, S. Yu y T. Tan, "Efficient night gait recognition based on template matching," en *Proc. 18th ICPR*, 2006, vol. 3, pp. 1000–1003.