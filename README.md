# Análisis de marcha y clasificación de riesgo de caída en adultos mayores

Línea 1, Proyecto 1 · Ingeniería Civil Eléctrica, UNAB

## Notebooks

| Notebook | Sub-etapa | Qué hace |
|---|---|---|
| `notebooks/01_EDA_LabWalks.ipynb` | 1.2 | Carga las caminatas de laboratorio de LTMM (PhysioNet), control de calidad, limpieza, variables de marcha y análisis exploratorio. Genera el dataset limpio. |
| `notebooks/01b_Vida_diaria.ipynb` | 1.3 (extensión) | Procesa los registros de 3 días de LTMM: detecta los tramos de marcha (detector calibrado con las caminatas de laboratorio), les calcula las mismas variables y resume cada persona. Genera las tablas de vida diaria y combinada. |
| `notebooks/02_EDA_CASIA.ipynb` | 1.2 | Revisa las siluetas de CASIA A, B y C: inventario, completitud, calidad y cadencia estimada desde la imagen. |
| `notebooks/03_Modelos_RF_XGBoost.ipynb` | 1.3 | Entrena y valida Random Forest y XGBoost, y los compara con tres referencias simples (sin modelo, una variable y TUG). |
| `notebooks/04_Modelo_LSTM.ipynb` | 1.3 | Entrena y valida una red CNN + LSTM con las señales, y compara los tres modelos en las mismas particiones. |
| `notebooks/05_Evaluacion_y_seleccion.ipynb` | 1.4 | Junta todos los resultados, los compara con la prueba t corregida para validación cruzada, evalúa los análisis definidos de antemano y selecciona el modelo final para la Etapa 2. |

En los notebooks 03 y 04, el parámetro `DATOS` de la configuración elige los datos de entrada: `"laboratorio"` (caminatas de 1 minuto), `"vida_diaria"` (registros de 3 días) o `"combinado"` (ambos). Los resultados de cada opción se guardan con su nombre al final (por ejemplo `metricas_modelos_vida_diaria.csv`) y no se sobrescriben.

El significado de cada columna de los datos está en [`DICCIONARIO_DE_DATOS.md`](DICCIONARIO_DE_DATOS.md).

## Cómo ejecutarlo

1. Instalar las librerías (una vez, en la terminal de Anaconda, dentro de esta carpeta):
```
   pip install -r requirements.txt
```
2. Copiar los datos crudos (no se suben a GitHub):
   - LabWalks: los `.hea` y `.dat` en `datos/crudos/LabWalks/` y `ClinicalDemogData_COFL.xlsx` en `datos/crudos/`. Se descargan con *LabWalks Explorer* o desde <https://physionet.org/content/ltmm/1.0.0/>.
   - Registros de 3 días: los `.hea` y `.dat` de LTMM (unos 20 GB) en `datos/crudos/Physionet/`, desde <https://physionet.org/content/ltmm/1.0.0/>.
   - CASIA: las siluetas de A, B y C en `datos/crudos/CASIA/`.
3. Abrir Jupyter (`jupyter lab`) y ejecutar los notebooks **en orden (01 → 01b → 02 → 03 → 04 → 05)** con *Kernel → Restart Kernel and Run All Cells*. Cada uno usa lo que generó el anterior. El 01b tarda unos minutos y guarda a cada persona apenas la termina; si se interrumpe, continúa donde quedó.
4. Para comparar las fuentes de datos, ejecutar el 03 y luego el 04 una vez con cada valor de `DATOS` (primero las tres del 03). El 05 se ejecuta al final, una sola vez.

## Estructura

