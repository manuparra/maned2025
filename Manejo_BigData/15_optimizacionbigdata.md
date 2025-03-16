# 🔵 **Tema 6.5.6 – Optimización del Rendimiento en Big Data**

La optimización del rendimiento es una fase crítica en proyectos de **Big Data**, especialmente cuando trabajamos con grandes volúmenes de datos en entornos distribuidos como **Apache Spark**. Una ejecución ineficiente puede consumir muchos recursos, aumentar los tiempos de cómputo e impactar negativamente en la escalabilidad del sistema.

En esta sección aprenderás a:

✅ Comprender los conceptos clave para optimizar el rendimiento en Spark  
✅ Utilizar técnicas avanzadas como **Data Locality**, **Particionado**, **Caché/Persistencia** y **Optimización de Memoria**  
✅ Elegir el mejor formato de almacenamiento para mejorar la velocidad y reducir el uso de recursos  
✅ Aplicar estas técnicas usando el dataset real de **NYC Taxi Trips**  
✅ Implementar ejemplos prácticos con mejoras cuantificables en tiempos de ejecución  

---

# 🔹 **1. Conceptos clave para optimización en Spark**

Apache Spark es un entorno distribuido que divide el trabajo en **tareas** (tasks) ejecutadas en **nodos** del clúster. Optimizar el rendimiento requiere minimizar el tiempo que Spark emplea en transferir datos y maximizar el aprovechamiento de la memoria y CPU.

---

## 📌 **1.1. Data Locality (Localidad de datos)**
El concepto de **localidad de datos** se refiere a ejecutar las tareas lo más cerca posible de los datos que se están procesando. Spark intenta ubicar las tareas en los mismos nodos donde se encuentran los datos, minimizando la transferencia a través de la red.

### 🔎 **Cómo optimizar la localidad de datos**
1. Usar formatos que favorezcan el almacenamiento distribuido como **Parquet** o **ORC**.  
2. Utilizar sistemas de archivos distribuidos como **HDFS** o **Amazon S3**.  
3. Configurar correctamente el particionado para garantizar que los datos estén repartidos de forma equilibrada.

**Ejemplo:** Cargar el dataset de taxis en formato Parquet, que optimiza la localidad de datos.

```r
# Cargar datos en formato Parquet (favorece la localidad de datos)
taxi_nyc <- spark_read_parquet(sc,
                               name = "taxi_nyc",
                               path = "hdfs://ruta/dataset.parquet")
```

---

## 📌 **1.2. Particionado y Replicación de Datos**
El **particionado** es el proceso de dividir grandes datasets en bloques más pequeños que se pueden procesar en paralelo. Esto es clave para mejorar el rendimiento en Spark.

### 🔎 **Cómo optimizar el particionado**
- Un particionado demasiado pequeño genera demasiadas tareas pequeñas que ralentizan el rendimiento.  
- Un particionado demasiado grande sobrecarga la memoria de cada nodo.  
- El número ideal de particiones se calcula generalmente como **4 x número de CPUs disponibles**.

**Configuración del número de particiones óptimo**
```r
# Repartir el dataset en 100 particiones para optimizar el rendimiento
taxi_nyc <- taxi_nyc %>%
  sdf_repartition(partitions = 100)
```

---

## 📌 **1.3. Formatos de Almacenamiento Eficientes**
Elegir el formato adecuado de almacenamiento es clave en proyectos Big Data.  

| **Formato** | **Ventajas** | **Uso recomendado** |
|---------------|------------------|-------------------------|
| **Parquet** | Eficiente en consultas analíticas; Soporta compresión y columnar | Consultas frecuentes sobre grandes volúmenes de datos |
| **ORC** | Similar a Parquet; Excelente en entornos Hadoop | Procesamiento intensivo en Spark y Hive |
| **Delta Lake** | Extiende Parquet con transacciones ACID y versionado de datos | Pipeline de datos confiable en producción |

---

### 🔎 **Ejemplo: Conversión de CSV a Parquet**
```r
# Cargar el dataset en formato CSV
taxi_nyc_csv <- spark_read_csv(sc,
                               name = "taxi_nyc_csv",
                               path = "hdfs://ruta/nyc_taxi.csv")

# Guardar en formato Parquet (mucho más eficiente)
spark_write_parquet(taxi_nyc_csv, path = "hdfs://ruta/nyc_taxi.parquet")
```

