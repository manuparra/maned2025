# 🔥 **Sección 6.7: Machine Learning con Big Data y `sparklyr`**


## 📚 **6.7.1. Introducción al Machine Learning en Spark con `sparklyr`**
✅ Conceptos clave de Machine Learning en Spark.  
✅ Arquitectura de **MLlib** (Librería de Machine Learning de Spark).  
✅ Ventajas de usar Spark para el entrenamiento de modelos sobre grandes volúmenes de datos.  
✅ Comparación entre el flujo de trabajo tradicional en R y el flujo de trabajo en Spark.  
✅ Integración de Spark con librerías avanzadas como **`mlflow`** para el seguimiento de experimentos.

La combinación de **`sparklyr`** y **Apache Spark** proporciona un entorno escalable y eficiente para realizar **Machine Learning (ML)** sobre grandes volúmenes de datos. En esta sección, exploraremos conceptos clave, la arquitectura de Spark para ML, sus ventajas y cómo implementar un flujo de trabajo eficiente.

---

## 🔹 **Conceptos clave de Machine Learning en Spark**

### 🚀 **¿Por qué usar Spark para Machine Learning?**
Cuando trabajamos con grandes volúmenes de datos, los algoritmos tradicionales de Machine Learning en R (como `lm()`, `randomForest()` o `xgboost()`) pueden no ser viables debido a:

❗ **Limitaciones de memoria:** Al cargar grandes conjuntos de datos en la memoria local.  
❗ **Baja escalabilidad:** Los algoritmos tradicionales no están diseñados para escalar horizontalmente en múltiples nodos.  

🔹 **Apache Spark**, mediante su componente **MLlib**, permite entrenar modelos predictivos de forma distribuida, aprovechando la arquitectura paralela de Spark.

---

## 🔹 **Arquitectura de `MLlib` en Spark**
**MLlib** es la librería nativa de Machine Learning en Apache Spark. Está diseñada para trabajar con grandes volúmenes de datos de forma distribuida.

🔹 **Componentes Clave de `MLlib`:**  
✅ **`ml_pipeline()`** → Permite construir flujos de trabajo completos de Machine Learning.  
✅ **`ml_regression()`** → Modelos de regresión (lineal, logística, etc.).  
✅ **`ml_classification()`** → Modelos de clasificación como Árboles de Decisión o Random Forest.  
✅ **`ml_clustering()`** → Algoritmos de clustering como **K-Means**.  
✅ **`ml_recommendation()`** → Sistemas de recomendación basados en el algoritmo **ALS** (Alternating Least Squares).  
✅ **`ml_evaluate()`** → Herramienta para evaluar el rendimiento de los modelos.

---

## 🔹 **Ventajas de Usar Spark para Machine Learning**

✅ **Escalabilidad:** Spark puede manejar grandes volúmenes de datos gracias a su arquitectura distribuida.  
✅ **Eficiencia:** Procesamiento optimizado al mantener los datos en memoria (**in-memory computing**).  
✅ **Flexibilidad:** Compatible con múltiples lenguajes de programación, incluido R.  
✅ **Integración Nativa:** Se conecta fácilmente con entornos de almacenamiento distribuido como **HDFS**, **S3**, y **Azure Blob Storage**.  
✅ **Automatización con Pipelines:** Permite encadenar múltiples etapas del flujo de trabajo de ML en una única estructura organizada.  

---

## 🔹 **Comparación entre el Flujo de Trabajo Tradicional en R vs Spark**

| Aspecto                 | Flujo Tradicional en R          | Flujo en Spark con `sparklyr` |
|--------------------------|----------------------------------|------------------------------|
| **Carga de Datos**          | `read.csv()` o `read_parquet()` (lectura en memoria local). | `spark_read_csv()` o `spark_read_parquet()` (lectura distribuida). |
| **Limpieza de Datos**        | Operaciones con `dplyr` o `data.table`. | Operaciones con `dplyr` sobre Spark DataFrames. |
| **Entrenamiento del Modelo** | Funciones tradicionales (`lm()`, `randomForest()`, etc.). | Algoritmos escalables de `ml_*()` en Spark. |
| **Evaluación del Modelo**    | `caret::confusionMatrix()` o `yardstick`. | `ml_evaluate()` para evaluar modelos directamente en Spark. |
| **Despliegue del Modelo**    | Guardar el modelo como `.RDS` o `.h5`. | `ml_save()` para almacenar el modelo directamente en el clúster. |

---

## 🔹 **Integración de Spark con `mlflow` para el Seguimiento de Experimentos**
**MLflow** es una potente herramienta que facilita el seguimiento, la comparación y la organización de experimentos de Machine Learning en entornos distribuidos.

🔹 **Características principales de `mlflow`:**  
✅ Permite registrar métricas, parámetros y artefactos del modelo.  
✅ Facilita la comparación de diferentes versiones de un mismo modelo.  
✅ Es compatible con Spark, permitiendo la gestión de experimentos de ML directamente en `sparklyr`.

---

## 🔹 **Ejemplo Práctico: Introducción al Machine Learning con el Dataset de NYC Yellow Cab**

En este ejemplo, implementaremos un flujo básico de Machine Learning utilizando el dataset **NYC Yellow Cab** en formato Parquet.

---

### 📋 **Paso 1: Cargar el Dataset en Spark**
El dataset contiene información sobre viajes de taxis en Nueva York, incluyendo columnas como:

