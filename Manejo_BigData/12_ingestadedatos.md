# 🔵 **Tema 6.5.2 – Carga e Ingesta de Datos en Big Data**

La **ingesta de datos** es el primer paso clave en el flujo de trabajo de Big Data. Este proceso consiste en capturar, transformar y almacenar los datos provenientes de diversas fuentes para que puedan ser utilizados posteriormente en análisis o modelos predictivos.

En esta sección aprenderemos a:

✅ Comprender los conceptos de ETL y ELT  
✅ Realizar la carga de datos desde distintos formatos como **CSV** y **Parquet**  
✅ Desarrollar un flujo de trabajo **ETL** (Extract, Transform, Load)  
✅ Desarrollar un flujo de trabajo **ELT** (Extract, Load, Transform)  
✅ Aplicar ejemplos prácticos con el dataset de **NYC Taxi Trips**  

---

## 🔹 **1. Conceptos clave: ETL vs ELT**

### 📂 **¿Qué es ETL (Extract, Transform, Load)?**

El flujo **ETL** consiste en:

1. **Extract (Extracción):** Se obtienen datos desde múltiples fuentes (bases de datos, APIs, logs, etc.).
2. **Transform (Transformación):** Se limpian, filtran o enriquecen los datos antes de almacenarlos.
3. **Load (Carga):** Los datos transformados se almacenan en un almacén de datos (Data Lake o Data Warehouse).

✅ **Ventaja:** Los datos llegan limpios y organizados.  
❌ **Inconveniente:** Si los datos crecen en volumen, la transformación previa puede volverse lenta.

---

### 📂 **¿Qué es ELT (Extract, Load, Transform)?**

El flujo **ELT** es una variante moderna en la que:

1. **Extract (Extracción):** Los datos se extraen desde diversas fuentes.  
2. **Load (Carga):** Los datos se cargan directamente en el sistema de almacenamiento distribuido (HDFS, S3, GCS, etc.).  
3. **Transform (Transformación):** La limpieza, normalización y enriquecimiento se realiza dentro del sistema de almacenamiento.

✅ **Ventaja:** Escalable y eficiente para grandes volúmenes.  
❌ **Inconveniente:** Los datos llegan "en bruto" y requieren más pasos posteriores.

---

### 🔎 **¿Cuándo usar cada modelo?**

- **ETL**: Cuando se trabaja con datos más pequeños o se requiere que los datos estén limpios antes de su almacenamiento.  
- **ELT**: Más eficiente para grandes volúmenes de datos ya que se aprovecha la escalabilidad del entorno distribuido.

---

## 🔹 **2. Ejemplo de carga de datos en Spark con `sparklyr`**

El dataset de taxis de Nueva York está disponible tanto en formato **Parquet** (eficiente para Big Data) como en **CSV** (más tradicional).

---

### 🟠 **Carga de datos en formato Parquet** (Recomendado para Big Data)
El formato **Parquet** es altamente eficiente porque almacena los datos de forma columnar y comprimida, lo que mejora el rendimiento en consultas analíticas.

```r
# Librerías necesarias
library(sparklyr)
library(dplyr)

# Conexión a Spark
sc <- spark_connect(master = "local")

# Carga del dataset en formato Parquet
taxi_nyc <- spark_read_parquet(
  sc, 
  name = "taxi_nyc",
  path = "https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.parquet"
)

# Ver primeras filas del dataset
taxi_nyc %>% head(10) %>% collect()
```

---

### 🟠 **Carga de datos en formato CSV** (Común en entornos empresariales)
El formato **CSV** es ampliamente utilizado, pero menos eficiente que Parquet para Big Data debido a la falta de compresión e indexación.

```r
# Carga del dataset en formato CSV
taxi_nyc_csv <- spark_read_csv(
  sc,
  name = "taxi_nyc_csv",
  path = "https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.csv",
  infer_schema = TRUE,
  header = TRUE
)

# Ver primeras filas del dataset CSV
taxi_nyc_csv %>% head(10) %>% collect()
```

---

## 🔹 **3. Flujo ETL con el dataset NYC Taxi**

En este flujo, se extraerán datos en formato CSV, se limpiarán y transformarán dentro de Spark, y finalmente se almacenarán en formato Parquet para su posterior análisis.

### 🔹 **Paso 1: Extracción**
Se extraen los datos desde un archivo CSV.

```r
# Extracción de datos (EXTRACT)
taxi_nyc_csv <- spark_read_csv(
  sc,
  name = "taxi_nyc_csv",
  path = "https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.csv",
  infer_schema = TRUE,
  header = TRUE
)
```

---

### 🔹 **Paso 2: Transformación**
- Eliminación de registros con tarifas negativas
- Creación de una nueva columna "tarifa_total" que sume tarifa base + propina

```r
# Transformación de datos (TRANSFORM)
taxi_nyc_clean <- taxi_nyc_csv %>%
  filter(fare_amount > 0) %>%
  mutate(total_fare = fare_amount + tip_amount)
```

---

