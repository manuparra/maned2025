# 🔵 **Tema 6.5.3 – Transformación y Limpieza de Datos en Big Data**

La **transformación y limpieza de datos** es una fase importante en el ciclo de vida del Big Data. Los datos reales, especialmente en entornos empresariales, suelen estar incompletos, inconsistentes o en formatos poco manejables, lo que requiere técnicas avanzadas para mejorar su calidad y adecuarlos a su uso analítico.

En esta sección cubriremos en profundidad:

✅ Introducción a la transformación y limpieza de datos  
✅ Operaciones de limpieza (datos faltantes, inconsistencias, duplicados)  
✅ Transformaciones avanzadas (joins, filtrado, agregaciones)  
✅ Uso de herramientas clave: `sparklyr`, `dplyr` y pipelines en Spark  
✅ Ejemplos prácticos con el dataset de **NYC Taxi Trips**  
✅ Ejercicios prácticos para que los alumnos fortalezcan su comprensión  

---

## 🔹 **1. Introducción a la Transformación y Limpieza de Datos**

El **proceso de transformación** se centra en convertir los datos brutos en un formato limpio, coherente y estructurado, listo para su análisis o modelado predictivo.  

El proceso de **limpieza de datos** se enfoca en resolver problemas comunes como:

✅ Valores faltantes  
✅ Inconsistencias en los datos  
✅ Duplicados  
✅ Valores atípicos (outliers)  
✅ Formatos incorrectos  

### 🚨 **¿Por qué es importante limpiar los datos en Big Data?**

En proyectos de Big Data, la calidad de los datos es clave. Trabajar con datos incorrectos puede generar:

❌ Predicciones inexactas  
❌ Informes erróneos que dificulten la toma de decisiones  
❌ Desperdicio de recursos computacionales  

El dataset de **NYC Taxi Trips**, por ejemplo, puede contener, entre otros:

- **Registros incompletos** (sin fecha de viaje o con coordenadas GPS incorrectas).  
- **Tarifas negativas** (errores en la captura de datos).  
- **Duplicados** (registros duplicados debido a errores en el sistema de recopilación).  

---

## 🔹 **2. Operaciones de Limpieza de Datos**

En esta sección abordaremos técnicas clave de limpieza usando R y Spark.

### 🟠 **2.1. Detección y Gestión de Valores Faltantes**
Los datos faltantes pueden deberse a errores en el sistema de registro, sensores defectuosos o fallos en el envío de datos.

### 🔎 **Identificar valores faltantes**
```r
# Identificar valores faltantes en el dataset
taxi_nyc %>%
  summarise_all(~ sum(is.na(.))) %>%
  collect()
```

### 🔧 **Gestión de datos faltantes**
- **Eliminación:** Para datos irrelevantes o registros con pocos valores.  
- **Imputación:** Rellenar los valores faltantes con la media, mediana o un valor calculado.  

```r
# Eliminar registros con datos faltantes
taxi_nyc_clean <- taxi_nyc %>%
  filter(!is.na(fare_amount) & !is.na(pickup_datetime))

# Imputar valores faltantes (ejemplo: rellenar 'passenger_count' con la media)
taxi_nyc_clean <- taxi_nyc_clean %>%
  mutate(passenger_count = ifelse(is.na(passenger_count), mean(passenger_count, na.rm = TRUE), passenger_count))
```

---

### 🟠 **2.2. Corrección de Inconsistencias**
Los datos inconsistentes suelen ocurrir debido a errores humanos, problemas técnicos o diferentes formatos de entrada.

### 🔎 **Detección de inconsistencias**
Por ejemplo, una **tarifa negativa** en un viaje es un error evidente.

```r
# Detectar registros con tarifas negativas
taxi_nyc %>%
  filter(fare_amount < 0) %>%
  count()
```

### 🔧 **Corrección de inconsistencias**
- Eliminar registros con valores claramente erróneos.  
- Sustituir valores incorrectos con cálculos estimados.  

```r
# Eliminar registros con tarifas negativas
taxi_nyc_clean <- taxi_nyc %>%
  filter(fare_amount >= 0)
```

---

### 🟠 **2.3. Detección y Eliminación de Duplicados**
Los datos duplicados suelen ocurrir por errores en la ingesta o procesos de carga.

### 🔎 **Detección de duplicados**
```r
# Detectar registros duplicados
taxi_nyc %>%
  group_by(pickup_datetime, dropoff_datetime, trip_distance) %>%
  summarise(total_registros = n()) %>%
  filter(total_registros > 1)
```

### 🔧 **Eliminación de duplicados**
```r
# Eliminar duplicados
taxi_nyc_clean <- taxi_nyc %>%
  distinct(pickup_datetime, dropoff_datetime, trip_distance, .keep_all = TRUE)
```

---

## 🔹 **3. Transformaciones Avanzadas**

Las transformaciones avanzadas permiten estructurar y enriquecer los datos para obtener información más valiosa.

### 🟠 **3.1. Filtrado de Datos**
El filtrado selecciona únicamente los registros que cumplen ciertos criterios.

**Ejemplo:** Filtrar viajes que recorrieron más de 10 millas.

```r
taxi_nyc_clean <- taxi_nyc %>%
  filter(trip_distance > 10)
```

---

### 🟠 **3.2. Joins (Uniones)**
Las uniones permiten combinar tablas basándose en una clave común.

**Ejemplo:** Unir el dataset de taxis con una tabla que contenga zonas de recogida.

```r
zonas <- data.frame(
  zone_id = c(1, 2, 3),
  zone_name = c("Manhattan", "Brooklyn", "Queens")
)

taxi_nyc_zones <- taxi_nyc %>%
  left_join(zonas, by = c("PULocationID" = "zone_id"))
```

---

### 🟠 **3.3. Agregaciones**
Las agregaciones resumen la información, calculando métricas clave.

**Ejemplo:** Calcular la tarifa promedio por día.

```r
taxi_nyc_clean %>%
  group_by(date = as.Date(pickup_datetime)) %>%
  summarise(
    tarifa_promedio = mean(fare_amount, na.rm = TRUE)
  ) %>%
  arrange(desc(tarifa_promedio)) %>%
  collect()
```

---

## 🔹 **4. Uso de Pipelines en Spark**

Los **pipelines** permiten encadenar múltiples transformaciones y simplificar el flujo de trabajo en Spark.

### 🔎 **Pipeline básico con `sparklyr`**
```r
taxi_pipeline <- taxi_nyc %>%
  filter(fare_amount > 0) %>%
  mutate(
    total_fare = fare_amount + tip_amount,
    trip_day = weekdays(pickup_datetime)
  ) %>%
  group_by(trip_day) %>%
  summarise(total_viajes = n())
```

---

## 🔹 **5. Ejercicios Prácticos**

### 🟩 **Ejercicio 1 – Limpieza completa del dataset**
**Objetivo:**  
- Cargar el dataset NYC Taxis en formato **Parquet**.  
- Eliminar registros con tarifas negativas.  
- Eliminar registros duplicados.  
- Imputar los valores faltantes en `passenger_count` usando la media (opcional).  

**Código inicial:**

```r

```

---

### 🟩 **Ejercicio 2 – Análisis de propinas por día de la semana**
**Objetivo:**  
- Cargar el dataset NYC Taxis.  
- Calcular la propina media (`tip_amount`) para cada día de la semana.  
- Determinar qué día de la semana se deja mayor propina promedio.  

**Código inicial:**

```r

```
