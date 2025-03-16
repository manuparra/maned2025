# 🔵 **Tema 6.6 – Visualización de datos en entornos Big Data**

La **visualización de datos** es una parte fundamental en el análisis de **Big Data**, ya que permite comprender patrones, tendencias y anomalías en grandes volúmenes de información. Sin embargo, al manejar datos masivos, la visualización presenta diversos desafíos que requieren técnicas avanzadas y herramientas específicas para ser efectivas.

En esta sección aprenderás a:

✅ Entender los principales desafíos de la visualización en Big Data  
✅ Utilizar herramientas específicas de **R** para visualizar grandes volúmenes de datos  
✅ Aplicar técnicas avanzadas como muestreo, agregaciones y visualización por bloques  
✅ Crear dashboards interactivos escalables para facilitar la toma de decisiones  
✅ Conectar R con herramientas externas como **Power BI**, **Tableau** y **Grafana**  
✅ Implementar ejemplos prácticos con el dataset real de **NYC Taxi Trips**  
✅ Resolver problemas reales con 2 ejercicios prácticos  

---

# 🔹 **1. El Problema de la Visualización en Big Data**

Visualizar grandes volúmenes de datos presenta importantes desafíos que requieren soluciones específicas.

### 🚨 **Principales Desafíos**
1. **Escalabilidad:** Gráficos tradicionales como los de `ggplot2` o `plotly` pueden colapsar si intentan mostrar millones de puntos en un mismo gráfico.  
2. **Rendimiento:** El tiempo de renderizado puede aumentar drásticamente si no se optimiza el flujo de datos.  
3. **Ruido Visual:** Con grandes volúmenes de datos, identificar patrones relevantes se complica debido al exceso de información.  
4. **Interactividad:** Las herramientas de visualización deben permitir filtrar y explorar datos masivos de forma interactiva para obtener insights efectivos.

---

### 🔎 **Soluciones para abordar el problema**
Para abordar estos desafíos, se pueden aplicar distintas estrategias:

✅ **Agregaciones:** Resumir los datos por categorías o intervalos.  
✅ **Muestreo:** Visualizar una muestra representativa del conjunto completo.  
✅ **Técnicas avanzadas de visualización escalable:** Como `geom_hex()`, `geom_bin2d()` o `bigvis`.  
✅ **Dashboards interactivos:** Para explorar grandes volúmenes de datos de forma eficiente.  
✅ **Conexión con herramientas externas:** Como **Power BI**, **Tableau** o **Grafana**.

---

# 🔹 **2. Visualización Escalable en R con `ggplot2` y `plotly`**

Las herramientas clásicas como `ggplot2` y `plotly` son ideales para gráficos exploratorios en R. Sin embargo, requieren técnicas específicas para manejar grandes volúmenes de datos de forma eficiente.

---

## 📌 **2.1. Visualización básica con `ggplot2`**
```r
library(sparklyr)
library(dplyr)
library(ggplot2)

# Conectar a Spark
sc <- spark_connect(master = "local")

# Cargar el dataset de NYC Taxis
taxi_nyc <- spark_read_parquet(sc, name = "taxi_nyc", path = "hdfs://ruta/nyc_taxi.parquet")

# Gráfico básico (NO ESCALABLE)
taxi_nyc %>%
  sample_n(10000) %>%  # Muestra de 10.000 registros
  collect() %>%
  ggplot(aes(x = trip_distance, y = fare_amount)) +
  geom_point(alpha = 0.5) +
  labs(title = "Distancia del viaje vs Tarifa (Muestra de 10k registros)")
```

---

## 📌 **2.2. Visualización eficiente usando agregaciones (`geom_bin2d()` y `geom_hex()`)**

Estas técnicas son ideales para visualizar **millones de registros** mediante resúmenes visuales.

### 🔎 **Ejemplo con `geom_bin2d()`**
```r
taxi_nyc %>%
  group_by(distance_range = round(trip_distance, 1)) %>%
  summarise(tarifa_media = mean(fare_amount, na.rm = TRUE)) %>%
  collect() %>%
  ggplot(aes(x = distance_range, y = tarifa_media)) +
  geom_bin2d(bins = 50) +
  labs(title = "Resumen de tarifas en función de la distancia (geom_bin2d)")
```

---

### 🔎 **Ejemplo con `geom_hex()`**
`geom_hex()` crea hexágonos para agrupar datos y reducir el impacto visual del ruido.