- **`pickup_datetime`** → Fecha y hora del inicio del viaje.  
- **`trip_distance`** → Distancia del viaje en millas.  
- **`fare_amount`** → Tarifa total pagada por el viaje.  
- **`passenger_count`** → Número de pasajeros en el vehículo.  

**Código en R para cargar el dataset**
```r
library(sparklyr)
library(dplyr)

# Conectar a Spark
sc <- spark_connect(master = "local[*]")

# Cargar el dataset desde Parquet
taxis_nyc <- spark_read_parquet(
  sc, 
  name = "taxis_nyc", 
  path = "yellow_tripdata_2023-01.parquet"
)

# Visualizar el esquema del dataset
sdf_schema(taxis_nyc)
```

---

### 📋 **Paso 2: Exploración y Limpieza de Datos**
- Filtraremos viajes inválidos (por ejemplo, con distancia negativa o tarifas cero).  
- Removeremos valores atípicos en las columnas clave.  

**Código en R**
```r
# Filtrado básico para limpiar datos incorrectos
taxis_nyc <- taxis_nyc %>%
  filter(trip_distance > 0, fare_amount > 0)

# Verificar la cantidad de registros tras limpieza
taxis_nyc %>% sdf_nrow()
```

---

### 📋 **Paso 3: División del Dataset en Entrenamiento y Prueba**
Usaremos la función **`sdf_partition()`** para dividir el dataset.

**Código en R**
```r
# División del dataset en entrenamiento (80%) y prueba (20%)
particiones <- taxis_nyc %>%
  sdf_partition(training = 0.8, testing = 0.2, seed = 1234)

# Asignación de subconjuntos
taxis_train <- particiones$training
taxis_test <- particiones$testing
```

---

### 📋 **Paso 4: Entrenamiento de un Modelo de Regresión Lineal**
Vamos a predecir el valor de la tarifa (`fare_amount`) en función de la distancia (`trip_distance`) y el número de pasajeros (`passenger_count`).

**Código en R**
```r
modelo_regresion <- taxis_train %>%
  ml_linear_regression(fare_amount ~ trip_distance + passenger_count)

# Evaluar el modelo en el conjunto de prueba
predicciones <- ml_predict(modelo_regresion, taxis_test)

# Visualizar predicciones
predicciones %>%
  select(fare_amount, prediction) %>%
  collect() %>%
  head(10)
```

---

### 📋 **Paso 5: Evaluación del Modelo**
Evaluaremos el rendimiento utilizando el **RMSE** (Error Cuadrático Medio) y el **R²**.

**Código en R**
```r
# Evaluación del modelo
ml_evaluate(modelo_regresion, taxis_test)
```

---

### 📋 **Paso 6: Guardar el Modelo para su Uso en Producción**
El modelo se puede guardar directamente en el sistema de archivos o en un entorno cloud.

**Código en R**
```r
ml_save(modelo_regresion, "modelo_regresion_spark")
```

---

## 📚 **6.7.2. Preparación de los Datos para Machine Learning en Spark**
✅ Ingeniería de características (Feature Engineering) en Spark.  

La **Ingeniería de Características** (Feature Engineering) es una fase crítica en el flujo de trabajo de **Machine Learning**, ya que influye directamente en el rendimiento del modelo. Consiste en crear, transformar o seleccionar variables (features) que mejor representen la información contenida en los datos.

En entornos de **Big Data**, como el dataset de **NYC Yellow Cab**, **Spark** permite realizar estas transformaciones de forma escalable y eficiente gracias a la integración de **`sparklyr`**.

---

## 🔹 **Importancia de la Ingeniería de Características**
✅ Mejora la capacidad predictiva del modelo.  
✅ Reduce la dimensionalidad eliminando variables irrelevantes.  
✅ Transforma datos crudos en representaciones más significativas.  
✅ Facilita que los algoritmos de Machine Learning converjan más rápido.  

---

## 🔹 **Técnicas de Ingeniería de Características en Spark**

A continuación, se detallan técnicas clave de Feature Engineering implementadas en Spark utilizando el dataset de **NYC Yellow Cab** en formato **Parquet**.

---

## 📌 **1. Generación de Nuevas Variables Temporales**
El dataset de **NYC Yellow Cab** incluye una columna **`pickup_datetime`**, que puede explotarse para generar múltiples características útiles.

### 🔎 **Ejemplo: Extraer el día de la semana y la hora del día**
```r
library(sparklyr)
library(dplyr)

# Conectar a Spark
sc <- spark_connect(master = "local[*]")

# Cargar el dataset
taxis_nyc <- spark_read_parquet(
  sc,
  name = "taxis_nyc",
  path = "yellow_tripdata_2023-01.parquet"
)

# Generar nuevas variables temporales
taxis_nyc <- taxis_nyc %>%
  mutate(
    pickup_hour = hour(pickup_datetime),
    pickup_day = weekday(pickup_datetime)
  )

# Visualización de datos transformados
taxis_nyc %>%
  select(pickup_datetime, pickup_hour, pickup_day) %>%
  head(10) %>%
  collect()
```

✅ **¿Por qué es útil?**  
- Las horas pico (rush hours) suelen influir en la duración del viaje y el costo.  
- Los días festivos o fines de semana pueden impactar el comportamiento de las tarifas.

---

## 📌 **2. Transformación de Variables Categóricas con `ft_string_indexer()`**
Las variables categóricas no se pueden usar directamente en algoritmos como regresión lineal o árboles de decisión. Spark permite convertirlas en variables numéricas mediante **indexación** o **one-hot encoding**.

