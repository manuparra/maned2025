# 📌 **Tema 6.5 – Manejo de datos en entornos Big Data**

---

## 🔹 **1. Introducción al Manejo de Datos en Big Data**

El manejo de datos en entornos **Big Data** consiste en almacenar, procesar, transformar y analizar grandes volúmenes de datos de diversas fuentes para obtener información valiosa, soportando así la toma de decisiones en la empresa. Este manejo implica técnicas y herramientas que permiten trabajar con datos cuyo volumen, velocidad y variedad exceden las capacidades de sistemas tradicionales.

### **¿Por qué gestionar Big Data es importante?**

El crecimiento exponencial de datos generados diariamente, especialmente en sectores como comercio electrónico, finanzas, salud o transporte, requiere herramientas y métodos avanzados. El manejo eficiente permite convertir estos datos masivos en conocimiento útil para mejorar:

- **Eficiencia operativa**
- **Análisis predictivo avanzado**
- **Mejora en la toma de decisiones estratégicas**

### **Diferencias clave con respecto al manejo tradicional**

Mientras las bases de datos tradicionales trabajan con datos estructurados, estáticos y manejables por sistemas limitados, el manejo Big Data tiene las siguientes características diferenciadoras:

- **Datos distribuidos:** Almacenamiento en múltiples servidores o clústeres.
- **Procesamiento paralelo:** Uso de técnicas como MapReduce o Spark para acelerar cálculos.
- **Flexibilidad:** Acepta datos estructurados, semi-estructurados (logs, JSON) o no estructurados (vídeos, imágenes, texto).

**Ejemplo práctico con dataset de taxis de Nueva York:**

Un dataset típico de viajes de taxi en NY contiene millones de registros generados continuamente, incluyendo datos como ubicación GPS, duración, distancia, costos, y métodos de pago. Este volumen y diversidad exige un tratamiento Big Data para ser útil a escala empresarial.

---

## 🔹 **2. Fuentes de Datos**

En Big Data, las fuentes de datos pueden clasificarse según su estructura en:

### **Datos estructurados**

Son datos que se almacenan fácilmente en tablas con esquemas predefinidos. Por ejemplo, una base de datos SQL tradicional con información de clientes o ventas.

**Ejemplo en dataset taxis NY:**  
Datos como el **ID del viaje**, **tarifa**, **propina**, y **método de pago** suelen venir en formato estructurado, fácilmente representables en tablas.

| trip_id | fare_amount | tip_amount | payment_type |
|---------|-------------|------------|--------------|
| 12345   | 15.00       | 2.00       | Credit card  |
| 12346   | 22.50       | 3.50       | Cash         |

### **Datos semi-estructurados**

Estos datos tienen estructura variable o flexible, generalmente representados en formatos como JSON o XML, comunes en APIs, logs y redes sociales.

**Ejemplo en dataset taxis NY (JSON):**
```json
{
  "trip_id": 12345,
  "pickup": {"lat": 40.712776, "lon": -74.005974},
  "dropoff": {"lat": 40.730610, "lon": -73.935242},
  "timestamp": "2023-01-05T08:45:30Z"
}
```

Este tipo de dato requiere tratamiento especial para analizar y extraer información útil.

### **Datos no estructurados**

Información sin un formato claro y estructurado, como imágenes, vídeos, audios, o texto libre. En el contexto de taxis, pueden ser comentarios en redes sociales o imágenes captadas por cámaras de seguridad.

**Ejemplo en contexto de taxis:**
- Comentarios de usuarios en redes sociales:
  - "Tuve una gran experiencia con el taxi #NYC, ¡conductor amable y rápido!"
  - Imágenes captadas por cámaras en calles para monitorear el tráfico.

Estos datos necesitan técnicas avanzadas (NLP, análisis de imagen) para extraer información útil.

---

### 🔹 **Ejemplo de código R/Spark de acceso inicial al dataset de taxis (CSV estructurado)**

Este ejemplo muestra cómo leer los datos estructurados (formato CSV) desde Apache Spark con R mediante la librería `sparklyr`:

```r
library(sparklyr)
library(dplyr)

# Conectar a Spark
sc <- spark_connect(master = "local[*]")

# Leer el archivo Parquet correctamente
taxis_nyc <- spark_read_parquet(
  sc, 
  name = "taxis_nyc", 
  path = "https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.parquet"
)

# Visualización de los primeros registros
taxis_nyc %>%
  head(5) %>%
  collect()
```

Este dataset proviene de la **Taxi & Limousine Commission de Nueva York**, disponible públicamente y ampliamente utilizado en análisis Big Data.

---

### 🔹 **Ejemplo práctico del manejo de datos semi-estructurados en R**

Si los datos semi-estructurados (JSON) se obtienen de una API, podríamos procesarlos así:

```r
library(jsonlite)

# Ejemplo de datos semi-estructurados (JSON)
json_data <- '{
  "trip_id": 12345,
  "pickup": {"lat": 40.712776, "lon": -74.005974},
  "dropoff": {"lat": 40.730610, "lon": -73.935242},
  "timestamp": "2023-01-05T08:45:30Z"
}'

# Convertir JSON a lista de R
datos <- fromJSON(json_data)

# Extraer elementos específicos
pickup_lat <- datos$pickup$lat
pickup_lon <- datos$pickup$lon

pickup_lat
pickup_lon
```

---

### 🔹 **Aplicación en entorno empresarial**

En el contexto empresarial, identificar correctamente las fuentes de datos y gestionarlas eficientemente permite:

- **Personalizar la experiencia del cliente**: Con análisis predictivo sobre hábitos de consumo.
- **Optimizar recursos**: Prediciendo la demanda de servicios como transporte, basado en histórico.
- **Mejorar la toma de decisiones**: Utilizando datos en tiempo real (streaming).

Por ejemplo, empresas como Uber y Lyft utilizan estos tipos de datos (estructurados, semi-estructurados y no estructurados) para optimizar rutas, precios dinámicos y marketing personalizado.
