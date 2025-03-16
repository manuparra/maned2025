# 🔵 **Tema 6.5.4 – Manipulación y Consulta de Datos con Spark SQL**

La manipulación y consulta de datos es una parte esencial en el manejo de **Big Data**. Para trabajar con grandes volúmenes de datos de forma eficiente, se requiere el uso de herramientas optimizadas que permitan realizar consultas avanzadas en entornos distribuidos.

En esta sección aprenderás a:

✅ Entender qué es Spark SQL y cómo se integra con `sparklyr` en R  
✅ Realizar consultas básicas y avanzadas en Spark SQL  
✅ Implementar operaciones comunes como `GROUP BY`, `JOIN`, `ORDER BY`, etc.  
✅ Ejecutar consultas sobre el dataset real de **NYC Taxi Trips**  
✅ Aplicar técnicas avanzadas como subconsultas, ventanas y funciones analíticas  
✅ Resolver problemas reales usando Spark SQL en un entorno empresarial  

---

# 🔹 **1. ¿Qué es Spark SQL?**

**Spark SQL** es un módulo de Apache Spark que proporciona una interfaz basada en **SQL** para consultar datos distribuidos de forma eficiente. Permite ejecutar sentencias SQL estándar sobre datos almacenados en Spark, ya sea en formato **Parquet**, **CSV**, **JSON** o en sistemas distribuidos como **HDFS**, **S3**, etc.

### 🔎 **Ventajas de Spark SQL**
✅ Permite realizar consultas avanzadas utilizando sintaxis SQL estándar  
✅ Soporta una amplia variedad de fuentes de datos (Parquet, JSON, Avro, etc.)  
✅ Optimización automática del plan de ejecución mediante el **Catalyst Optimizer**  
✅ Integración perfecta con `dplyr`, permitiendo manipulación híbrida en SQL y R  
✅ Escalable para manejar volúmenes masivos de datos  

---

# 🔹 **2. Configuración inicial de Spark SQL en R con `sparklyr`**

Para ejecutar consultas SQL desde R utilizando Spark SQL, se requiere configurar la conexión usando `sparklyr`.

### 🔎 **Configuración de Spark con R**
```r
# Instalar librerías necesarias
library(sparklyr)
library(dplyr)

# Conexión a Spark en modo local
sc <- spark_connect(master = "local")

# Cargar el dataset de NYC Taxi Trips (Parquet)
taxi_nyc <- spark_read_parquet(sc,
                               name = "taxi_nyc",
                               path = "https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.parquet")

# Verificar las primeras filas
taxi_nyc %>% head() %>% collect()
```

---

# 🔹 **3. Consultas Básicas en Spark SQL**

## 📌 **3.1. Sintaxis básica en Spark SQL**
La sintaxis de Spark SQL es muy similar a SQL estándar. Ejemplos de consultas básicas incluyen:

✅ `SELECT` – Para seleccionar columnas específicas  
✅ `WHERE` – Para filtrar registros según condiciones  
✅ `ORDER BY` – Para ordenar los datos según criterios  
✅ `LIMIT` – Para limitar el número de resultados  

---

## 🔎 **3.2. Ejemplos básicos con el dataset NYC Taxi Trips**

### ➤ **Seleccionar columnas específicas**
```r
# Mostrar las columnas 'pickup_datetime', 'fare_amount' y 'tip_amount'
dbGetQuery(sc, "
  SELECT 
    pickup_datetime,
    fare_amount,
    tip_amount
  FROM taxi_nyc
  LIMIT 10
")
```

---

### ➤ **Filtrar registros según una condición**
Por ejemplo, filtrar viajes con tarifas superiores a 50 USD:

```r
dbGetQuery(sc, "
  SELECT 
    pickup_datetime,
    fare_amount
  FROM taxi_nyc
  WHERE fare_amount > 50
  LIMIT 10
")
```

---

### ➤ **Ordenar los datos por un criterio específico**
Ordenar por tarifas más elevadas:

```r
dbGetQuery(sc, "
  SELECT 
    pickup_datetime,
    fare_amount
  FROM taxi_nyc
  ORDER BY fare_amount DESC
  LIMIT 10
")
```

