# Diccionario de datos

## `datos/procesados/labwalks_variables.csv` (dataset limpio)

Una fila por persona. Origen: caminatas de laboratorio de la base LTMM, sensor inercial en la zona lumbar, 100 Hz.

### Identificación y etiqueta

| Columna | Tipo | Unidad | Descripción |
|---|---|---|---|
| `registro` | texto | | Nombre del archivo en PhysioNet (`co001_base`, `fl001_base`, …) |
| `grupo` | texto | | `No cae` (archivos `co`) o `Cae` (archivos `fl`, dos o más caídas en el año previo) |
| `cae` | 0 / 1 | | **Variable objetivo** para la clasificación: 1 = cae |
| `edad` | número | años | Del encabezado del archivo `.hea` |
| `sexo` | texto | | `F` o `M`, del encabezado |
| `fs_hz` | número | Hz | Frecuencia de muestreo |
| `duracion_util_s` | número | s | Duración analizada, tras quitar 3 s al inicio y al final |
| `n_pasos` | número | pasos | Pasos detectados. **Solo descriptivo:** no se usa como variable de marcha porque depende de la duración del registro |
| `pct_intervalos_descartados` | número | % | Porcentaje de intervalos entre pasos descartados por anómalos (pausas, giros o pasos mal detectados). Sirve para controlar la calidad de la detección |

Si está la planilla clínica, se agregan al final sus columnas originales (por ejemplo TUG o número de caídas). Su significado está en la hoja `Legend` de la misma planilla. La columna de género, que tiene un nombre distinto en cada hoja, se unifica como `genero_planilla` (`F` o `M`).

### Variables temporales (desde los pasos detectados en la aceleración vertical)

Antes de calcularlas se descartan los intervalos entre pasos menores a 0,5 o mayores a 1,5 veces la mediana de cada persona, porque corresponden a pausas, giros o pasos mal detectados.

| Columna | Unidad | Descripción |
|---|---|---|
| `cadencia_pasos_min` | pasos/min | 60 / tiempo medio de paso |
| `tiempo_paso_s` | s | Tiempo medio entre dos pasos seguidos |
| `cv_tiempo_paso_pct` | % | Variabilidad del tiempo de paso: desviación estándar / media × 100 |
| `tiempo_zancada_s` | s | Tiempo medio de una zancada (dos pasos) |
| `cv_tiempo_zancada_pct` | % | Variabilidad del tiempo de zancada |

### Regularidad y simetría (autocorrelación de la aceleración vertical, Moe-Nilssen y Helbostad, 2004)

| Columna | Rango | Descripción |
|---|---|---|
| `regularidad_paso` | 0 a 1 | Parecido entre un paso y el siguiente (1 = idénticos) |
| `regularidad_zancada` | 0 a 1 | Parecido entre una zancada y la siguiente |
| `simetria` | ≈ 1 | regularidad_paso / regularidad_zancada; se aleja de 1 cuando la pierna izquierda y la derecha se mueven distinto |

### Variables frecuenciales (espectro de la aceleración vertical, como en Weiss et al., 2013)

| Columna | Unidad | Descripción |
|---|---|---|
| `frec_dominante_hz` | Hz | Frecuencia con más energía entre 0,5 y 3 Hz (≈ pasos por segundo) |
| `amplitud_dominante` | g²/Hz | Potencia en esa frecuencia |
| `ancho_pico_hz` | Hz | Ancho del pico dominante a media altura; un pico más ancho indica una marcha menos constante |

### Intensidad

| Columna | Unidad | Descripción |
|---|---|---|
| `rms_acc_v`, `rms_acc_ml`, `rms_acc_ap` | g | Valor RMS de la aceleración vertical, mediolateral y anteroposterior (sin su promedio) |
| `rms_giro_…` | °/s (la unidad del archivo) | Valor RMS de la velocidad angular en cada eje del giroscopio |

Los canales se renombran al cargarlos para que el análisis no dependa de cómo se llamen en el archivo: `acc_v`, `acc_ml`, `acc_ap` para la aceleración y `giro_yaw`, `giro_pitch`, `giro_roll` para el giroscopio cuando el nombre lo indica, o `giro_1`, `giro_2`, `giro_3` según su orden. La tabla de la sección 2 del notebook muestra la equivalencia exacta.

## `datos/procesados/labwalks_modelo.csv` (entrada de los modelos)

Contiene `registro`, `grupo`, `cae` y solo las variables de marcha elegidas para los modelos de la sub-etapa 1.3. Se obtiene recorriendo las variables en orden y descartando las que tienen una correlación de Spearman mayor a 0,9 (en valor absoluto) con alguna ya elegida. La lista exacta de variables elegidas y descartadas aparece en la sección 6.6 del notebook.

## Limpieza aplicada

1. Exclusión de registros con datos faltantes, un canal plano, menos de 20 s útiles o una detección de pasos no confiable (más del 20 % de los intervalos entre pasos descartados). Los registros excluidos y el motivo quedan en `labwalks_calidad.csv`.
2. Recorte de 3 s al inicio y al final (arranque y frenado).
3. Filtro Butterworth pasa bajo de 4.º orden, 20 Hz, sin desfase (`filtfilt`).
4. Eliminación de la gravedad en el eje vertical (se resta el promedio).
5. Para detectar pasos: filtro adicional de 3 Hz y búsqueda de máximos separados por al menos 0,3 s.
6. Intervalos entre pasos fuera de 0,5–1,5 veces la mediana de cada persona: se descartan antes de calcular las variables temporales.

## Otros archivos

| Archivo | Contenido |
|---|---|
| `labwalks_calidad.csv` | Control de calidad por registro: frecuencia, NaN, canal plano, saturación, media del eje vertical, duración útil, exclusión y motivo |
| `labwalks_comparacion_grupos.csv` | Mediana por grupo, tamaño del efecto (r) y valor p (Mann-Whitney, corregido por Holm) de cada variable |
| `senales_limpias/<registro>.csv` | Señal recortada y filtrada: `tiempo_s` y los 6 canales con sus nombres del notebook |
| `casia_inventario.csv` | Una fila por secuencia de CASIA: base, persona, condición, número, ángulo y cuadros |
| `casia_calidad_muestra.csv` | En una muestra: tamaño de imagen, área media de la silueta y cuadros vacíos |
| `casia_periodo_marcha.csv` | En vistas laterales: período de paso (cuadros) y cadencia estimada desde la silueta |

### Códigos de CASIA

| Código | Significado | Base |
|---|---|---|
| `nm` | Marcha normal | A (todas) y B |
| `bg` | Con bolso o mochila | B |
| `cl` | Con abrigo | B |
| `fn`, `fb`, `fq`, `fs` | De noche, infrarrojo: normal, con bolso, rápido, lento | C |
| ángulo `000`–`180` | Ángulo de la cámara; lateral = `000` en A, `090` en B; en C es siempre lateral (`lat`) | |