### 🔎 **Ejemplo: Indexar la columna `payment_type`**
```r
# Transformar la columna 'payment_type' a índices numéricos
taxis_nyc <- taxis_nyc %>%
  ft_string_indexer("payment_type", "payment_type_indexed")

# Visualización de la columna transformada
taxis_nyc %>%
  select(payment_type, payment_type_indexed) %>%
  distinct() %>%
  collect()
```

✅ **¿Por qué es útil?**  
- Permite que los algoritmos procesen variables categóricas.  
- Es especialmente valioso en Spark para evitar que la codificación ralentice el entrenamiento del modelo.

---

## 📌 **3. Escalado y Normalización de Variables con `ft_standard_scaler()`**
El escalado es importante para que las variables tengan una escala comparable, especialmente en algoritmos que se basan en distancias (como **KNN** o **K-Means**).

### 🔎 **Ejemplo: Escalar la columna `trip_distance`**
```r
# Escalado de la distancia del viaje
taxis_nyc <- taxis_nyc %>%
  ft_vector_assembler(input_cols = "trip_distance", output_col = "trip_distance_vec") %>%
  ft_standard_scaler(input_col = "trip_distance_vec", output_col = "trip_distance_scaled")

# Visualización de la distancia escalada
taxis_nyc %>%
  select(trip_distance, trip_distance_scaled) %>%
  head(10) %>%
  collect()
```

✅ **¿Por qué es útil?**  
- Evita que variables con escalas grandes dominen el modelo.  
- Es fundamental en algoritmos como **Regresión Lineal**, **SVM**, o **K-Means**.

---

## 📌 **4. Tratamiento de Valores Atípicos (Outliers)**
Los valores extremos pueden afectar negativamente el rendimiento del modelo. En Spark, se pueden manejar de distintas maneras:

✅ **Filtrado de valores atípicos**  
✅ **Winsorización** (ajustar los valores extremos a un umbral)  
✅ **Transformación logarítmica** para reducir el impacto de los outliers.

### 🔎 **Ejemplo: Filtrado de tarifas extremadamente altas**
```r
# Filtrar tarifas superiores al percentil 99
percentil_99 <- taxis_nyc %>%
  summarise(limite_superior = percentile_approx(fare_amount, 0.99)) %>%
  collect() %>%
  pull(limite_superior)

# Filtrar outliers
taxis_nyc <- taxis_nyc %>%
  filter(fare_amount < percentil_99)

# Visualización del nuevo rango de tarifas
taxis_nyc %>%
  summarise(min_fare = min(fare_amount), max_fare = max(fare_amount)) %>%
  collect()
```

✅ **¿Por qué es útil?**  
- Evita que los modelos se ajusten excesivamente a datos extremos.  
- Mejora la precisión de algoritmos sensibles a outliers (como regresión lineal).

---

## 📌 **5. Combinación de Variables (Feature Interaction)**
Crear nuevas variables combinando las existentes puede mejorar significativamente el rendimiento del modelo.

### 🔎 **Ejemplo: Crear una nueva variable 'tarifa por milla'**
```r
# Crear una nueva variable: tarifa por milla
taxis_nyc <- taxis_nyc %>%
  mutate(fare_per_mile = fare_amount / trip_distance)

# Visualización del resultado
taxis_nyc %>%
  select(fare_amount, trip_distance, fare_per_mile) %>%
  head(10) %>%
  collect()
```

✅ **¿Por qué es útil?**  
- Permite capturar relaciones no lineales en los datos.  
- Facilita la identificación de patrones que no son evidentes en las variables originales.

---

## 📌 **6. Imputación de Valores Faltantes**
El manejo de datos faltantes es crucial para que los algoritmos de Machine Learning funcionen correctamente.

### 🔎 **Ejemplo: Imputar valores faltantes en la columna `passenger_count` usando la mediana**
```r
# Imputar valores faltantes con la mediana
taxis_nyc <- taxis_nyc %>%
  mutate(passenger_count = ifelse(is.na(passenger_count), median(passenger_count, na.rm = TRUE), passenger_count))

# Comprobación de datos sin NA
taxis_nyc %>%
  summarise(total_na = sum(is.na(passenger_count))) %>%
  collect()
```

✅ **¿Por qué es útil?**  
- Evita que el modelo ignore registros valiosos.  
- Reduce el sesgo introducido por la eliminación directa de filas.

---

## 📌 **7. Selección de Variables (Feature Selection)**
Eliminar variables irrelevantes mejora la eficiencia del modelo y reduce el riesgo de sobreajuste (**overfitting**).

### 🔎 **Ejemplo: Eliminar columnas redundantes**
```r
# Seleccionar solo las columnas relevantes para el modelo
taxis_nyc <- taxis_nyc %>%
  select(fare_amount, trip_distance, passenger_count, pickup_hour)
```

✅ **¿Por qué es útil?**  
- Mejora el rendimiento del modelo.  
- Facilita la interpretabilidad del modelo.

---

## 📚 **6.7.3. División del Dataset en Conjuntos de Entrenamiento y Prueba**
✅ Uso de `sdf_partition()` para dividir los datos de forma eficiente.  
✅ Estrategias para manejar datasets desbalanceados.  
✅ Técnicas avanzadas para generar subconjuntos representativos en entornos Big Data.  

La **división de datos** en conjuntos de entrenamiento y prueba es un paso crucial en el flujo de trabajo de Machine Learning. Esta etapa garantiza que el modelo se evalúe con datos que no fueron vistos durante el entrenamiento, proporcionando una estimación más precisa de su rendimiento.

