# 📌 Instalación y Configuración de Apache Spark para R

---

## 🔹 **1. Instalación de Spark en R**
Para instalar Spark en R, abre **RStudio o Jupyter Lab** y ejecuta:

```r
# Instalar la librería sparklyr
install.packages("sparklyr")

# Instalar Apache Spark en local (descarga automática)
sparklyr::spark_install()
```

## Installación en Colab

```
install.packages("sparklyr")
library(sparklyr)
spark_available_versions()
park_install(version = "3.1")
sc <- spark_connect(master = "local", version = "3.1")
```


### **📌 Notas sobre la instalación**
- `sparklyr` permite la conexión entre **R y Apache Spark**.
- `spark_install()` descarga e instala Spark automáticamente en el sistema.

Si prefieres instalar **una versión específica** de Spark, usa:

```r
sparklyr::spark_install(version = "3.2.0")
```

---

## 🔹 **2. Verificación de la instalación**
Para asegurarte de que Spark está correctamente instalado, ejecuta:

```r
library(sparklyr)
spark_available_versions()
```

Para comprobar que Spark se inicia correctamente, conecta R con Spark:

```r
sc <- spark_connect(master = "local")
sc
```

Si la conexión es exitosa, verás información sobre el servidor Spark.

---

## 🔹 **3. Configuración avanzada de Spark**
Si necesitas asignar más recursos a Spark, puedes personalizar la inicialización:

```r
sc <- spark_connect(master = "local", 
                    config = list(spark.memory.fraction = 0.8, 
                                  spark.executor.memory = "4G"))
```

O si estás trabajando con **Spark en un clúster**, cambia el `master`:

```r
sc <- spark_connect(master = "yarn")  # Para entornos Hadoop/YARN
sc <- spark_connect(master = "spark://ip-servidor:7077")  # Para Spark independiente
```

Aquí tienes algunos **ejemplos prácticos** de uso de **Big Data en nubes públicas** utilizando las herramientas mencionadas anteriormente. Estos ejemplos están diseñados para ilustrar flujos de trabajo comunes en entornos reales.

---

📌 Ejemplos de Uso de sparklyr en Nubes Públicas

### 🔹 **3.1. AWS - Procesamiento de Datos Masivos con Amazon EMR**
**Caso de uso:** Procesar grandes volúmenes de logs de servidores web para obtener patrones de tráfico.

#### 🔹 **Flujo de trabajo en AWS**
1. **Almacenar los logs en Amazon S3**.
2. Crear un clúster de **Amazon EMR** con Apache Spark.
3. Conectar R a Spark mediante `sparklyr`.
4. Ejecutar consultas para identificar las páginas web más visitadas.

#### **Código en R usando `sparklyr` en AWS EMR**
```r
library(sparklyr)
library(dplyr)

# Conectar a Spark en EMR
sc <- spark_connect(master = "yarn")

# Leer datos directamente desde S3
logs <- spark_read_csv(sc, name = "logs", path = "s3://mi-bucket/logs.csv")

# Procesamiento de datos en Spark
logs %>%
  filter(status == 200) %>%
  group_by(url) %>%
  summarise(visitas = n()) %>%
  arrange(desc(visitas)) %>%
  collect()  # Devuelve el resultado a R
```

---

### 🔹 **3.2. Google Cloud - Análisis de Datos con BigQuery**
**Caso de uso:** Realizar análisis exploratorio de un conjunto de datos público con millones de registros.

#### 🔹 **Flujo de trabajo en Google Cloud**
1. Crear un proyecto en **Google Cloud** y habilitar BigQuery.
2. Subir los datos a **Google Cloud Storage** o utilizar un dataset público.
3. Utilizar la librería `bigrquery` en R para conectarse a BigQuery.

#### **Código en R usando `bigrquery`**
```r
library(bigrquery)
library(dplyr)

# Definir parámetros de conexión
project_id <- "mi-proyecto"
sql <- "SELECT year, AVG(magnitude) as avg_magnitude
        FROM `bigquery-public-data.noaa_historic_severe_storms.tornadoes`
        GROUP BY year
        ORDER BY year"

# Ejecutar la consulta
resultados <- bq_project_query(project_id, sql)

# Mostrar los resultados
resultados %>%
  bq_table_download() %>%
  head(10)
```

---