### 🔹 **Paso 3: Carga**
Se almacenan los datos limpios en formato Parquet para mejorar el rendimiento en consultas futuras.

```r
# Carga de datos (LOAD)
spark_write_parquet(taxi_nyc_clean, path = "hdfs://ruta/nyc_taxi_clean.parquet")
```

---

## 🔹 **4. Flujo ELT con el dataset NYC Taxi**

En este flujo, se cargan primero los datos en bruto dentro de Spark (en formato Parquet) y posteriormente se realiza la transformación directamente en Spark.

### 🔹 **Paso 1: Extracción y Carga**
Carga directa de datos sin limpieza previa.

```r
# Extracción y Carga simultánea (EXTRACT & LOAD)
taxi_nyc_raw <- spark_read_parquet(
  sc, 
  name = "taxi_nyc_raw",
  path = "https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.parquet"
)
```

---

### 🔹 **Paso 2: Transformación**
Transformación dentro del entorno distribuido Spark.

```r
# Transformación interna en Spark
taxi_nyc_clean_elt <- taxi_nyc_raw %>%
  filter(fare_amount > 0) %>%
  mutate(total_fare = fare_amount + tip_amount)

# Carga final del resultado en Parquet optimizado
spark_write_parquet(taxi_nyc_clean_elt, path = "hdfs://ruta/nyc_taxi_clean_elt.parquet")
```

---
# 🔎 **Modos de Inferir el Esquema (Schema) de un CSV en Apache Spark y su Eficiencia**

Cuando se trabaja con datos en formato **CSV** en Apache Spark, uno de los pasos más importantes es la **definición del esquema** (schema). Un esquema describe la estructura de los datos, es decir, el nombre de las columnas, sus tipos de datos (entero, cadena, fecha, etc.) y las posibles restricciones.  

En Apache Spark existen diversas formas de inferir o definir el esquema de un CSV, cada una con sus ventajas y desventajas en términos de **eficiencia**, **precisión** y **rendimiento**.

---

## 🔹 **¿Por qué es importante el esquema en Spark?**
Dado que Spark es una plataforma **distribuida**, los datos se procesan en paralelo en múltiples nodos. Inferir el esquema correctamente es fundamental para:

✅ Evitar errores en el tratamiento de datos.  
✅ Mejorar el rendimiento, ya que Spark puede optimizar las consultas cuando conoce los tipos de datos.  
✅ Reducir el tiempo de carga al evitar que Spark tenga que inspeccionar cada fila del CSV.  

---

## 🔹 **Modos de Inferir el Esquema en Apache Spark**

Existen principalmente tres métodos para inferir o definir el esquema de un CSV en Spark:

1. **Inferencia Automática del Esquema**  
2. **Especificación Manual del Esquema**  
3. **Lectura Parcial del Dataset (Sampling)**  

---

## 📌 **1. Inferencia Automática del Esquema**
La inferencia automática es el método más sencillo, pero puede ser menos eficiente en grandes volúmenes de datos.

### 🔎 **¿Cómo funciona?**
Cuando se utiliza la opción **`infer_schema = TRUE`** (en `spark_read_csv()` de `sparklyr`), Spark inspecciona una parte del archivo para determinar el tipo de datos de cada columna.

```r
library(sparklyr)
library(dplyr)

# Conectar a Spark
sc <- spark_connect(master = "local")

# Inferencia automática del esquema
taxi_nyc <- spark_read_csv(
  sc,
  name = "taxi_nyc",
  path = "hdfs://ruta/dataset.csv",
  infer_schema = TRUE,
  header = TRUE
)

# Visualización del esquema inferido
sdf_schema(taxi_nyc)
```

### ✅ **Ventajas**  
✔️ Rápido y sencillo para conjuntos de datos pequeños o medianos.  
✔️ No requiere que el usuario defina manualmente el esquema.  

### ❗ **Desventajas**  
❌ Puede ser **lento** en conjuntos de datos grandes porque Spark analiza múltiples filas para determinar los tipos de datos.  
❌ El resultado puede ser **impreciso** si los datos contienen valores atípicos o errores.  

> ⚠️ **Recomendación:** En archivos muy grandes, este método no es óptimo, ya que Spark debe escanear el dataset para inferir correctamente los tipos de datos.

---

## 📌 **2. Especificación Manual del Esquema**
La definición manual del esquema es la opción **más eficiente** y precisa, especialmente cuando se conoce de antemano la estructura del CSV.

### 🔎 **¿Cómo funciona?**
Se utiliza el parámetro **`columns`** en `spark_read_csv()` para definir el esquema.

### 🔎 **Ejemplo de definición manual del esquema**
```r
# Definición manual del esquema
schema_manual <- list(
  VendorID = "integer",
  pickup_datetime = "timestamp",
  dropoff_datetime = "timestamp",
  passenger_count = "integer",
  trip_distance = "double",
  fare_amount = "double",
  tip_amount = "double",
  total_amount = "double"
)

# Carga del CSV con el esquema manual
taxi_nyc <- spark_read_csv(
  sc,
  name = "taxi_nyc",
  path = "hdfs://ruta/dataset.csv",
  columns = schema_manual,
  header = TRUE
)

# Visualización del esquema definido
sdf_schema(taxi_nyc)
```