Cuando se trabaja con grandes volúmenes de datos, como el dataset de **NYC Yellow Cab**, Spark permite manejar esta fase de forma eficiente utilizando herramientas diseñadas para entornos distribuidos.

---

## 🔹 **Importancia de Dividir los Datos Correctamente**
✅ Permite evaluar de forma objetiva el rendimiento del modelo.  
✅ Evita el problema del **sobreajuste** (overfitting).  
✅ Facilita el ajuste de hiperparámetros y la selección del mejor modelo.  
✅ En datasets grandes, una división eficiente es clave para garantizar el rendimiento.  

---

## 🔹 **1. Uso de `sdf_partition()` para Dividir los Datos de Forma Eficiente**

La función **`sdf_partition()`** es la herramienta más eficiente en Spark para dividir un dataset en múltiples subconjuntos de forma distribuida.

### 🔎 **Sintaxis de `sdf_partition()`**
```r
sdf_partition(
  dataset,
  training = 0.8,
  testing = 0.2,
  seed = 1234  # Para reproducibilidad
)
```

🔹 **`training = 0.8`** → El 80% de los datos se asignan al conjunto de entrenamiento.  
🔹 **`testing = 0.2`** → El 20% restante se asigna al conjunto de prueba.  
🔹 **`seed = 1234`** → Permite obtener los mismos resultados en cada ejecución.  

---

### 📋 **Ejemplo Práctico: División del Dataset de NYC Yellow Cab**

#### Paso 1: Cargar el Dataset
```r
library(sparklyr)
library(dplyr)

# Conectar a Spark
sc <- spark_connect(master = "local[*]")

# Cargar el dataset desde Parquet
taxis_nyc <- spark_read_parquet(
  sc,
  name = "taxis_nyc",
  path = "yellow_tripdata_2023-01.parquet"
)
```

---

#### Paso 2: Dividir los Datos
```r
# División del dataset en entrenamiento (80%) y prueba (20%)
particiones <- taxis_nyc %>%
  sdf_partition(
    training = 0.8,
    testing = 0.2,
    seed = 1234
  )

# Asignar subconjuntos
taxis_train <- particiones$training
taxis_test <- particiones$testing

# Comprobación del tamaño de cada conjunto
cat("Registros en el conjunto de entrenamiento: ", sdf_nrow(taxis_train), "\n")
cat("Registros en el conjunto de prueba: ", sdf_nrow(taxis_test), "\n")
```

✅ **Ventaja:** `sdf_partition()` realiza la división de forma distribuida, lo que permite manejar grandes volúmenes de datos sin problemas de memoria.

---

## 🔹 **2. Estrategias para Manejar Datasets Desbalanceados**

En problemas de clasificación, es común que una clase tenga muchos más registros que otra. Esto puede sesgar el modelo predictivo, reduciendo su capacidad para identificar correctamente las clases minoritarias.

### 🔎 **Técnicas para manejar desequilibrios en los datos**
✅ **Submuestreo (Undersampling):** Reducir el número de registros en la clase mayoritaria.  
✅ **Sobremuestreo (Oversampling):** Aumentar el número de registros en la clase minoritaria.  
✅ **Ajuste de pesos en el modelo:** Asignar mayor peso a las clases minoritarias durante el entrenamiento.  

---

### 📋 **Ejemplo: Identificación de un Dataset Desbalanceado**

#### Paso 1: Comprobar el Balance de Clases
```r
# Comprobar la distribución de la variable 'payment_type'
taxis_nyc %>%
  group_by(payment_type) %>%
  summarise(total = n()) %>%
  collect()
```

Si el resultado muestra que una clase domina claramente (por ejemplo, el 90% de los registros pertenece a una clase), se recomienda aplicar una de las siguientes técnicas.

---

### 📋 **Ejemplo 1: Submuestreo (Undersampling)**
```r
# Seleccionar aleatoriamente un subconjunto de la clase mayoritaria
min_clase <- taxis_nyc %>%
  filter(payment_type == "Credit card") %>%
  sample_frac(0.5)

# Combinar con la clase minoritaria
taxis_balanceado <- taxis_nyc %>%
  filter(payment_type != "Credit card") %>%
  union(min_clase)
```

---

### 📋 **Ejemplo 2: Sobremuestreo (Oversampling)**
```r
# Duplicar registros de la clase minoritaria
minority_class <- taxis_nyc %>%
  filter(payment_type == "Cash")

# Duplicar los registros para equilibrar el dataset
taxis_balanceado <- taxis_nyc %>%
  union(minority_class) %>%
  union(minority_class)
```

✅ **Recomendación:** El sobre/submuestreo puede aplicarse con `sample_frac()` en Spark, garantizando eficiencia en grandes volúmenes de datos.

---

### 📋 **Ejemplo 3: Ajuste de Pesos en el Modelo**
En algoritmos como **`ml_logistic_regression()`**, se puede especificar un parámetro de ponderación (`weight_col`) para dar mayor relevancia a la clase minoritaria.

```r
modelo <- ml_logistic_regression(
  taxis_train,
  fare_amount ~ trip_distance + passenger_count,
  weight_col = "class_weight"
)
```

---

## 🔹 **3. Técnicas Avanzadas para Generar Subconjuntos Representativos**

Cuando se manejan grandes volúmenes de datos en Spark, dividir el dataset correctamente es clave para garantizar que:

