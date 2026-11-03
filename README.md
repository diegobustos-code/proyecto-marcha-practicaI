# Análisis de marcha y clasificación de riesgo de caída en adultos mayores

Proyecto 1 de la Línea 1 (Biorrobótica y Sistemas Inteligentes), carrera de Ingeniería en Automatización y Robótica, Facultad de Ingeniería, UNAB. Desarrollado por Diego Bustos, estudiante de Ingeniería Civil Eléctrica.

Clasificación del riesgo de caída a partir de la marcha, con datos abiertos (PhysioNet LTMM y CASIA) y aprendizaje automático (Random Forest, XGBoost y LSTM). La Etapa 1 construye y compara los modelos; la Etapa 2 llevará el modelo seleccionado a un prototipo con cámara y Raspberry Pi.

## Estado del proyecto

| Sub-etapa | Semanas | Producto | Estado |
|---|---|---|---|
| 1.1 Marco teórico y estado del arte | 1-3 | Síntesis bibliográfica con tabla comparativa | ✅ Revisado, con observaciones |
| 1.2 Obtención y preparación de datos | 4-5 | Dataset limpio + notebooks de EDA | ✅ Revisado, con observaciones |
| 1.3 Implementación y entrenamiento de modelos | 6-9 | Notebooks con los tres modelos entrenados (+ extensión con vida diaria) | ✅ Revisado, con observaciones |
| 1.4 Evaluación y selección del modelo | 10-11 | Tabla comparativa + informe de selección | ✅ Revisado, con observaciones (análisis complementarios en el notebook 05b) |
| 1.5 Artículo metodológico (IEEE) | 12-14 | Borrador del paper | Borrador listo, en revisión con el profesor guía (aún no se publica en el repositorio) |

La revisión del profesor guía, Ángel Custodio (1 de noviembre de 2026), y lo que se hizo con cada observación están en la bitácora.

## Documentos de avance

Están en la carpeta [`Informes/`](Informes/), en PDF:

| Documento | Contenido |
|---|---|
| [1.1 Marco teórico y estado del arte](Informes/1.1%20Marco%20te%C3%B3rico%20y%20estado%20del%20arte%20marcha%20y%20riesgo%20de%20ca%C3%ADda.pdf) | Conceptos, revisión de 34 fuentes (14 estudios experimentales de 2020 a 2026), tablas comparativas y brechas (versión 2) |
| [Avance 1.2: obtención y preparación de datos](Informes/Avance_1.2_Obtencion_y_preparacion_de_datos.pdf) | Datos, limpieza, variables y análisis exploratorio |
| [Avance 1.3: implementación y entrenamiento de modelos](Informes/Avance_1.3_Implementacion_y_entrenamiento_de_modelos.pdf) | Random Forest, XGBoost, CNN + LSTM y extensión con registros de 3 días |
| [Avance 1.4: evaluación y selección del modelo](Informes/Avance_1.4_Evaluacion_y_seleccion_del_modelo.pdf) | Comparación estadística, sesgo de selección y modelo final |

La misma carpeta tiene los notebooks exportados a HTML, para leerlos sin instalar nada: `01_EDA_LabWalks`, `01b_Vida_diaria`, `02_EDA_CASIA`, `03_laboratorio`, `03_vida_diaria`, `03_combinado`, `04_laboratorio`, `04_vida_diaria`, `04_combinado`, `05_Evaluacion_y_seleccion` y `05b_Respuesta_revision`.

## Notebooks