---

### ➤ **Calcular estadísticas básicas**
Por ejemplo, calcular la tarifa promedio de todos los viajes.

```r
dbGetQuery(sc, "
  SELECT 
    AVG(fare_amount) AS tarifa_promedio
  FROM taxi_nyc
")
```

---

# 🔹 **4. Consultas Avanzadas en Spark SQL**

Las consultas avanzadas en Spark SQL permiten realizar análisis más complejos mediante el uso de:

✅ **`GROUP BY`** para agrupaciones  
✅ **`JOIN`** para combinar tablas  
✅ **`WINDOW FUNCTIONS`** para cálculos avanzados sobre particiones de datos  
✅ **`SUBQUERIES`** para cálculos condicionales o anidados  

---

## 📌 **4.1. Agrupaciones con `GROUP BY`**

### ➤ **Calcular el total de ingresos por día**
```r
dbGetQuery(sc, "
  SELECT 
    DATE(pickup_datetime) AS fecha,
    SUM(fare_amount) AS total_ingresos
  FROM taxi_nyc
  GROUP BY fecha
  ORDER BY fecha ASC
")
```

---

### ➤ **Calcular la tarifa promedio por método de pago**
```r
dbGetQuery(sc, "
  SELECT 
    payment_type,
    AVG(fare_amount) AS tarifa_media
  FROM taxi_nyc
  GROUP BY payment_type
")
```

---

## 📌 **4.2. Uniones con `JOIN`**

Spark SQL permite combinar tablas de forma eficiente utilizando `JOIN`.

### ➤ **Unir la tabla de taxis con una tabla de zonas**
```r
zonas <- data.frame(
  zone_id = c(1, 2, 3),
  zone_name = c("Manhattan", "Brooklyn", "Queens")
)

# Cargar la tabla de zonas en Spark
sdf_copy_to(sc, zonas, name = "zonas", overwrite = TRUE)

# JOIN para combinar ambas tablas
dbGetQuery(sc, "
  SELECT 
    taxi_nyc.PULocationID,
    zonas.zone_name,
    AVG(taxi_nyc.fare_amount) AS tarifa_promedio
  FROM taxi_nyc
  JOIN zonas
    ON taxi_nyc.PULocationID = zonas.zone_id
  GROUP BY zonas.zone_name
")
```

---

## 📌 **4.3. Funciones de Ventana (`WINDOW FUNCTIONS`)**

Las funciones de ventana permiten calcular métricas avanzadas sobre subconjuntos de datos.

### ➤ **Calcular la tarifa acumulada por día**
```r
dbGetQuery(sc, "
  SELECT 
    pickup_datetime,
    fare_amount,
    SUM(fare_amount) OVER (PARTITION BY DATE(pickup_datetime)) AS tarifa_acumulada
  FROM taxi_nyc
  ORDER BY pickup_datetime ASC
  LIMIT 10
")
```

---

## 📌 **4.4. Subconsultas (`SUBQUERIES`)**

Las subconsultas permiten anidar consultas para resolver problemas complejos.

### ➤ **Obtener las 5 tarifas más altas de cada día**
```r
dbGetQuery(sc, "
  SELECT *
  FROM (
    SELECT 
      pickup_datetime,
      fare_amount,
      RANK() OVER (PARTITION BY DATE(pickup_datetime) ORDER BY fare_amount DESC) AS rank
    FROM taxi_nyc
  ) AS ranked
  WHERE rank <= 5
")
```

---

# 🔹 **5. Ejercicios Prácticos**

### 🟩 **Ejercicio 1 – Análisis de Propinas por Zonas**
**Objetivo:**  
- Cargar el dataset de NYC Taxi Trips.  
- Calcular la propina media (`tip_amount`) por cada zona.  
- Identificar la zona que registra la mayor propina promedio.  

**Código:**

```r

```

---

### 🟩 **Ejercicio 2 – Análisis de Viajes Largos**
**Objetivo:**  
- Identificar los viajes cuya distancia (`trip_distance`) sea superior a **30 millas**.  
- Determinar la tarifa promedio para estos viajes.  

**Código:**

```r

```