✅ Los subconjuntos conserven la misma distribución estadística que el dataset original.  
✅ Los datos se distribuyan de forma eficiente para maximizar el rendimiento en el entrenamiento.  

---

### 📋 **Ejemplo 1: Muestreo Estratificado (Stratified Sampling)**
El **muestreo estratificado** garantiza que cada subconjunto tenga la misma proporción de registros para cada categoría.

```r
# División estratificada por 'payment_type'
particiones <- taxis_nyc %>%
  sdf_partition(
    training = 0.8,
    testing = 0.2,
    stratify_by = "payment_type",
    seed = 1234
  )

taxis_train <- particiones$training
taxis_test <- particiones$testing
```

✅ **Ventaja:** El muestreo estratificado es ideal para problemas de clasificación con clases desbalanceadas.  

---

### 📋 **Ejemplo 2: Muestreo Aleatorio (Random Sampling)**
Si no se requiere que las clases estén balanceadas, el muestreo aleatorio es una opción rápida y eficiente.

```r
# Muestreo del 30% de los datos de forma aleatoria
taxis_sample <- taxis_nyc %>%
  sdf_sample(fraction = 0.3, replacement = FALSE)
```

✅ **Ventaja:** Muy eficiente para datasets extremadamente grandes.

---

### 📋 **Ejemplo 3: Muestreo Temporal (Time-Based Sampling)**
En datos temporales, se recomienda dividir el dataset en función de rangos de fechas para que el modelo se entrene con datos históricos y se valide con datos recientes.

```r
# Dividir por fecha
taxis_train <- taxis_nyc %>%
  filter(pickup_datetime < "2023-01-20")

taxis_test <- taxis_nyc %>%
  filter(pickup_datetime >= "2023-01-20")
```

✅ **Ventaja:** Ideal para modelos predictivos basados en series temporales.


---

## 📚 **6.7.4. Modelos de Machine Learning en `sparklyr`**
✅ Modelos de regresión lineal y logística en Spark.  
✅ Modelos de clasificación como **Random Forest**, **Decision Trees**, **SVM**.  
✅ Modelos de regresión para predicciones continuas.  
✅ Modelos de clustering como **K-Means**

Spark ofrece una potente suite de algoritmos de **Machine Learning** a través de su librería **MLlib**, que permite realizar entrenamientos escalables, eficientes y distribuidos sobre grandes volúmenes de datos. La integración de Spark con R mediante el paquete **`sparklyr`** facilita la implementación de estos modelos de forma sencilla.

En esta sección se describen los principales modelos de Machine Learning soportados en Spark, acompañados de ejemplos prácticos utilizando el dataset de **NYC Yellow Cab**.

---

## 📌 **1. Modelos de Regresión Lineal y Logística**
### 🔎 **Regresión Lineal**
La **regresión lineal** se utiliza para predecir una variable continua (como el precio de un viaje o el tiempo de duración).

✅ **Fórmula general:**  
\[
y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \ldots + \beta_n X_n
\]

---

### 📋 **Ejemplo: Predicción de tarifas usando Regresión Lineal**
**Objetivo:** Predecir la tarifa (`fare_amount`) en función de la distancia del viaje (`trip_distance`).

```r
library(sparklyr)
library(dplyr)

# Conectar a Spark
sc <- spark_connect(master = "local[*]")

# Cargar el dataset
taxis_nyc <- spark_read_parquet(
  sc,
  name = "taxis_nyc",
  path = "yellow_tripdata_2023-01.parquet"
)

# Filtrar datos incorrectos
taxis_nyc <- taxis_nyc %>%
  filter(fare_amount > 0, trip_distance > 0)

# Entrenar el modelo de regresión lineal
modelo_rl <- taxis_nyc %>%
  ml_linear_regression(fare_amount ~ trip_distance + passenger_count)

# Evaluar el modelo
predicciones <- ml_predict(modelo_rl, taxis_nyc)

# Visualización
predicciones %>%
  select(fare_amount, prediction) %>%
  head(10) %>%
  collect()
```

✅ **Ventaja:** Fácil de interpretar y eficiente en datasets grandes.  
❗ **Desventaja:** Sensible a outliers y datos con relaciones no lineales.

---

### 🔎 **Regresión Logística**
La **regresión logística** se utiliza para resolver problemas de **clasificación binaria** (por ejemplo, si una propina supera cierto umbral).

✅ **Fórmula general:**  
\[
p = \frac{1}{1 + e^{-(\beta_0 + \beta_1 X_1 + \beta_2 X_2 + \ldots + \beta_n X_n)}}
\]

---

### 📋 **Ejemplo: Clasificación de viajes costosos (> $50)**
```r
# Variable objetivo: tarifa mayor o menor a $50
taxis_nyc <- taxis_nyc %>%
  mutate(fare_expensive = ifelse(fare_amount > 50, 1, 0))

# Entrenar el modelo de regresión logística
modelo_log <- taxis_nyc %>%
  ml_logistic_regression(fare_expensive ~ trip_distance + passenger_count)

# Evaluar el modelo
predicciones <- ml_predict(modelo_log, taxis_nyc)

# Visualización de predicciones
predicciones %>%
  select(fare_expensive, prediction) %>%
  head(10) %>%
  collect()
```

✅ **Ventaja:** Robusta para problemas de clasificación binaria.  
❗ **Desventaja:** Menos eficiente en datasets con múltiples clases.

---

## 📌 **2. Modelos de Clasificación: Random Forest, Árboles de Decisión y SVM**