### ✅ **Ventajas**  
✔️ **Mayor eficiencia:** Spark no necesita escanear el dataset, ya que el esquema está definido.  
✔️ Garantiza que los tipos de datos sean correctos.  
✔️ Reduce el riesgo de errores de inferencia.  

### ❗ **Desventajas**  
❌ Requiere que el usuario conozca previamente la estructura del dataset.  
❌ Si el CSV tiene columnas adicionales o nombres inconsistentes, pueden generarse errores.

> 🔎 **Consejo:** Este método es ideal para datasets muy grandes o cuando el esquema es conocido de antemano.

---

## 📌 **3. Inferencia del Esquema mediante Muestreo (Sampling)**
La técnica de **muestreo** permite que Spark examine solo una parte del CSV para inferir el esquema, en lugar de inspeccionar todas las filas.

### 🔎 **¿Cómo funciona?**
Con el parámetro **`samplingRatio`**, puedes especificar el porcentaje de datos que Spark inspeccionará para inferir el esquema.

### 🔎 **Ejemplo de inferencia mediante muestreo**
```r
# Inferencia del esquema con muestreo del 10%
taxi_nyc <- spark_read_csv(
  sc,
  name = "taxi_nyc",
  path = "hdfs://ruta/dataset.csv",
  infer_schema = TRUE,
  header = TRUE,
  options = list(samplingRatio = 0.1)  # Muestreo del 10%
)
```

### ✅ **Ventajas**  
✔️ Más rápido que la inferencia automática completa.  
✔️ Buena opción para datasets muy grandes.  

### ❗ **Desventajas**  
❌ Puede generar un esquema incompleto si el muestreo no es representativo.  
❌ Si hay datos atípicos o errores en filas fuera del muestreo, el esquema puede ser incorrecto.  

> 🔎 **Recomendación:** Ajusta el valor de `samplingRatio` en función del tamaño del dataset (valores recomendados entre **0.1** y **0.3**).

---

## 🔹 **Comparación de Métodos para Inferir el Esquema**

| Método                    | Eficiencia | Precisión | Escenarios Recomendados |
|---------------------------|-------------|------------|---------------------------|
| **Inferencia Automática**   | 🟠 Medio     | 🟠 Medio    | Datasets pequeños o medianos |
| **Especificación Manual**   | 🟢 Alta      | 🟢 Alta     | Datasets grandes o esquemas conocidos |
| **Muestreo (`samplingRatio`)** | 🟢 Alta | 🟠 Medio    | Datasets muy grandes con estructura desconocida |

---

## 🔹 **Mejores Prácticas para Manejar Esquemas en Spark**

✅ Si el dataset es **grande**, utiliza **definición manual del esquema** para maximizar el rendimiento.  
✅ Si el dataset es **desconocido** o varía con el tiempo, usa el **muestreo** para acelerar la inferencia.  
✅ Si el dataset es **pequeño o mediano**, la **inferencia automática** es la opción más sencilla.  
✅ Utiliza siempre la función **`sdf_schema()`** después de cargar el dataset para verificar que el esquema sea el esperado.  

---

## 🔹 **Ejemplo Final: Combinación de Métodos para Máxima Eficiencia**
La mejor estrategia para datasets masivos consiste en:

1. Utilizar la **inferencia mediante muestreo** en la fase exploratoria para determinar el esquema correcto.  
2. Luego, definir manualmente el esquema para mejorar la eficiencia en el procesamiento final.  

**Ejemplo combinado:**
```r
# 1. Usar sampling para identificar el esquema correcto
taxi_nyc_sample <- spark_read_csv(
  sc, 
  name = "taxi_nyc_sample", 
  path = "hdfs://ruta/dataset.csv",
  infer_schema = TRUE,
  options = list(samplingRatio = 0.1)
)

# 2. Extraer el esquema identificado
schema_identificado <- sdf_schema(taxi_nyc_sample)

# 3. Aplicar el esquema manualmente para mejorar la eficiencia
taxi_nyc <- spark_read_csv(
  sc, 
  name = "taxi_nyc", 
  path = "hdfs://ruta/dataset.csv", 
  columns = schema_identificado
)
```

---


## 🔹 **5. Ejercicios prácticos**

### 🟩 **Ejercicio 1 – Análisis de tarifas limpias usando ETL**
- Carga el dataset de taxis en formato **CSV**.
- Filtra los registros con tarifas negativas o nulas.
- Calcula la tarifa media y la tarifa máxima.
- Almacena el resultado en formato **Parquet**.

```r

```

---

### 🟩 **Ejercicio 2 – Análisis de viajes por hora usando ELT**
- Carga el dataset de taxis en formato **Parquet**.
- Crea una columna que extraiga la **hora** de recogida (`pickup_datetime`).
- Calcula el total de viajes por hora.
- Almacena el resultado en formato **Parquet**.


```r

```
