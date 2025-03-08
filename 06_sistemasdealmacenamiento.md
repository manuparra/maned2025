# 📌 Sistemas de Almacenamiento para Big Data en R y Apache Spark

Sistemas de almacenamiento más populares utilizados para manejar grandes volúmenes de datos, centrándonos en la integración con **R** mediante **Apache Spark** (`sparklyr`). Hablaremos de **HDFS**, **Amazon S3** y **Google Cloud Storage**, explicando sus modelos de distribución, ventajas y ejemplos reales de acceso a datos.

---

## 🔹 **1. Hadoop Distributed File System (HDFS)**

### 📂 **¿Qué es HDFS?**
**HDFS** es un sistema de archivos distribuido diseñado para almacenar grandes conjuntos de datos, distribuidos en varios nodos de un clúster. Se basa en la replicación de bloques para garantizar tolerancia a fallos y acceso paralelo eficiente.

### 🔸 **Modelo de Distribución**
- Los datos se dividen en bloques (generalmente de 128 MB).
- Los bloques se replican en varios nodos (por defecto 3 copias).
- El acceso a datos es paralelo y local a los nodos que los almacenan, mejorando el rendimiento.

### 🔸 **Acceso a HDFS desde R con Spark**
```r
library(sparklyr)
sc <- spark_connect(master = "yarn")

# Leer datos desde HDFS
datos <- spark_read_csv(sc, name = "datos_hdfs", path = "hdfs://cluster/user/data/sample_data.csv")

# Ejemplo real: dataset público de la ciudad de Nueva York (Taxi trips)
# Ruta de ejemplo real (pública en muchos clústeres):
ruta_hdfs <- "hdfs://cluster/public/nyc_taxi/yellow_tripdata_2023-01.csv"
taxi_nyc <- spark_read_csv(sc, name = "taxi_nyc", path = ruta_hdfs, infer_schema = TRUE)

head(taxi_nyc)
```

### 🔸 **Ventajas de HDFS**
✅ Alto rendimiento para grandes volúmenes de datos en paralelo.  
✅ Alta tolerancia a fallos mediante replicación.  
✅ Compatible nativamente con ecosistemas Hadoop/Spark.

---

## 🔹 **2. Amazon S3 (Simple Storage Service)**

### 📂 **¿Qué es Amazon S3?**
Amazon S3 es un sistema de almacenamiento basado en objetos, ampliamente utilizado para almacenar datos en la nube, accesible globalmente y escalable sin límites prácticos.

### 🔸 **Modelo de Distribución**
- Almacenamiento basado en objetos distribuidos globalmente.
- Escalabilidad automática y acceso paralelo desde múltiples clústeres.
- Integración directa con Spark y otras herramientas analíticas.

### 🔸 **Acceso a S3 desde R con Spark**
```r
library(sparklyr)
sc <- spark_connect(master = "local")

# Configurar acceso con credenciales AWS (se asume configuración previa)
# Sys.setenv(AWS_ACCESS_KEY_ID="tu_key", AWS_SECRET_ACCESS_KEY="tu_secret")

# Leer datos directamente desde Amazon S3
datos_s3 <- spark_read_parquet(sc, name = "datos_s3",
                               path = "s3a://mi-bucket/datos.parquet")

# Ejemplo real: dataset público de Landsat en AWS
ruta_s3_landsat <- "s3a://landsat-pds/c1/L8/001/003/LC08_L1TP_001003_20170428_20170510_01_T1/*MTL.txt"
landsat <- spark_read_text(sc, name = "landsat_metadata", path = ruta_s3_landsat)

head(landsat)
```

### 🔸 **Ventajas de Amazon S3**
✅ Alta escalabilidad sin mantenimiento del sistema de almacenamiento.  
✅ Integración nativa con AWS EMR y Spark.  
✅ Pago según uso, con almacenamiento altamente duradero y disponible.

---

## 🔹 **3. Google Cloud Storage (GCS)**

### 📂 **¿Qué es Google Cloud Storage?**
Google Cloud Storage es un servicio de almacenamiento de objetos escalable y seguro en la nube de Google, diseñado para grandes volúmenes de datos.

### 🔸 **Modelo de Distribución**
- Objetos almacenados en "buckets" distribuidos geográficamente.
- Alta disponibilidad y rendimiento mediante almacenamiento distribuido.
- Acceso eficiente mediante Spark y BigQuery.