### 🔎 **a) Random Forest**
El algoritmo **Random Forest** es un modelo basado en múltiples árboles de decisión que mejora la precisión y reduce el riesgo de sobreajuste.

✅ **Ventaja:** Robusto ante outliers y datos ruidosos.  
❗ **Desventaja:** Puede ser costoso en términos computacionales.

---

### 📋 **Ejemplo: Clasificación de tipo de pago usando Random Forest**
```r
# Entrenar un modelo Random Forest
modelo_rf <- taxis_nyc %>%
  ml_random_forest(payment_type ~ trip_distance + passenger_count)

# Evaluar el modelo
predicciones <- ml_predict(modelo_rf, taxis_nyc)

# Visualización de predicciones
predicciones %>%
  select(payment_type, prediction) %>%
  head(10) %>%
  collect()
```

---

### 🔎 **b) Árboles de Decisión**
Los **Árboles de Decisión** son modelos que dividen los datos en función de preguntas basadas en características.

✅ **Ventaja:** Fácil interpretación y visualización.  
❗ **Desventaja:** Pueden generar sobreajuste en datasets grandes.

---

### 📋 **Ejemplo de Árbol de Decisión**
```r
modelo_tree <- taxis_nyc %>%
  ml_decision_tree(payment_type ~ trip_distance + passenger_count)

ml_predict(modelo_tree, taxis_nyc) %>%
  select(payment_type, prediction) %>%
  head(10) %>%
  collect()
```

---

### 🔎 **c) SVM (Support Vector Machines)**
Las **SVM** son eficaces para clasificar datos no lineales utilizando un **margen óptimo de separación**.

✅ **Ventaja:** Eficiente en datasets grandes.  
❗ **Desventaja:** Menos intuitivo que otros modelos.

---

### 📋 **Ejemplo de SVM**
```r
modelo_svm <- taxis_nyc %>%
  ml_linear_svc(payment_type ~ trip_distance + passenger_count)

ml_predict(modelo_svm, taxis_nyc) %>%
  select(payment_type, prediction) %>%
  head(10) %>%
  collect()
```

---

## 📌 **3. Modelos de Clustering: K-Means y GMM**

### 🔎 **a) K-Means**
El algoritmo **K-Means** se utiliza para agrupar datos en clusters similares.

✅ **Ventaja:** Muy eficiente en grandes volúmenes de datos.  
❗ **Desventaja:** Sensible a la elección del número de clusters (`k`).

---

### 📋 **Ejemplo de Clustering con K-Means**
```r
modelo_kmeans <- taxis_nyc %>%
  ml_kmeans(features = c("trip_distance", "fare_amount"), k = 3)

# Visualización de clusters
predicciones <- ml_predict(modelo_kmeans, taxis_nyc)

predicciones %>%
  select(trip_distance, fare_amount, prediction) %>%
  head(10) %>%
  collect()
```

---

### 🔎 **b) Gaussian Mixture Model (GMM)**
El **GMM** es un modelo de clustering que asume que los datos provienen de múltiples distribuciones normales.

✅ **Ventaja:** Capta mejor la variabilidad de los datos.  
❗ **Desventaja:** Menos eficiente que K-Means.

---

### 📋 **Ejemplo con GMM**
```r
modelo_gmm <- taxis_nyc %>%
  ml_gaussian_mixture(features = c("trip_distance", "fare_amount"), k = 3)

ml_predict(modelo_gmm, taxis_nyc) %>%
  select(trip_distance, fare_amount, prediction) %>%
  head(10) %>%
  collect()
```

---

## 📚 **6.7.4. Evaluación y Validación de Modelos**
✅ Uso de métricas como **RMSE**, **MAE**, **AUC** y **F1 Score**.  
✅ Evaluación de modelos usando `ml_evaluate()`.  
✅ Implementación de la validación cruzada en Spark con `ml_cross_validator()`.  
✅ Ajuste de hiperparámetros mediante `ml_tuning()`.  

La **evaluación de modelos** es una fase crucial en el flujo de trabajo de Machine Learning, ya que permite medir el rendimiento del modelo y garantizar que sus predicciones son precisas y generalizables.

Spark, a través de su paquete **`sparklyr`**, proporciona herramientas potentes para evaluar el rendimiento de modelos entrenados, validar su comportamiento en datos no vistos y optimizar sus hiperparámetros para mejorar la precisión.

---

## 🔹 **Importancia de la Evaluación y Validación**
✅ Permite medir la precisión y el error del modelo.  
✅ Identifica si el modelo está sobreajustado (**overfitting**) o subajustado (**underfitting**).  
✅ Facilita la elección del mejor modelo entre varias alternativas.  
✅ Optimiza el rendimiento del modelo mediante la búsqueda de los mejores hiperparámetros.  

---

## 📌 **1. Uso de Métricas para Evaluar Modelos**
En Spark, se pueden aplicar diversas métricas para evaluar modelos según el tipo de problema:

| **Métrica** | **Descripción** | **Uso recomendado** |
|:-------------|:-----------------|:---------------------|
| **RMSE** (Root Mean Squared Error) | Mide el error cuadrático medio en una escala real. | Modelos de regresión. |
| **MAE** (Mean Absolute Error) | Mide el error medio absoluto, menos sensible a outliers que RMSE. | Modelos de regresión. |
| **R²** (R-Squared) | Mide qué tan bien el modelo explica la variabilidad de los datos. | Modelos de regresión. |
| **AUC** (Area Under Curve) | Mide la capacidad del modelo para distinguir entre clases. | Modelos de clasificación binaria. |
| **F1 Score** | Combina precisión y exhaustividad en una única métrica. | Modelos de clasificación multiclase. |