```r
taxi_nyc %>%
  sample_n(100000) %>%  # Muestra de 100.000 registros
  collect() %>%
  ggplot(aes(x = trip_distance, y = fare_amount)) +
  geom_hex(bins = 40) +
  labs(title = "Distribución de tarifas en función de la distancia (geom_hex)")
```

---

## 📌 **2.3. Visualización escalable con `dbplot` (Recomendado para Big Data en Spark)**
`dbplot` permite crear gráficos sin necesidad de cargar los datos completos en memoria.

### 🔎 **Ejemplo con `dbplot`**
```r
library(dbplot)

# Distribución de tarifas en función de la distancia
taxi_nyc %>%
  dbplot_histogram(trip_distance, binwidth = 1) +
  labs(title = "Distribución de Distancias (dbplot)")
```

---

## 📌 **2.4. Visualización escalable e interactiva con `trelliscopejs`**
`trelliscopejs` permite dividir los datos en facetas interactivas para explorar grandes volúmenes de información.

### 🔎 **Ejemplo con `trelliscopejs`**
```r
library(trelliscopejs)

# Gráfico interactivo por zonas de recogida
taxi_nyc %>%
  sample_n(50000) %>%
  collect() %>%
  ggplot(aes(x = trip_distance, y = fare_amount)) +
  geom_point() +
  facet_trelliscope(~ PULocationID, nrow = 2, ncol = 3)
```

---

# 🔹 **3. Visualización con herramientas externas (Power BI, Tableau y Grafana) [Opcional]**

Aunque R es muy potente en visualización, las herramientas externas como **Power BI**, **Tableau** y **Grafana** permiten trabajar de forma más visual e interactiva con datos masivos.

## 📌 **3.1. Conexión de R con Power BI**
Power BI permite ejecutar scripts de R para crear gráficos avanzados:

1. En Power BI, inserta un **visual de R**.  
2. Escribe el siguiente script de R para visualizar los datos:

```r
library(ggplot2)

ggplot(data, aes(x = trip_distance, y = fare_amount)) +
  geom_point(alpha = 0.3) +
  labs(title = "Visualización de datos en Power BI usando R")
```

---

## 📌 **3.2. Conexión de R con Tableau**
- Tableau permite conectar R usando el paquete **Rserve** para procesar datos directamente desde R.

**Ejemplo básico en Tableau:**
```r
# Activar el servidor R para Tableau
library(Rserve)
Rserve()
```

---

# 🔵 **Visualización Avanzada en Big Data usando Datos Geoespaciales (GIS) en el dataset NYC Taxi Trips**

El dataset de **NYC Taxi Trips** incluye información geoespacial muy valiosa, como las coordenadas GPS de los puntos de recogida (`pickup_latitude`, `pickup_longitude`) y destino (`dropoff_latitude`, `dropoff_longitude`). Estos datos permiten realizar visualizaciones avanzadas para analizar patrones espaciales, flujos de movilidad, zonas con mayor actividad, etc.

En esta sección aprenderás a:

✅ Visualizar datos geoespaciales en R utilizando herramientas avanzadas como `leaflet`, `sf`, `ggmap` y `mapdeck`  
✅ Representar puntos geográficos con información adicional (tarifas, distancia, etc.)  
✅ Crear mapas de calor para identificar zonas con mayor densidad de viajes  
✅ Aplicar mapas dinámicos e interactivos para facilitar el análisis visual  
✅ Resolver problemas reales con 2 ejercicios prácticos  

---

# 🔹 **1. Introducción a la Visualización Geoespacial en R**

Las herramientas más recomendadas para visualizar datos geoespaciales en R incluyen:

| Herramienta | Descripción | Escalabilidad |
|---------------|--------------|----------------|
| **`leaflet`** | Mapas interactivos con múltiples capas. | ⭐⭐⭐⭐ |
| **`sf`** (Simple Features) | Manipulación avanzada de datos geoespaciales. | ⭐⭐⭐ |
| **`ggmap`** | Visualización rápida basada en Google Maps. | ⭐⭐⭐ |
| **`mapdeck`** | Visualización optimizada para grandes volúmenes de datos. | ⭐⭐⭐⭐⭐ |

---

# 🔹 **2. Visualización Geoespacial con `leaflet`**

La librería **`leaflet`** es altamente recomendada para crear mapas interactivos en R.

### 🔎 **2.1. Visualización básica con `leaflet`**
```r
library(leaflet)
library(dplyr)

# Cargar el dataset en Spark
taxi_nyc <- spark_read_parquet(sc, name = "taxi_nyc", path = "hdfs://ruta/nyc_taxi.parquet")

# Muestra aleatoria para visualizar (evitar sobrecarga)
taxi_sample <- taxi_nyc %>%
  sample_n(5000) %>%
  collect()

# Mapa básico con puntos de recogida
leaflet(data = taxi_sample) %>%
  addTiles() %>%
  addCircleMarkers(
    lng = ~pickup_longitude,
    lat = ~pickup_latitude,
    radius = 2,
    color = "blue",
    opacity = 0.5
  )
```