### 🔸 **Acceso a Google Cloud Storage desde R con Spark**
```r
library(sparklyr)
sc <- spark_connect(master = "local")

# Autenticación previa con Google Cloud SDK o mediante archivo JSON de credenciales
# Sys.setenv(GOOGLE_APPLICATION_CREDENTIALS="ruta/credenciales.json")

# Leer datos CSV desde GCS
datos_gcs <- spark_read_csv(sc, name = "datos_gcs",
                            path = "gs://mi-bucket/data/dataset.csv")

# Ejemplo real: Dataset público de COVID-19 en GCS
ruta_covid_gcs <- "gs://covid19-open-data/v2/latest/epidemiology.csv"
covid_data <- spark_read_csv(sc, name = "covid_data", path = ruta_covid_gcs, infer_schema = TRUE)

head(covid_data)
```

### 🔸 **Ventajas de Google Cloud Storage**
✅ Fácil integración con Google BigQuery y servicios analíticos.  
✅ Muy alto rendimiento para consultas de grandes datasets desde Spark.  
✅ Seguridad avanzada y excelente gestión del ciclo de vida de los datos.

---

## 🔹 **4. Comparativa de Sistemas de Almacenamiento**

| Característica        | HDFS                          | Amazon S3                    | Google Cloud Storage   |
|-----------------------|-------------------------------|------------------------------|------------------------|
| **Tipo de Almacenamiento**  | Sistema distribuido en bloques | Basado en objetos            | Basado en objetos      |
| **Modelo de distribución**  | Replicación en nodos          | Distribución global          | Distribución global    |
| **Velocidad de acceso**     | Alta (localidad en nodos)     | Alta (acceso paralelo global)| Alta (acceso paralelo global) |
| **Integración con Spark**   | Nativa                        | Muy buena                    | Muy buena              |
| **Costos**                  | Requiere mantenimiento local   | Pago por uso (sin gestión)   | Pago por uso (sin gestión)  |
| **Ejemplos de uso real**    | Datos empresariales grandes, logs | Datos públicos (NASA, Landsat)| Datos públicos (COVID-19, NOAA)|

---

## 🔹 **5. Modelo de Distribución Local en Nodos**
- **Spark** prioriza el acceso paralelo aprovechando el principio de "data locality": los cálculos se realizan lo más cerca posible del nodo donde residen físicamente los datos.
- Los datos almacenados en sistemas distribuidos (como HDFS) permiten que Spark ejecute operaciones de forma eficiente, minimizando el movimiento de datos a través de la red.

**Ejemplo gráfico simplificado**:
```
Nodo 1      Nodo 2      Nodo 3
[datos] --> [datos] --> [datos]
  │           │           │
[Spark]     [Spark]     [Spark]
```

Cada nodo procesa simultáneamente los datos locales, aumentando la eficiencia del acceso paralelo.

---

Aquí tienes el documento ampliado con una sección adicional sobre **tipos de ficheros más utilizados en Big Data** y cómo trabajar con ellos desde **R y Apache Spark**.

---

# 📌 **Tipos de Ficheros más usados en Big Data con R y Spark**

En Big Data, la elección del formato del fichero es fundamental para optimizar el almacenamiento, acceso y procesamiento eficiente de grandes conjuntos de datos. A continuación, revisamos los formatos más comunes, sus características, ventajas y ejemplos de uso con R y Spark.

---

## 🔹 **1. CSV (Comma Separated Values)**

### 📂 **Características**
- Formato basado en texto plano.
- Alta compatibilidad y fácil manejo.
- Poco eficiente para grandes volúmenes de datos (no comprimido, sin indexado).

### 📌 **Ejemplo en R/Spark**
```r
library(sparklyr)
sc <- spark_connect(master = "local")

datos_csv <- spark_read_csv(sc, name = "datos_csv", path = "hdfs://ruta/datos.csv")

# Ejemplo público: NYC Taxi Dataset (CSV)
ruta_nyc <- "s3a://nyc-tlc/trip data/yellow_tripdata_2023-01.csv"
nyc_taxi <- spark_read_csv(sc, "nyc_taxi", ruta_nyc, infer_schema = TRUE)

head(nyc_taxi)
```

---

## 🔹 **2. Parquet**

### 📂 **Características**
- Formato binario columnar altamente eficiente.
- Compresión efectiva y soporte para particionado.
- Ideal para análisis rápidos sobre columnas específicas.

### 📌 **Ejemplo en R/Spark**
```r
library(sparklyr)
sc <- spark_connect(master = "local")

datos_parquet <- spark_read_parquet(sc, name = "datos_parquet", path = "s3a://mi-bucket/datos.parquet")

# Ejemplo público: Amazon Reviews (Parquet)
ruta_reviews <- "s3a://amazon-reviews-pds/parquet/product_category=Electronics/*.parquet"
reviews <- spark_read_parquet(sc, "amazon_reviews", ruta_reviews)

head(reviews)
```