| Notebook | Sub-etapa | Qué hace |
|---|---|---|
| `notebooks/01_EDA_LabWalks.ipynb` | 1.2 | Carga las caminatas de laboratorio de LTMM (PhysioNet), control de calidad, limpieza, variables de marcha y análisis exploratorio. Genera el dataset limpio. |
| `notebooks/01b_Vida_diaria.ipynb` | 1.3 (extensión) | Procesa los registros de 3 días de LTMM: detecta los tramos de marcha (detector calibrado con las caminatas de laboratorio), les calcula las mismas variables y resume cada persona. Genera las tablas de vida diaria y combinada. |
| `notebooks/02_EDA_CASIA.ipynb` | 1.2 | Revisa las siluetas de CASIA A, B y C: inventario, completitud, calidad y cadencia estimada desde la imagen. |
| `notebooks/03_Modelos_RF_XGBoost.ipynb` | 1.3 | Entrena y valida Random Forest y XGBoost, y los compara con tres referencias simples (sin modelo, una variable y TUG). |
| `notebooks/04_Modelo_LSTM.ipynb` | 1.3 | Entrena y valida una red CNN + LSTM con las señales, y compara los tres modelos en las mismas particiones. |
| `notebooks/05_Evaluacion_y_seleccion.ipynb` | 1.4 | Junta todos los resultados, los compara con la prueba t corregida para validación cruzada, evalúa los análisis definidos de antemano y selecciona el modelo final para la Etapa 2. |
| `notebooks/05b_Respuesta_revision.ipynb` | 1.4 (complemento) | Responde a la revisión del profesor guía sin modificar los notebooks anteriores: versiones exactas (`requirements.txt`), columna "6 Months Fall", referencia "solo velocidad", comparación en las mismas personas, modelo elegido contra el azar y contra el TUG, umbral en Hz, matriz de confusión y calibración. |

En los notebooks 03 y 04, el parámetro `DATOS` de la configuración elige los datos de entrada: `"laboratorio"` (caminatas de 1 minuto), `"vida_diaria"` (registros de 3 días) o `"combinado"` (ambos). Los resultados de cada opción se guardan con su nombre al final (por ejemplo `metricas_modelos_vida_diaria.csv`) y no se sobrescriben.

El significado de cada columna de los datos está en [`DICCIONARIO_DE_DATOS.md`](DICCIONARIO_DE_DATOS.md).

## Cómo ejecutarlo

1. Instalar las librerías (una vez, en la terminal de Anaconda, dentro de esta carpeta):
   ```
   pip install -r requirements.txt
   ```
   `requirements.txt` tiene las versiones exactas con que se obtuvieron los resultados (lo genera el notebook 05b). Se usó Python 3.14.
2. Copiar los datos crudos (no se suben a GitHub):
   - LabWalks: los `.hea` y `.dat` en `datos/crudos/LabWalks/` y `ClinicalDemogData_COFL.xlsx` en `datos/crudos/`. Se descargan con *LabWalks Explorer* o desde <https://physionet.org/content/ltmm/1.0.0/>.
   - Registros de 3 días: los `.hea` y `.dat` de LTMM (unos 20 GB) en `datos/crudos/Physionet/`, desde <https://physionet.org/content/ltmm/1.0.0/>.
   - CASIA: las siluetas de A, B y C en `datos/crudos/CASIA/`. Se solicitan y descargan desde <http://www.cbsr.ia.ac.cn/english/Gait%20Databases.asp>.
3. Abrir Jupyter (`jupyter lab`) y ejecutar los notebooks **en orden (01 → 01b → 02 → 03 → 04 → 05 → 05b)** con *Kernel → Restart Kernel and Run All Cells*. Cada uno usa lo que generó el anterior. El 01b tarda unos minutos y guarda a cada persona apenas la termina; si se interrumpe, continúa donde quedó.
4. Para comparar las fuentes de datos, ejecutar el 03 y luego el 04 una vez con cada valor de `DATOS` (primero las tres del 03). El 05 y el 05b se ejecutan al final, una sola vez cada uno.

## Estructura