```
notebooks/          notebooks de análisis y modelos
datos/crudos/       datos originales (no se suben)
datos/procesados/   dataset limpio, señales limpias, tablas del EDA y vida_diaria/ (tramos por persona)
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

### 22-10-2026 · Extensión de la 1.3: registros de 3 días de vida diaria

**Avanzado**
- Descarga de los registros de 3 días de LTMM (71 registros, unos 20 GB, mediana de 74 h cada uno).
- Notebook `01b_Vida_diaria.ipynb`: detección de tramos de marcha de al menos 30 s, mismas variables del notebook 01 y mediana por persona. Resultado: 3905 tramos detectados, 3741 válidos (96 %), 70 personas (40 no caen, 30 caen).
- Parámetro `DATOS` en los notebooks 03 y 04 para entrenar con laboratorio, vida diaria o ambos.
- Avance 1.3 actualizado con la extensión.

**Resultados**
- Ninguna variable de vida diaria es significativa tras Holm. Las mayores diferencias: quienes caen tienen menor amplitud dominante (r = −0,31) y caminan algo menos (22,6 vs 28,1 min/día, no significativo).
- El ritmo de la marcha se parece entre laboratorio y casa (cadencia ρ = 0,62), pero la variabilidad casi no (CV ρ ≈ 0,2).
- AUC en las mismas 15 pruebas (laboratorio / vida diaria / combinado): Random Forest 0,68 / 0,60 / 0,64 · XGBoost 0,67 / 0,60 / 0,58 · LSTM 0,55 / 0,62 / 0,57 · solo frecuencia dominante 0,75 / 0,38 / 0,71.
- Los registros de 3 días **no mejoran la clasificación** con estas variables (resultado negativo; las diferencias están dentro de la variación de ±0,15). La mejor referencia sigue siendo la frecuencia dominante de laboratorio.

**Decisiones**
- El detector se calibró con las caminatas de laboratorio, donde se sabe que la persona camina: el umbral de ritmo de pasos bajó de 0,50 a 0,29 y las caminatas bajo 80 % detectado pasaron de 27 a 8. La calibración solo relaja los umbrales.
- Cada tramo debe pasar el mismo control de calidad que una caminata de laboratorio (≥ 10 pasos y ≤ 20 % de intervalos descartados). Se excluye a quien tenga menos de 5 tramos válidos (se excluyó FL035).
- Para la LSTM se usan como máximo 60 ventanas por persona, para que quienes caminan más no dominen el entrenamiento.
- Las señales de vida diaria (`datos/procesados/vida_diaria/senales/`) no se suben a GitHub; se regeneran con el 01b.

**Limitación a reportar:** el detector reconoce peor la marcha más irregular (8 caminatas de laboratorio bajo 80 %, casi todas de personas que caen).

### Semanas 10-11 · Sub-etapa 1.4: evaluación y selección del modelo

**Avanzado**
- Notebook `05_Evaluacion_y_seleccion.ipynb`: 27 combinaciones de modelo y fuente de datos, con la prueba t corregida de Nadeau y Bengio (2003) + Holm e intervalos de confianza del AUC.
- Siete modelos nuevos, definidos antes de ver los resultados: selección de variables dentro de cada partición, modelos con variables medibles con cámara y dos variantes de vida diaria.
- Tabla comparativa (`resultados/tabla_comparativa.csv`), modelo final (`modelos/modelo_final.joblib`) y avance 1.4 (informe de selección).

**Resultados**
- Ningún modelo es significativamente peor que el mejor de su fuente: los intervalos del AUC miden unos ±0,15 y se superponen.
- Sesgo de selección: la frecuencia dominante elegida en el EDA daba AUC 0,75; eligiendo la variable dentro de cada partición entre todas, 0,62; entre las de cámara, 0,72 (elige la frecuencia dominante en 46 de 50 particiones).
- Las variantes de vida diaria no mejoran (mejor: variabilidad entre tramos con Random Forest, 0,64).
- **Modelo seleccionado:** regresión logística con la frecuencia dominante. AUC 0,72 (IC 95 %: 0,57 a 0,88); coeficiente −0,87 (menor frecuencia = más riesgo). Con umbral 0,40: sensibilidad ≥ 80 % y especificidad 0,47.

**Decisiones**
- Criterios de selección fijados de antemano: (1) variables medibles con cámara en condiciones de laboratorio, como en la Etapa 2; (2) no ser significativamente peor que el mejor; (3) el más simple.
- La referencia elegida en el EDA no se usa como punto de comparación, porque su AUC está inflado.
- Umbral de tamizaje elegido para sensibilidad ≥ 80 % (importa más no dejar pasar a quien cae).

**Pendiente**
- Sub-etapa 1.5: artículo en plantilla IEEE (benchmark honesto, sesgo de selección y resultado negativo de la vida diaria).
- Etapa 2: medir la frecuencia dominante con MediaPipe, compararla con la del sensor y recalibrar el umbral.

## Fuentes de datos

**PhysioNet – Long Term Movement Monitoring (LTMM)**
- A. Weiss *et al.*, "Does the evaluation of gait quality during daily life provide insight into fall risk? A novel approach using 3-day accelerometer recordings," *Neurorehabil. Neural Repair*, vol. 27, no. 8, pp. 742–752, 2013, doi: 10.1177/1545968313491004.
- J. M. Hausdorff, "Long Term Movement Monitoring Database" (versión 1.0.0), PhysioNet, 2016, doi: 10.13026/C2S59C.
- A. L. Goldberger *et al.*, "PhysioBank, PhysioToolkit, and PhysioNet," *Circulation*, vol. 101, no. 23, pp. e215–e220, 2000, doi: 10.1161/01.CIR.101.23.e215.

**CASIA Gait Database**
- Base A: L. Wang, T. Tan, H. Ning y W. Hu, "Silhouette analysis-based gait recognition for human identification," *IEEE Trans. Pattern Anal. Mach. Intell.*, vol. 25, no. 12, pp. 1505–1518, 2003.
- Base B: S. Yu, D. Tan y T. Tan, "A framework for evaluating the effect of view angle, clothing and carrying condition on gait recognition," en *Proc. 18th ICPR*, 2006, vol. 4, pp. 441–444.
- Base C: D. Tan, K. Huang, S. Yu y T. Tan, "Efficient night gait recognition based on template matching," en *Proc. 18th ICPR*, 2006, vol. 3, pp. 1000–1003.