---

## 📌 **1.4. Caché y Persistencia en Spark**
El **caché** y la **persistencia** permiten almacenar temporalmente los datos en memoria para acelerar el acceso en cálculos repetidos.

### 🔎 **Diferencias entre Cache y Persistencia**
| Método | Características |
|---------|-----------------|
| **Cache()** | Almacena temporalmente los datos en memoria para accesos rápidos; solo es válido dentro de la sesión actual. |
| **Persist()** | Similar a `cache()` pero permite elegir niveles de almacenamiento (RAM, disco o ambos). |

### 🔎 **Cuándo usar cada uno**
- Utiliza **`cache()`** cuando vas a reutilizar un dataset en múltiples pasos dentro de una misma sesión.  
- Utiliza **`persist()`** cuando el dataset es grande y quieres control sobre cómo se almacena.

---

### 🔎 **Ejemplo: Uso de `cache()` para optimizar consultas repetidas**
```r
# Cargar datos en memoria para reutilización rápida
taxi_nyc <- taxi_nyc %>% cache()

# Consulta rápida usando los datos en caché
taxi_nyc %>%
  group_by(DATE(pickup_datetime)) %>%
  summarise(total_ingresos = sum(fare_amount)) %>%
  collect()
```

---

### 🔎 **Ejemplo: Uso de `persist()` en Spark**
```r
# Persistir el dataset en memoria + disco (recomendado para conjuntos muy grandes)
taxi_nyc %>% persist("MEMORY_AND_DISK")
```

---

## 📌 **1.5. Optimización del Tamaño de Particiones**
El tamaño de cada partición es un factor crítico en el rendimiento de Spark. Si las particiones son demasiado grandes, algunas tareas ocuparán más tiempo que otras, reduciendo la eficiencia del clúster.

### 🔎 **Configuración óptima de particiones en Spark**
```r
# Optimizar número de particiones en base a la cantidad de nodos disponibles
taxi_nyc <- sdf_repartition(taxi_nyc, partitions = 200)
```

---

# 🔹 **2. Ejemplos Prácticos de Optimización en Spark**

## ➤ **Ejemplo 1 – Comparación de tiempos de consulta con y sin optimización**
Este ejemplo evalúa el impacto del uso de `cache()` y formato **Parquet** en tiempos de consulta.

### **Paso 1:** Carga inicial sin optimización
```r
system.time({
  taxi_nyc %>%
    group_by(DATE(pickup_datetime)) %>%
    summarise(total_ingresos = sum(fare_amount)) %>%
    collect()
})
```

### **Paso 2:** Carga optimizada con `cache()` y Parquet
```r
# Almacenar en Parquet (optimizado)
spark_write_parquet(taxi_nyc, path = "hdfs://ruta/nyc_taxi.parquet")

# Cargar el dataset Parquet y usar cache
taxi_nyc_opt <- spark_read_parquet(sc, name = "taxi_nyc_opt", path = "hdfs://ruta/nyc_taxi.parquet") %>%
  cache()

# Comparar tiempos
system.time({
  taxi_nyc_opt %>%
    group_by(DATE(pickup_datetime)) %>%
    summarise(total_ingresos = sum(fare_amount)) %>%
    collect()
})
```

---

## 🔹 **3. Ejercicios Prácticos**

### 🟩 **Ejercicio 1 – Optimización con Parquet y Cache**
**Objetivo:**  
- Cargar el dataset de taxis en formato **CSV**.  
- Convertir el dataset a **Parquet**.  
- Utilizar `cache()` para optimizar la ejecución de una consulta que calcule la distancia media por día.  

**Código orientativo:**
```r

```

---

### 🟩 **Ejercicio 2 – Optimización del Particionado**
**Objetivo:**  
- Cargar el dataset de taxis en formato **Parquet**.  
- Repartir los datos en **100 particiones** para optimizar el rendimiento.  
- Calcular la tarifa promedio por día.  

**Código orientativo:**
```r

```