```
notebooks/          notebooks de análisis y modelos
datos/crudos/       datos originales (no se suben)
datos/procesados/   dataset limpio, señales limpias, tablas del EDA y vida_diaria/ (tramos por persona)
modelos/            modelos entrenados (checkpoints) y su información; modelo_final.joblib es el seleccionado
resultados/         métricas de validación y tabla comparativa
figuras/            gráficos para los informes
Informes/           documentos de avance (PDF) y notebooks exportados a HTML
```

## Reproducibilidad

- Semilla fija `SEMILLA = 42` en todos los notebooks.
- Los parámetros de cada etapa están al inicio de cada notebook.
- Cada modelo guardado tiene un archivo `.json` con sus variables de entrada, parámetros, métricas y versiones de las librerías.
- Los análisis de la sub-etapa 1.4 y los criterios de selección están escritos al inicio del notebook 05. El historial de este repositorio comienza el 20 de octubre y el notebook 05 se subió junto con sus resultados, así que el historial no permite verificar de forma independiente que se fijaron antes de ver los resultados.
- Desde la revisión del 1 de noviembre, cada plan de análisis se sube **sin ejecutar** antes de correrlo (por ejemplo, el notebook 05b), para que quede con fecha anterior a sus resultados.

## Estado del arte (resumen de la sub-etapa 1.1)

La literatura muestra que la marcha permite clasificar el riesgo de caída: el metaanálisis de Mou *et al.* (2026, 20 estudios) reporta un AUC combinado de 0,85 con sensores vestibles, pero con sensibilidad moderada (0,55) y resultados más altos en estudios con menos de 100 participantes. Tabla comparativa de los estudios experimentales revisados (detalle y referencias completas en el documento 1.1):

| Estudio | Año | Participantes | Sensor | Modelo | Resultado principal | Etiqueta |
|---|---|---|---|---|---|---|
| Weiss *et al.* | 2013 | 71 adultos mayores | Acelerómetro lumbar, 3 días | Comparación estadística | Menor regularidad de la marcha en quienes caen | Retrospectiva + seguimiento 6 meses |
| Kiprijanovska *et al.* | 2020 | 18 voluntarios | Relojes inteligentes | CNN + LSTM bidireccional | Exactitud 88,9 % | Marcha alterada simulada |
| Ng *et al.* | 2020 | 31 adultos mayores con demencia | Cámara + estimación de pose | Asociación | Variables asociadas a caídas futuras | Prospectiva |
| Noh *et al.* | 2021 | 746 adultos mayores | IMU en ambos zapatos | XGBoost | Exactitud 67-70 %, sensibilidad 43-53 % | Retrospectiva |
| Lockhart *et al.* | 2021 | 171 adultos mayores | IMU en el esternón | Random Forest | Exactitud 81,6 %, sensibilidad 86,7 % | Prospectiva, 6 meses |
| Altunkaya | 2024 | 71 adultos mayores (LTMM) | Acelerómetro en la cintura, 1 min | Red neuronal multicapa | Exactitud 82,2 %, sensibilidad 82,9 % | Retrospectiva |
| Lim *et al.* | 2024 | 65 personas (jóvenes y mayores) | Dos cámaras + pose | LightGBM y MLP | Exactitud 96 % (una sola división) | Escalas clínicas |
| García-de-Villa *et al.* | 2025 | 157 mayores de 70 años | IMU en el pie | Máquina de vectores de soporte | Exactitud 79,6 % | Retrospectiva |
| Sakane *et al.* | 2025 | 190 trabajadores de mediana edad | Cámara RGB + MediaPipe | Aprendizaje automático | AUC 0,909 (hombres) y 0,670 (mujeres) | Retrospectiva |
| Alqurashi *et al.* | 2026 | 163 personas de 70 a 98 años | IMU | CNN y LSTM | Exactitud 87-92 % validando por sujeto | Retrospectiva |
| **Este proyecto** | 2026 | 72 adultos mayores (LTMM) | Acelerómetro lumbar, 1 min | Regresión logística (1 variable) | **AUC 0,72** (IC 95 %: 0,57-0,88), exactitud 69 %; validación cruzada 5 × 10 por sujeto | Retrospectiva |

