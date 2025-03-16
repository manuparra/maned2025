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