### 🔹 **3.3. Microsoft Azure - Machine Learning Distribuido con Azure Synapse Analytics**
**Caso de uso:** Entrenar un modelo de predicción del valor de viviendas usando un dataset de grandes dimensiones.

#### 🔹 **Flujo de trabajo en Azure**
1. Subir el dataset a **Azure Data Lake Storage**.
2. Crear un clúster en **Azure Synapse Analytics**.
3. Conectar R a Synapse utilizando `DBI`.

#### **Código en R usando `DBI` y `sparklyr`**
```r
library(DBI)
library(sparklyr)

# Conectar a Azure Synapse Analytics
sc <- spark_connect(master = "synapse://mi-synapse-workspace")

# Cargar datos desde Azure Data Lake Storage
viviendas <- spark_read_csv(sc, "viviendas", "abfss://datos@mi-datalake.dfs.core.windows.net/viviendas.csv")

# Entrenamiento de un modelo de regresión logística en Spark
modelo <- viviendas %>%
  ml_logistic_regression(response = "precio", features = c("habitaciones", "metros", "ubicacion"))

# Evaluar el modelo
ml_evaluate(modelo)
```

---

### 🔹 **3.4. Databricks - Análisis de Series Temporales en Apache Spark**
**Caso de uso:** Predecir la demanda de energía eléctrica a partir de datos históricos.

#### 🔹 **Flujo de trabajo en Databricks**
1. Crear un workspace en **Databricks** (AWS, Azure o Google Cloud).
2. Importar el dataset desde un bucket en S3 o Azure Blob Storage.
3. Utilizar Spark ML para el análisis de series temporales.

#### **Código en R usando `sparklyr` en Databricks**
```r
library(sparklyr)
library(dplyr)

# Conectar a Spark en Databricks
sc <- spark_connect(method = "databricks")

# Cargar datos de consumo energético
energia <- spark_read_csv(sc, "energia", "/mnt/datos/energia.csv")

# Modelo ARIMA para series temporales
modelo_arima <- energia %>%
  ml_arima(x = "fecha", y = "consumo")

# Previsión para los próximos 30 días
forecast <- predict(modelo_arima, newdata = energia)
forecast
```

---

### 🔹 **3.5. Snowflake - Análisis de Datos en SQL Escalable**
**Caso de uso:** Analizar datos de ventas para encontrar patrones de compra en tiempo real.

#### 🔹 **Flujo de trabajo en Snowflake**
1. Cargar los datos en **Snowflake** desde S3 o Azure Blob Storage.
2. Conectarse a Snowflake usando `DBI`.
3. Ejecutar consultas en SQL directamente desde R.

#### **Código en R usando `DBI` para Snowflake**
```r
library(DBI)

# Conectar a Snowflake
con <- dbConnect(odbc::odbc(), 
                 Driver = "SnowflakeDSIIDriver",
                 Server = "mi-servidor.snowflakecomputing.com",
                 Database = "ventas",
                 UID = "mi_usuario",
                 PWD = "mi_contraseña")

# Consulta SQL en Snowflake
query <- "SELECT producto, SUM(ventas) as total_ventas
          FROM ventas
          WHERE fecha >= '2024-01-01'
          GROUP BY producto
          ORDER BY total_ventas DESC"

# Obtener los resultados
resultados <- dbGetQuery(con, query)
head(resultados)
```

---

## 🔹 **4. Uso básico de Spark con R**
Una vez conectado, puedes cargar y manipular datos directamente en Spark:

```r
# Cargar datos en Spark desde un CSV
datos <- spark_read_csv(sc, name = "datos_spark", path = "ruta/dataset.csv", infer_schema = TRUE)

# Ver las primeras filas
head(datos)

# Transformaciones en Spark usando dplyr
library(dplyr)

datos %>%
  group_by(categoria) %>%
  summarise(promedio = mean(valor)) %>%
  collect()  # 'collect()' trae los resultados a R
```

---

## 🔹 **5. Apagar la sesión de Spark y desinsta**
Cuando termines de usar Spark, cierra la conexión con:

```r
spark_disconnect(sc)
```
Con esto se liberarán todos los recursos de memoria, disco, etc. asociados a la session de spark.

Para desinstalar spark completamente usa lo siguiente:

```r
spark_uninstall(version = "3.2.0")
```