**Brechas identificadas** y cómo las aborda el proyecto:

1. **Etiquetas retrospectivas** → se reconoce como limitación. La columna "6 Months Fall" de la planilla clínica también resultó retrospectiva (notebook 05b), así que LTMM no permite evaluar la predicción de caídas futuras.
2. **Validación poco rigurosa** → validación cruzada repetida separando por persona, prueba t corregida y selección de variables dentro de cada partición (sub-etapa 1.4).
3. **Sensibilidad baja** → se reportan sensibilidad y especificidad, y se fija un umbral de tamizaje con sensibilidad ≥ 80 %.
4. **Falta de estandarización** → se documentan sensor, frecuencia de muestreo, limpieza y variables (`DICCIONARIO_DE_DATOS.md`).
5. **Pocos estudios con cámara en adultos mayores** → el modelo final usa solo variables medibles con cámara, como base para la Etapa 2.

## Bitácora

### Semanas 1-3 · Sub-etapa 1.1: marco teórico y estado del arte

**Avanzado**
- Marco teórico: parámetros de marcha, sensores inerciales y estimación de pose, Random Forest, XGBoost y LSTM, métricas y validación.
- Revisión de 26 fuentes, de las cuales 16 son trabajos científicos publicados entre 2020 y 2026, incluidos tres metaanálisis o revisiones sistemáticas.
- Tabla comparativa de 10 estudios experimentales (participantes, sensor, modelo, resultado y tipo de etiqueta).
- Cinco brechas identificadas: etiquetas retrospectivas, validación poco rigurosa, sensibilidad baja, falta de estandarización y pocos estudios con cámara en adultos mayores.

**Decisiones**
- Base de datos: LTMM (PhysioNet), porque es abierta y tiene etiquetas de caída; se reconoce que son retrospectivas y que la muestra es pequeña (71 personas).
- Variables: parámetros espaciotemporales más medidas de variabilidad y regularidad (siguiendo a Weiss *et al.*).
- Validación cruzada separando por sujeto (Saeb *et al.*, Alqurashi *et al.*), con exactitud, sensibilidad, F1 y AUC.
- Referencias de comparación: Altunkaya (mismas caminatas de LTMM), un método simple de una variable y el TUG.
- CASIA no sirve para entrenar (no tiene etiquetas de caída); puede servir para probar la estimación de pose.

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

### Extensión de la 1.3 (22-10-2026): registros de 3 días de vida diaria

**Avanzado**
- Descarga de los registros de 3 días de LTMM (71 registros, unos 20 GB, mediana de 74 h cada uno).
- Notebook `01b_Vida_diaria.ipynb`: detección de tramos de marcha de al menos 30 s, mismas variables del notebook 01 y mediana por persona. Resultado: 3905 tramos detectados, 3741 válidos (96 %), 70 personas (40 no caen, 30 caen).
- Parámetro `DATOS` en los notebooks 03 y 04 para entrenar con laboratorio, vida diaria o ambos.
- Avance 1.3 actualizado con la extensión.

**Resultados**
- Ninguna variable de vida diaria es significativa tras Holm. Las mayores diferencias: quienes caen tienen menor amplitud dominante (r = −0,31) y caminan algo menos (22,6 vs 28,1 min/día, no significativo).
- El ritmo de la marcha se parece entre laboratorio y casa (cadencia ρ = 0,62), pero la variabilidad casi no (CV ρ ≈ 0,2).
- AUC en las mismas 15 pruebas (laboratorio / vida diaria / combinado): Random Forest 0,68 / 0,60 / 0,64 · XGBoost 0,67 / 0,60 / 0,58 · LSTM 0,55 / 0,62 / 0,57 · solo frecuencia dominante 0,75 / 0,38 / 0,71.
- Los registros de 3 días **no mejoran la clasificación** con estas variables (resultado negativo; las diferencias están dentro de la variación de ±0,15).

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