---

### 📋 **Ejemplo: Evaluación con RMSE, MAE y R² (Regresión Lineal)**
**Objetivo:** Predecir la tarifa del viaje (`fare_amount`) en función de la distancia (`trip_distance`).

```r
library(sparklyr)
library(dplyr)

# Conexión a Spark
sc <- spark_connect(master = "local[*]")

# Cargar el dataset NYC Yellow Cab
taxis_nyc <- spark_read_parquet(
  sc,
  name = "taxis_nyc",
  path = "yellow_tripdata_2023-01.parquet"
) %>%
  filter(fare_amount > 0, trip_distance > 0)

# Dividir los datos en entrenamiento y prueba
particiones <- sdf_partition(
  taxis_nyc,
  training = 0.8,
  testing = 0.2,
  seed = 1234
)

taxis_train <- particiones$training
taxis_test <- particiones$testing

# Entrenar el modelo de regresión lineal
modelo_rl <- ml_linear_regression(
  taxis_train,
  fare_amount ~ trip_distance + passenger_count
)

# Evaluación del modelo
evaluacion <- ml_evaluate(modelo_rl, taxis_test)

# Visualización de métricas
evaluacion %>%
  select(rmse, mae, r2) %>%
  collect()
```

✅ **RMSE bajo** indica que el modelo tiene menor error en las predicciones.  
✅ **R² cercano a 1** indica que el modelo explica bien la variabilidad de los datos.  

---

### 📋 **Ejemplo: Evaluación con AUC y F1 Score (Clasificación)**
**Objetivo:** Clasificar si un viaje es costoso (`fare_amount > 50`) o no.

```r
# Crear una variable categórica binaria
taxis_nyc <- taxis_nyc %>%
  mutate(fare_expensive = ifelse(fare_amount > 50, 1, 0))

# Entrenar un modelo de regresión logística
modelo_log <- ml_logistic_regression(
  taxis_train,
  fare_expensive ~ trip_distance + passenger_count
)

# Evaluación del modelo
evaluacion_log <- ml_evaluate(modelo_log, taxis_test)

# Visualización de métricas
evaluacion_log %>%
  select(auc, f1) %>%
  collect()
```

✅ **AUC alto** (> 0.8) indica que el modelo distingue bien entre clases.  
✅ **F1 Score alto** indica que el modelo logra un buen equilibrio entre precisión y exhaustividad.  

---

## 📌 **2. Evaluación de Modelos usando `ml_evaluate()`**
La función **`ml_evaluate()`** permite evaluar automáticamente modelos de regresión y clasificación en Spark, proporcionando métricas clave para tomar decisiones.

### 🔎 **Principales métricas que devuelve `ml_evaluate()`**
- Para **Regresión**: RMSE, MAE, R².  
- Para **Clasificación**: AUC, F1 Score, Precisión, Recall.  

---

### 📋 **Ejemplo: Evaluación Completa de un Modelo de Clasificación**
```r
# Evaluación del modelo de regresión logística
evaluacion <- ml_evaluate(modelo_log, taxis_test)

# Visualización completa
evaluacion %>%
  collect()
```

✅ **Ventaja:** Facilita la obtención de métricas sin necesidad de cálculos manuales.  
✅ **Flexible:** Se adapta automáticamente al tipo de modelo (regresión o clasificación).  

---

## 📌 **3. Implementación de Validación Cruzada en Spark con `ml_cross_validator()`**
La **validación cruzada** es una técnica avanzada que evalúa el rendimiento del modelo utilizando múltiples particiones del dataset.

✅ **Ventaja:** Proporciona una evaluación más robusta del modelo.  
❗ **Desventaja:** Puede ser computacionalmente costoso en datasets muy grandes.

---

### 📋 **Ejemplo: Validación Cruzada en Spark**
```r
# Crear un grid de hiperparámetros para Random Forest
param_grid <- list(
  num_trees = c(10, 20, 50),
  max_depth = c(5, 10, 20)
)

# Validación cruzada con búsqueda de hiperparámetros
cv_model <- ml_cross_validator(
  taxis_train,
  estimator = ml_random_forest(payment_type ~ trip_distance + passenger_count),
  estimator_param_maps = param_grid,
  evaluator = ml_binary_classification_evaluator(),
  num_folds = 5
)

# Mejor modelo tras validación cruzada
best_model <- ml_fit(cv_model, taxis_train)

# Evaluación del modelo óptimo
ml_evaluate(best_model, taxis_test) %>%
  collect()
```

✅ **Ventaja:** Garantiza una evaluación más precisa mediante múltiples particiones del dataset.  
✅ **Ideal para entornos Big Data**, donde una única partición de datos puede no representar bien el conjunto completo.  

---

## 📌 **4. Ajuste de Hiperparámetros mediante `ml_tuning()`**
El ajuste de hiperparámetros permite encontrar la combinación óptima de parámetros que maximiza el rendimiento del modelo.

✅ **Ventaja:** Permite automatizar la búsqueda de la mejor configuración del modelo.  
❗ **Desventaja:** Puede requerir mayor tiempo de cómputo.

---

