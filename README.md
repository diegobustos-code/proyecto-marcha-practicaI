# Análisis de marcha y clasificación de riesgo de caída en adultos mayores

Línea 1, Proyecto 1 · Ingeniería Civil Eléctrica, UNAB

## Sub-etapa 1.2: obtención y preparación de datos (semanas 4-5)

| Notebook | Qué hace |
|---|---|
| `notebooks/01_EDA_LabWalks.ipynb` | Carga las 73 caminatas de laboratorio de LTMM (PhysioNet), revisa su calidad, las limpia, calcula variables de marcha y hace el análisis exploratorio. Genera el **dataset limpio** `datos/procesados/labwalks_variables.csv`. |
| `notebooks/02_EDA_CASIA.ipynb` | Revisa las siluetas de CASIA A, B y C: inventario, completitud, calidad y una primera estimación de la cadencia desde la imagen. |

El significado de cada columna está en [`DICCIONARIO_DE_DATOS.md`](DICCIONARIO_DE_DATOS.md).

## Cómo ejecutarlo

1. Instalar las librerías (una vez, en la terminal de Anaconda, dentro de esta carpeta):
   ```
   pip install -r requirements.txt
   ```
2. Copiar los datos:
   - LabWalks: los `.hea` y `.dat` en `datos/crudos/LabWalks/` y, si está, `ClinicalDemogData_COFL.xlsx` en `datos/crudos/`. Se pueden descargar con la app *LabWalks Explorer* o desde <https://physionet.org/content/ltmm/1.0.0/>.
   - CASIA: descomprimir las siluetas de A, B y C dentro de `datos/crudos/CASIA/`.
3. Abrir Jupyter en esta carpeta (`jupyter lab`), abrir cada notebook y ejecutar *Kernel → Restart Kernel and Run All Cells*.

Los datos crudos no se suben a GitHub (ver `.gitignore`); cada persona del equipo los descarga de su fuente.

## Estructura

```
notebooks/          notebooks de análisis
datos/crudos/       datos originales (no se suben)
datos/procesados/   dataset limpio y tablas que generan los notebooks
figuras/            gráficos para el informe
```

## Reproducibilidad

- Semilla fija `SEMILLA = 42` en ambos notebooks (también se usará en los modelos).
- Todos los parámetros de limpieza están al inicio de cada notebook.
- Al inicio de cada notebook se imprimen las versiones de Python y de las librerías.

## Fuentes de datos

**PhysioNet – Long Term Movement Monitoring (LTMM)**
- A. Weiss *et al.*, "Does the evaluation of gait quality during daily life provide insight into fall risk? A novel approach using 3-day accelerometer recordings," *Neurorehabil. Neural Repair*, vol. 27, no. 8, pp. 742–752, 2013, doi: 10.1177/1545968313491004.
- J. M. Hausdorff, "Long Term Movement Monitoring Database" (versión 1.0.0), PhysioNet, 2016, doi: 10.13026/C2S59C.
- A. L. Goldberger *et al.*, "PhysioBank, PhysioToolkit, and PhysioNet: Components of a new research resource for complex physiologic signals," *Circulation*, vol. 101, no. 23, pp. e215–e220, 2000, doi: 10.1161/01.CIR.101.23.e215.

**CASIA Gait Database**
- Base A: L. Wang, T. Tan, H. Ning y W. Hu, "Silhouette analysis-based gait recognition for human identification," *IEEE Trans. Pattern Anal. Mach. Intell.*, vol. 25, no. 12, pp. 1505–1518, 2003.
- Base B: S. Yu, D. Tan y T. Tan, "A framework for evaluating the effect of view angle, clothing and carrying condition on gait recognition," en *Proc. 18th Int. Conf. Pattern Recognit. (ICPR)*, 2006, vol. 4, pp. 441–444, doi: 10.1109/ICPR.2006.67.
- Base C: D. Tan, K. Huang, S. Yu y T. Tan, "Efficient night gait recognition based on template matching," en *Proc. 18th Int. Conf. Pattern Recognit. (ICPR)*, 2006, vol. 3, pp. 1000–1003.
## Bitácora

### Semanas 4-5 · Sub-etapa 1.2: obtención y preparación de datos
**Avanzado**
- Descarga de LTMM/LabWalks (73 registros, 38 no caen y 35 caen) y de CASIA A, B y C (20, 124 y 153 personas).
- Control de calidad: ningún registro de LabWalks excluido; CASIA-B completa al 99,7 %; menos de 0,1 % de cuadros vacíos.
- Limpieza (recorte de 3 s, filtro de 20 Hz, eliminación de la gravedad) y cálculo de 18 variables de marcha por persona.
- EDA: quienes caen muestran menor frecuencia dominante (1,78 vs 1,93 Hz; p Holm = 0,004) y menor movimiento del tronco.
- Primera estimación de la cadencia desde siluetas de CASIA: distingue entre marcha lenta, normal y rápida.

**Decisiones tomadas**
- `n_pasos` no se usará como variable, porque depende de la duración del registro.
- CASIA no se usará para entrenar, solo para probar la estimación de pose en la etapa 2.
- Se excluirán las secuencias de CASIA con menos de 20 cuadros.

**Pendiente**
- Corregir el cálculo del CV del tiempo de paso (valores extremos por pausas o giros).
- Sub-etapa 1.3: entrenar Random Forest, XGBoost y LSTM con validación por sujeto.