### Semanas 12-14 · Sub-etapa 1.5: artículo metodológico (adelantada)

**Avanzado**
- Borrador del artículo en plantilla IEEE, en inglés y en español (8 páginas cada uno), con sus proyectos para Overleaf. Se comparte con el profesor guía y se subirá al repositorio cuando se decida publicarlo.
- Se verificaron las 46 referencias contra su fuente original (autores, revista, volumen, páginas y DOI).
- Se revisó que estuvieran todos los productos de la Etapa 1 pedidos en el plan.
- Se contrastaron los resultados con literatura fuera de la bibliografía del 1.1 y se agregaron nueve referencias (ver *Fuentes adicionales*).

**Resultados que agrega el artículo**
- Cálculo de tamaño de muestra (Riley *et al.*, 2020): incluso un modelo de una variable necesitaría unas 380 personas, y con las nueve variables de cámara, unas 500. Con 72, solo se pueden estimar modelos muy simples.
- El estudio más cercano con LTMM (Askhatova *et al.*, 2026, red convolucional temporal) reporta AUC medio 0,75, dentro del intervalo del modelo de una variable de este proyecto.
- Contraste con van Schooten *et al.* (2015), donde la marcha de vida diaria sí mejoró la predicción prospectiva: el resultado negativo de la extensión se presenta como propio de LTMM y de estas variables.

**Decisiones**
- La afirmación de novedad se redactó con prudencia: lo nuevo es la comparación contra referencias simples con validación repetida y prueba corregida.
- Se declara que el informe aún no se revisó punto por punto con la guía TRIPOD+AI.

### Revisión del profesor guía (01-11-2026)

**Resultado:** los avances 1.1 a 1.4 cumplen lo esencial del plan, con observaciones. El repositorio está completo y los notebooks 03 y 05 se reprodujeron en un entorno limpio (XGBoost varió de 0,644 a 0,636 por la versión de la librería).

**Hallazgos de la revisión**
- La planilla clínica tiene una columna "6 Months Fall" (66 personas) que, según el profesor, podría permitir evaluar la predicción de caídas futuras.
- La planilla tiene la velocidad de marcha ("base(velocity)", 68 personas), con ρ = 0,56 respecto de la frecuencia dominante en la prueba rápida del profesor.

**Acciones (2-11-2026)**
- Notebook `05b_Respuesta_revision.ipynb`, con el plan de análisis escrito antes de ejecutarlo:
  - Escribe `requirements.txt` con las versiones exactas instaladas.
  - Antes de usar "6 Months Fall", la cruza con el grupo. Weiss *et al.* (2013) registraron dos variables distintas: las caídas de los 6 meses **anteriores** (media 0,15 en controles y 2,56 en quienes caen) y un **seguimiento** de 6 meses posterior (12 personas con 2 o más caídas). Solo en el segundo caso sirve como etiqueta prospectiva.
  - Agrega la referencia "solo velocidad" y "velocidad + frecuencia dominante" en las mismas 50 particiones del notebook 03.
  - Compara frecuencia dominante, velocidad y TUG en las mismas personas, sin completar datos faltantes.
  - Compara el modelo elegido contra el azar (AUC 0,5) y contra el TUG.
  - Expresa el umbral en Hz, estima la especificidad con el umbral elegido solo con las personas de entrenamiento, y agrega matriz de confusión y calibración (Brier).
- Se corrigió la carrera en este README: el proyecto pertenece a Ingeniería en Automatización y Robótica; el investigador es estudiante de Ingeniería Civil Eléctrica.