---

## 🔹 **3. ORC (Optimized Row Columnar)**

### 📂 **Características**
- Similar a Parquet (almacenamiento columnar comprimido).
- Mejor rendimiento en consultas complejas de Hive.
- Muy usado en ecosistemas Hadoop.

### 📌 **Ejemplo en R/Spark**
```r
library(sparklyr)
sc <- spark_connect(master = "yarn")

datos_orc <- spark_read_orc(sc, name = "datos_orc", path = "hdfs://ruta/datos.orc")

# Ejemplo real (datos de ventas almacenados en Hadoop)
ruta_ventas <- "hdfs://cluster/user/data/ventas.orc"
ventas <- spark_read_orc(sc, "ventas", ruta_ventas)

head(ventas)
```

---

## 🔹 **4. Avro**

### 📂 **Características**
- Formato basado en filas con esquema definido.
- Muy eficiente en sistemas de transmisión (Kafka).
- Compatible con esquemas evolutivos.

### 📌 **Ejemplo en R/Spark**
```r
library(sparklyr)
sc <- spark_connect(master = "local")

# Spark nativamente puede necesitar paquetes adicionales para leer Avro
datos_avro <- spark_read_avro(sc, name = "datos_avro", path = "s3a://ruta/datos.avro")

# Ejemplo público: datos climáticos en formato Avro
ruta_clima <- "s3a://noaa-avro-pds/datos-clima/*.avro"
clima <- spark_read_avro(sc, "clima", ruta_clima)

head(clima)
```

---

## 🔹 **5. JSON**

### 📂 **Características**
- Formato texto, estructurado, ligero y fácil de leer.
- Menos eficiente en Big Data, aunque común en datos semi-estructurados.

### 📌 **Ejemplo en R/Spark**
```r
library(sparklyr)
sc <- spark_connect(master = "local")

datos_json <- spark_read_json(sc, name = "datos_json", path = "gs://bucket/datos.json")

# Ejemplo real: datos abiertos de Twitter (JSON)
ruta_twitter <- "gs://public-datasets/twitter_sample/*.json"
tweets <- spark_read_json(sc, "tweets", ruta_twitter)

head(tweets)
```

---

## 🔹 **6. Feather (Apache Arrow)**

### 📂 **Características**
- Formato ligero diseñado para intercambio rápido entre R y Python.
- Alto rendimiento para análisis interactivos.

### 📌 **Ejemplo en R**
```r
library(arrow)
library(dplyr)

datos_feather <- read_feather("datos.feather")

head(datos_feather)
```

(No disponible directamente en Spark, pero útil en entornos híbridos R/Python)

---

## 🔹 **7. Delta Lake**

### 📂 **Características**
- Formato basado en Parquet con transacciones ACID.
- Soporte para versionado de datos.
- Integrado profundamente con Spark (Databricks).

### 📌 **Ejemplo en R/Spark**
```r
library(sparklyr)
sc <- spark_connect(master = "local", packages = "io.delta:delta-core_2.12:2.4.0")

datos_delta <- spark_read_delta(sc, name = "datos_delta", path = "s3a://bucket/delta-table/")

head(datos_delta)
```

---

## 🔹 **Comparativa de Formatos para Big Data**

| Formato | Compresión | Rendimiento Lectura | Uso típico | Integración Spark |
|---------|------------|---------------------|------------|-------------------|
| CSV     | ❌ Baja    | ⚠️ Medio-Bajo        | Datos abiertos, fácil uso | ✅ Completa |
| Parquet | ✅ Alta    | 🚀 Alto (columnar)   | Analítica eficiente | ✅ Completa |
| ORC     | ✅ Alta    | 🚀 Alto (columnar)   | Hadoop/Hive  | ✅ Completa |
| Avro    | ✅ Alta    | ⚠️ Medio-Alto        | Streaming, intercambio estructurado | ✅ Completa |
| JSON    | ⚠️ Medio   | ⚠️ Medio-Bajo        | Semi-estructurados, APIs | ✅ Completa |
| Feather | ⚠️ Medio-Alto | 🚀 Muy Alto       | Análisis rápido (R-Python) | ❌ Indirecta |
| Delta Lake | ✅ Alta | 🚀 Muy Alto (versionado)| Data lakes avanzados | ✅ Completa |