---

### 🔎 **2.2. Visualización avanzada con `leaflet` (marcadores coloreados según tarifa)**
```r
leaflet(data = taxi_sample) %>%
  addTiles() %>%
  addCircleMarkers(
    lng = ~pickup_longitude,
    lat = ~pickup_latitude,
    radius = ~sqrt(fare_amount),
    color = ~ifelse(fare_amount > 50, "red", "green"),
    popup = ~paste("Tarifa: $", fare_amount),
    opacity = 0.7
  )
```

---

# 🔹 **3. Visualización Geoespacial con `sf` (Simple Features)**

El paquete **`sf`** permite manipular datos geoespaciales utilizando una estructura optimizada y eficiente para grandes volúmenes de datos.

## 📌 **3.1. Convertir datos a formato geoespacial (`sf`)**
```r
library(sf)

# Convertir dataset en formato geoespacial
taxi_sf <- taxi_sample %>%
  st_as_sf(coords = c("pickup_longitude", "pickup_latitude"), crs = 4326)  # EPSG: 4326 (sistema GPS)
```

---

## 📌 **3.2. Visualización de puntos GPS con `ggplot2` + `sf`**
```r
library(ggplot2)

ggplot(data = taxi_sf) +
  geom_sf(aes(color = fare_amount), size = 1, alpha = 0.5) +
  scale_color_viridis_c() +
  labs(title = "Puntos de recogida con escala de tarifas")
```

---

# 🔹 **4. Visualización de Mapas de Calor con `ggmap`**

Los mapas de calor permiten visualizar zonas con mayor densidad de viajes o actividad comercial.

## 📌 **4.1. Crear un mapa de calor básico**
```r
library(ggmap)

# Descargar el mapa base de NYC
nyc_map <- get_map(location = "New York City", zoom = 12)

# Visualizar el mapa de calor
ggmap(nyc_map) +
  geom_density2d(data = taxi_sample, aes(x = pickup_longitude, y = pickup_latitude), size = 0.3) +
  stat_density2d(
    data = taxi_sample,
    aes(x = pickup_longitude, y = pickup_latitude, fill = ..level..),
    geom = "polygon",
    alpha = 0.5
  ) +
  scale_fill_viridis_c() +
  labs(title = "Mapa de calor - Puntos de recogida en NYC")
```

---

# 🔹 **5. Técnicas avanzadas de visualización para Big Data**

Cuando los volúmenes de datos son extremadamente grandes, las siguientes técnicas permiten optimizar el proceso:

✅ **Downsampling:** Seleccionar una muestra representativa en lugar de usar el dataset completo.  
✅ **Aggregated Grids:** Crear cuadrículas o hexágonos para resumir los datos.  
✅ **Streaming Data:** Utilizar flujos de datos en tiempo real para evitar almacenar todos los datos en memoria.  
✅ **Visualización distribuida:** Utilizar herramientas como **`sparklyr`** para filtrar y transformar los datos antes de graficarlos.  

---

# 🔹 **7. Ejercicios Prácticos**

### 🟩 **Ejercicio 1 – Visualización de las Zonas con Mayor Actividad**
**Objetivo:**  
- Crear un **mapa de calor** que muestre las zonas de NYC con mayor actividad de recogida de taxis.  
- Utilizar `leaflet` o `ggmap` para representar los datos.  

**Código:**
```r

```

---

### 🟩 **Ejercicio 2 – Visualización de la Tarifa Promedio por Distrito**
**Objetivo:**  
- Visualizar la tarifa promedio por distrito usando `leaflet`.  
- Colorear las zonas según el costo promedio del viaje.  

**Código:**
```r

```


### 🟩 **Ejercicio 3 – Visualización de tarifas por horas del día**
**Objetivo:**  
- Cargar el dataset de taxis en formato **Parquet**.  
- Crear un gráfico que muestre la tarifa promedio por cada hora del día.  

**Código :**
```r

```

---

### 🟩 **Ejercicio 4 – Visualización de propinas según distancia**
**Objetivo:**  
- Crear un gráfico de dispersión que relacione la distancia del viaje con la propina (`tip_amount`).  
- Aplicar `geom_hex()` para evitar ruido visual.  

**Código :**
```r

```