**Cambios respecto del plan original (para aprobación del profesor guía)**
- CASIA no se usa para entrenar, porque no tiene etiquetas de caída. Además, las siluetas descargadas no sirven para MediaPipe, que necesita video RGB.
- No se usó Gait in Aging and Disease, porque no tiene etiquetas de caída; se usó LTMM.
- Se usó PyTorch en vez de TensorFlow.
- No se pudieron calcular el largo de zancada ni la velocidad desde el sensor lumbar (se podrían estimar con un modelo de péndulo invertido, que requiere la altura del sensor).
- La comparación "solo velocidad" prometida en el 1.1 se había reemplazado por la frecuencia dominante; se retoma con la velocidad de la planilla clínica.
- El modelo final es una regresión logística de una variable, no Random Forest, XGBoost ni LSTM.
- La extensión con registros de 3 días no estaba en el plan.

**Resultados del notebook 05b (03-11-2026)**
- **"6 Months Fall" es retrospectiva** (caídas de los 6 meses anteriores), no el seguimiento prospectivo. Tiene una correlación de ρ = 0,93 con las caídas del año anterior, que la predicen con AUC 0,98; sus medias (2,86 en quienes caen y 0,14 en quienes no) coinciden con la variable "caídas en los últimos 6 meses" de la Tabla 1 de Weiss *et al.* (2,56 y 0,15), y 22 personas tienen 2 o más caídas, frente a 12 en el seguimiento. La regla escrita en el plan concluyó "posiblemente prospectiva" porque sus umbrales eran demasiado estrictos (exigía al menos 90 % de FL y como máximo 2 controles con caídas; hubo 84 % y 5). Se deja registrado el error de la regla y **no se usa como etiqueta nueva**: la brecha de las etiquetas retrospectivas se mantiene.
- **Velocidad:** "solo velocidad" AUC 0,67 (IC 95 %: 0,50 a 0,83); correlación con la frecuencia dominante ρ = 0,56. "Velocidad + frecuencia dominante" llega a 0,72, sin diferencia significativa con la velocidad sola (p = 0,29). La frecuencia dominante refleja en parte la velocidad y con 72 personas no se puede separar su aporte.
- **Mismas 68 personas, sin completar datos:** frecuencia dominante 0,74, velocidad 0,69 y TUG 0,68; las tres superan al azar (p Holm ≤ 0,01). Frecuencia dominante frente a TUG: +0,07, p = 0,41.
- **Modelo elegido contra el azar y el TUG:** AUC 0,72, significativamente mayor que el azar (p Holm = 0,017) y **no significativamente distinto del TUG** (0,66; p Holm = 0,47).
- **Umbral en Hz:** riesgo si la frecuencia dominante es menor que 1,93 Hz (unos 116 pasos por minuto).
- **Umbral elegido sin mirar la prueba:** sensibilidad 0,78 y especificidad 0,54 (con el umbral del notebook 05: 0,81 y 0,48). Matriz de confusión promedio: de 34 personas que caen, 26,6 detectadas; de 38 que no caen, 17,5 marcadas como riesgo.
- **Calibración:** puntaje de Brier 0,216, frente a 0,249 de un modelo que siempre predice la proporción de caedores.
- Se fijaron las versiones en `requirements.txt` (13 paquetes; Python 3.14.6, scikit-learn 1.9.0, XGBoost 3.4.1).