### 📋 **Ejemplo: Ajuste de Hiperparámetros en un Modelo de Regresión Lineal**
```r
# Grid de hiperparámetros
param_grid <- list(
  reg_param = c(0.01, 0.1, 0.5),
  elastic_net_param = c(0.0, 0.5, 1.0)
)

# Modelo con ajuste de hiperparámetros
modelo_tuned <- ml_tuning(
  estimator = ml_linear_regression(fare_amount ~ trip_distance),
  estimator_param_maps = param_grid,
  evaluator = ml_regression_evaluator(),
  num_folds = 5
)

# Entrenamiento del modelo optimizado
best_model <- ml_fit(modelo_tuned, taxis_train)

# Evaluación del mejor modelo
ml_evaluate(best_model, taxis_test) %>%
  collect()
```

# 📚 **Casos Prácticos de Machine Learning con `sparklyr`**

En esta sección desarrollaremos ejemplos prácticos utilizando el dataset de **NYC Yellow Cab** en formato **Parquet**, junto con ejemplos adicionales para detección de fraude y sistemas de recomendación.

---

## 🔹 **1. Detección de Fraude usando Random Forest**
### 🚨 **Objetivo:** Detectar posibles fraudes en los viajes de taxis en NYC utilizando un modelo de **Random Forest**.

### 📋 **Dataset Recomendado:** [NYC Yellow Cab Data - Parquet](https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.parquet)

---

### 🔎 **Paso 1: Cargar el Dataset y Filtrar Datos Irregulares**
```r
library(sparklyr)
library(dplyr)

# Conexión a Spark
sc <- spark_connect(master = "local[*]")

# Cargar el dataset de NYC Yellow Cab
taxis_nyc <- spark_read_parquet(
  sc,
  name = "taxis_nyc",
  path = "yellow_tripdata_2023-01.parquet"
)

# Filtrar datos irregulares
taxis_nyc <- taxis_nyc %>%
  filter(fare_amount > 0, trip_distance > 0)

# Crear una variable binaria de fraude: 1 = Fraude, 0 = No fraude
taxis_nyc <- taxis_nyc %>%
  mutate(fraud = ifelse(fare_amount / trip_distance > 15, 1, 0))
```

---

### 🔎 **Paso 2: Dividir los Datos en Entrenamiento y Prueba**
```r
particiones <- sdf_partition(
  taxis_nyc,
  training = 0.8,
  testing = 0.2,
  seed = 1234
)

taxis_train <- particiones$training
taxis_test <- particiones$testing
```

---

### 🔎 **Paso 3: Entrenar el Modelo de Random Forest**
```r
modelo_rf <- ml_random_forest(
  taxis_train,
  fraud ~ trip_distance + fare_amount + passenger_count
)
```

---

### 🔎 **Paso 4: Evaluar el Modelo**
```r
predicciones <- ml_predict(modelo_rf, taxis_test)

# Métricas de evaluación
evaluacion <- ml_evaluate(modelo_rf, taxis_test)

# Mostrar el AUC y el F1 Score
evaluacion %>%
  select(auc, f1) %>%
  collect()
```

✅ **Conclusión:** El modelo identifica viajes sospechosos basados en características anómalas, como tarifas elevadas en distancias cortas.

---

## 🔹 **2. Predicción de Tarifas de Taxis usando Regresión Lineal**
### 🚨 **Objetivo:** Predecir la tarifa (`fare_amount`) en función de la distancia y el número de pasajeros.

---

### 🔎 **Paso 1: Cargar el Dataset**
```r
# Cargar el dataset
taxis_nyc <- spark_read_parquet(
  sc,
  name = "taxis_nyc",
  path = "yellow_tripdata_2023-01.parquet"
) %>%
  filter(fare_amount > 0, trip_distance > 0)
```

---

### 🔎 **Paso 2: Entrenar el Modelo de Regresión Lineal**
```r
modelo_rl <- ml_linear_regression(
  taxis_nyc,
  fare_amount ~ trip_distance + passenger_count
)
```

---

### 🔎 **Paso 3: Evaluar el Modelo**
```r
evaluacion <- ml_evaluate(modelo_rl, taxis_nyc)

# Visualización de métricas
evaluacion %>%
  select(rmse, r2) %>%
  collect()
```

---

### 🔎 **Paso 3: Generar Recomendaciones**
```r
# Generar recomendaciones para todos los usuarios
recomendaciones <- ml_recommend(modelo_als, type = "items", num = 5)

# Visualizar recomendaciones
recomendaciones %>%
  head(10) %>%
  collect()
```

✅ **Conclusión:** Este modelo recomienda productos específicos a cada cliente basándose en su historial de compras.

---

## 🔹 **4. Segmentación de Clientes usando K-Means**
### 🚨 **Objetivo:** Identificar grupos de pasajeros con comportamientos similares en función de la distancia y la tarifa del viaje.

---

### 🔎 **Paso 1: Cargar el Dataset**
```r
# Cargar el dataset
taxis_nyc <- spark_read_parquet(
  sc,
  name = "taxis_nyc",
  path = "yellow_tripdata_2023-01.parquet"
)
```

---

### 🔎 **Paso 2: Entrenar el Modelo K-Means**
```r
modelo_kmeans <- ml_kmeans(
  taxis_nyc,
  features = c("trip_distance", "fare_amount"),
  k = 3
)
```
---

### 🔎 **Paso 3: Visualización de los Clusters**
```r
# Predecir los clusters
clusters <- ml_predict(modelo_kmeans, taxis_nyc)

# Visualizar algunos registros con sus clusters asignados
clusters %>%
  select(trip_distance, fare_amount, prediction) %>%
  head(10) %>%
  collect()
```