**Correcciones de los informes (03-11-2026)**
- **1.1 (versión 2):** método de búsqueda (falta completar bases, palabras clave y fechas), cinco estudios experimentales más (Buisseret 2020, Wang 2023, Robles Cruz 2024, Neira Álvarez 2024 y Askhatova 2026), nueva Tabla II con datos, validación y métricas de cada estudio, cifra de caídas en Chile (SABE, Santiago 34,0 %), aclaración de 71 y 73 personas en LTMM, G-STRIDE como base para validación externa, Barry *et al.* en lugar de Bargiotas *et al.* para el TUG y corrección de la promesa de calcular largo de zancada y velocidad.
- **1.2 (versión 2):** URL del repositorio, señales limpias de la LSTM documentadas, cambio respecto del plan (sin velocidad ni largo de zancada), filtro de redundancia aplicado a todas las personas, posible efecto de la duración de las caminatas y redacción más prudente sobre CASIA (la cadencia es coherente, no validada; las siluetas no sirven para MediaPipe).
- **1.3 (versión 3):** tabla de correspondencia de personas entre fuentes (incluidos los archivos CO028_n, CO029_n y CO038_2), explicación del AUC de 0,38, épocas de la LSTM final y límites de la parada temprana, tratamiento de las 4 personas sin TUG, sesgo del detector calibrado con las mismas personas y alcance de la extensión.
- **1.4 (versión 2):** "numéricamente mayor, sin diferencias significativas", comparación con el azar y con el TUG, umbral en Hz, umbral elegido sin mirar la prueba, matriz de confusión, calibración, velocidad, columna de 6 meses y sesgos declarados.
- **Artículo 1.5:** resultados del 05b, sesgos declarados y Etapa 2 replanteada.

**Pendiente**
- Completar en el informe 1.1 las bases consultadas, las palabras clave y las fechas de la búsqueda.
- Control de duración: recortar todas las caminatas a la misma duración y repetir el análisis (notebook 05c, con su plan subido antes de ejecutarlo).
- Aprobación por escrito del profesor guía de los cambios respecto del plan.
- Etapa 2: conseguir video RGB y replantear las sub-etapas 2.1 y 2.2 con foco en validar la frecuencia dominante medida con cámara contra un sensor inercial en las mismas personas.

## Fuentes de datos

**PhysioNet – Long Term Movement Monitoring (LTMM)**
- A. Weiss *et al.*, "Does the evaluation of gait quality during daily life provide insight into fall risk? A novel approach using 3-day accelerometer recordings," *Neurorehabil. Neural Repair*, vol. 27, no. 8, pp. 742–752, 2013, doi: 10.1177/1545968313491004.
- J. M. Hausdorff, "Long Term Movement Monitoring Database" (versión 1.0.0), PhysioNet, 2016, doi: 10.13026/C2S59C.
- A. L. Goldberger *et al.*, "PhysioBank, PhysioToolkit, and PhysioNet," *Circulation*, vol. 101, no. 23, pp. e215–e220, 2000, doi: 10.1161/01.CIR.101.23.e215.

**CASIA Gait Database** (Institute of Automation, Chinese Academy of Sciences) — <http://www.cbsr.ia.ac.cn/english/Gait%20Databases.asp>
- Base A: L. Wang, T. Tan, H. Ning y W. Hu, "Silhouette analysis-based gait recognition for human identification," *IEEE Trans. Pattern Anal. Mach. Intell.*, vol. 25, no. 12, pp. 1505–1518, 2003.
- Base B: S. Yu, D. Tan y T. Tan, "A framework for evaluating the effect of view angle, clothing and carrying condition on gait recognition," en *Proc. 18th ICPR*, 2006, vol. 4, pp. 441–444.
- Base C: D. Tan, K. Huang, S. Yu y T. Tan, "Efficient night gait recognition based on template matching," en *Proc. 18th ICPR*, 2006, vol. 3, pp. 1000–1003.

## Fuentes adicionales usadas después de la sub-etapa 1.1

La bibliografía completa del estado del arte (26 fuentes) está en el documento 1.1. Además, en las sub-etapas 1.2 a 1.5 se usaron:

- R. Moe-Nilssen y J. L. Helbostad, "Estimation of gait cycle characteristics by trunk accelerometry," *J. Biomech.*, vol. 37, no. 1, pp. 121–126, 2004 (regularidad y simetría por autocorrelación, sub-etapa 1.2).
- S. Holm, "A simple sequentially rejective multiple test procedure," *Scand. J. Statist.*, vol. 6, no. 2, pp. 65–70, 1979 (corrección por comparaciones múltiples).
- F. Pedregosa *et al.*, "Scikit-learn: Machine learning in Python," *J. Mach. Learn. Res.*, vol. 12, pp. 2825–2830, 2011.
- A. Paszke *et al.*, "PyTorch: An imperative style, high-performance deep learning library," en *Adv. Neural Inf. Process. Syst. 32*, 2019, pp. 8024–8035.
- C. Nadeau y Y. Bengio, "Inference for the generalization error," *Mach. Learn.*, vol. 52, no. 3, pp. 239–281, 2003 (prueba t corregida, sub-etapa 1.4).
- R. R. Bouckaert y E. Frank, "Evaluating the replicability of significance tests for comparing learning algorithms," en *PAKDD 2004*, LNCS vol. 3056, pp. 3–12 (sub-etapa 1.4).
- C. Ambroise y G. J. McLachlan, "Selection bias in gene extraction on the basis of microarray gene-expression data," *Proc. Natl. Acad. Sci. USA*, vol. 99, no. 10, pp. 6562–6566, 2002 (sesgo de selección de variables, sub-etapa 1.4).
- W. Zijlstra y A. L. Hof, "Assessment of spatio-temporal gait parameters from trunk accelerations during human walking," *Gait Posture*, vol. 18, no. 2, pp. 1–10, 2003 (estimación del largo de paso, sub-etapa 1.5).
- E. Barry, R. Galvin, C. Keogh, F. Horgan y T. Fahey, "Is the Timed Up and Go test a useful predictor of risk of falls in community dwelling older adults: A systematic review and meta-analysis," *BMC Geriatr.*, vol. 14, art. 14, 2014 (limitaciones del TUG, sub-etapa 1.5).
- E. Christodoulou *et al.*, "A systematic review shows no performance benefit of machine learning over logistic regression for clinical prediction models," *J. Clin. Epidemiol.*, vol. 110, pp. 12–22, 2019 (sub-etapa 1.5).
- Y. Gao *et al.*, "Machine learning and deep learning models for predicting future falls in community-dwelling older adults: Systematic review and meta-analysis of longitudinal evidence," *J. Med. Internet Res.*, vol. 28, art. e84844, 2026 (sub-etapa 1.5).
- I. Hillel *et al.*, "Is every-day walking in older adults more analogous to dual-task walking or to usual walking?," *Eur. Rev. Aging Phys. Act.*, vol. 16, art. 6, 2019 (marcha en laboratorio frente a vida diaria, sub-etapa 1.5).
- K. S. van Schooten *et al.*, "Ambulatory fall-risk assessment: Amount and quality of daily-life gait predict falls in older adults," *J. Gerontol. A Biol. Sci. Med. Sci.*, vol. 70, no. 5, pp. 608–615, 2015 (sub-etapa 1.5).
- A. Nait Aicha *et al.*, "Deep learning to predict falls in older adults based on daily-life trunk accelerometry," *Sensors*, vol. 18, no. 5, art. 1654, 2018 (sub-etapa 1.5).
- A. Askhatova *et al.*, "A temporal convolutional network approach for gait-based fall risk prediction to support safe sport participation in older adults," *MethodsX*, vol. 16, art. 103845, 2026 (estudio más cercano con LTMM, sub-etapa 1.5).
- R. D. Riley *et al.*, "Calculating the sample size required for developing a clinical prediction model," *BMJ*, vol. 368, art. m441, 2020 (tamaño de muestra, sub-etapa 1.5).
- G. S. Collins *et al.*, "TRIPOD+AI statement: Updated guidance for reporting clinical prediction models that use regression or machine learning methods," *BMJ*, vol. 385, art. e078378, 2024 (sub-etapa 1.5).
- S. García-de-Villa *et al.*, "A database with frailty, functional and inertial gait metrics for the research of fall causes in older adults," *Sci. Data*, vol. 10, 2023 (base GSTRIDE, candidata para validación externa).
