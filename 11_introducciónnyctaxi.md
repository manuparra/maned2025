## 📌 **¿Qué es Big Data y por qué gestionarlo?**

**Big Data** se refiere al manejo y análisis de volúmenes extremadamente grandes de datos, que superan las capacidades de las herramientas tradicionales. Este fenómeno se caracteriza comúnmente mediante el modelo de las **"5 V"**:

- **Volumen:** La cantidad de datos generados es enorme (terabytes o petabytes).
- **Velocidad:** Los datos se generan de manera continua y muy rápida (datos en tiempo real o streaming).
- **Variedad:** Los datos pueden ser estructurados (bases de datos), semi-estructurados (JSON, XML) y no estructurados (texto, imágenes, videos).
- **Veracidad:** Los datos pueden tener calidad variable o inconsistencias que requieren validación y limpieza.
- **Valor:** Capacidad de extraer información útil y estratégica para tomar decisiones.

---

## 📌 **¿Por qué las empresas necesitan Big Data?**

Las empresas actuales generan grandes cantidades de información procedente de múltiples fuentes, como sistemas transaccionales, redes sociales, sensores IoT, etc. Gestionar eficientemente estos datos proporciona ventajas estratégicas clave:

- **Análisis predictivo avanzado:** Por ejemplo, predecir demanda de productos, identificar fraudes o anticipar la rotación de clientes.
- **Toma de decisiones basada en datos:** Permite decisiones rápidas e informadas, mejorando resultados comerciales y operativos.
- **Optimización de procesos:** Desde rutas logísticas hasta la gestión del inventario en tiempo real.

---

## 📌 **Diferencias entre Big Data y manejo tradicional**

| Característica | Manejo Tradicional | Big Data |
|----------------|--------------------|----------|
| Volumen de datos | Gigabytes | Terabytes o Petabytes |
| Velocidad | Moderada o baja | Muy alta, en tiempo real |
| Variedad | Datos estructurados | Datos estructurados y no estructurados |
| Herramientas | Bases SQL tradicionales | Hadoop, Spark, NoSQL |
| Almacenamiento | Centralizado | Distribuido (HDFS, S3, Cloud Storage) |

---

## 📌 **Ejemplo práctico introductorio: NYC Taxi dataset**

El dataset público de taxis de Nueva York contiene información detallada sobre millones de viajes en taxi cada mes, incluyendo aspectos como punto de recogida, destino, duración, costos y propinas. Este dataset cumple perfectamente con las características Big Data:

- **Volumen:** Millones de registros por mes.
- **Velocidad:** Continuamente actualizado cada mes con nuevos datos.
- **Variedad:** Incluye datos estructurados (costos, duración), semi-estructurados (ubicaciones GPS), e incluso texto (metadatos, comentarios).
- **Veracidad:** Puede contener errores, datos faltantes o inconsistentes.
- **Valor:** Útil para análisis predictivos, optimización de tarifas, rutas, y análisis del comportamiento de usuarios.

---

## 🔹 **Descripción del Dataset**

Este dataset es ofrecido públicamente por la [New York City Taxi and Limousine Commission (TLC)](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page), que regula y publica información mensual sobre millones de viajes realizados por taxis amarillos en la ciudad de Nueva York.

El dataset incluye información de cada viaje realizado, como:

- **pickup_datetime y dropoff_datetime**: fecha y hora de inicio y fin del viaje.
- **pickup y drop-off locations**: ubicaciones geográficas de recogida y llegada.
- **passenger_count**: Número de pasajeros.
- **trip_distance**: Distancia recorrida durante el viaje (en millas).
- **fare_amount**: Tarifa básica del viaje.
- **tip_amount**: Cantidad de propina dada por el cliente.
- **payment_type**: Tipo de pago (efectivo, tarjeta, etc.).

---

## 🔹 **¿Por qué utilizamos este dataset?**

El dataset cumple con las características principales del Big Data:

- **Volumen**: Millones de registros (entre 8-10 millones por mes aproximadamente).
- **Velocidad**: Generación continua mensual (actualización constante).
- **Variedad**: Incluye datos estructurados, y adicionalmente podemos complementarlo con datos semi-estructurados o no estructurados (comentarios de usuarios, eventos externos, etc.).
- **Veracidad**: Ofrece datos públicos regulados y fiables, aunque siempre puede requerir tareas adicionales de limpieza.

---

## 🔹 **Enlace para descargar los datos (ejemplo mes de Enero 2023)**

Puedes descargar el dataset de taxis de Enero de 2023 desde aquí en formato Parquet (formato eficiente para Big Data):

- 🔗 [Yellow Taxi Trip Data – Enero 2023 (Parquet)](https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.parquet)

*Nota*: Mensualmente se actualiza el dataset en la página oficial de la TLC [enlace oficial aquí](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

Puedes descargar el dataset completo directamente desde:

- Página oficial de TLC NYC:
    https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page

- Enlaces directos para el curso (Enero 2023):
  - Formato CSV (~600 MB): https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.csv
  - Formato Parquet (~150 MB, comprimido y eficiente para Big Data): https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.parquet


---

## 🔹 **Acceso inicial con R y Apache Spark (sparklyr)**

Veamos cómo cargar rápidamente estos datos en Spark usando **`sparklyr` desde R**:

```r
# Cargar librerías
library(sparklyr)
library(dplyr)

# Conexión a Apache Spark local
sc <- spark_connect(master = "local")

# Cargar el dataset de taxis NYC desde URL (parquet)
taxi_nyc <- spark_read_parquet(sc, 
                               name = "taxi_nyc",
                               path = "https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.parquet")

# Mostrar estructura básica
glimpse(taxi_nyc)

# Visualizar primeras filas
head(taxi_nyc)
```

---

## 🔹 **Ejemplos prácticos y usos empresariales reales del dataset**

Este dataset se utiliza frecuentemente en entornos empresariales para:

- **Predecir demanda en transporte urbano**: Ajustar dinámicamente precios según demanda y horario.
- **Analizar patrones de tráfico**: Optimizar rutas, mejorando tiempos de trayecto y disminuyendo costos operativos.
- **Identificar clientes más rentables**: Perfilamiento para programas de fidelización y marketing dirigido.
- **Detección de anomalías**: Fraudes, viajes irregulares, posibles errores en tarifas, etc.

---

## 🔹 **Código de exploración básica inicial en R/Spark**

A continuación, exploramos de forma básica algunos aspectos fundamentales del dataset:

```r
# Número total de registros
taxi_nyc %>% summarise(total_viajes = n()) %>% collect()

# Campos (columnas) disponibles
colnames(taxi_nyc)

# Muestra inicial del dataset
taxi_nyc %>% head(10) %>% collect()
```

---

## 🔹 **Ejercicios iniciales sugeridos a los alumnos**

Estos ejercicios permiten comenzar la familiarización inicial con el dataset:

**Ejercicio 1:**  
- Cargar datos usando sparklyr (ver sección previa)
- Explorar columnas y tipos de datos

```r
taxi_nyc %>% glimpse()
```

**Ejercicio 2:**  
- Determinar tarifa promedio, propina promedio y distancia promedio de todos los viajes.

```r
taxi_nyc %>% summarise(
  tarifa_media = mean(fare_amount, na.rm=TRUE),
  propina_media = mean(tip_amount, na.rm=TRUE),
  distancia_media = mean(trip_distance, na.rm=TRUE)
) %>% collect()
```


---

## 📌 **Ejercicios propuestos**

Aquí tienes dos ejercicios introductorios para poner en práctica los conceptos explicados usando el dataset de NYC Taxis:

### 🟩 **Ejercicio 1 – Exploración inicial del dataset**

Carga el dataset usando Spark en R, explora los datos iniciales, e identifica:

- ¿Cuántos registros tiene el dataset?
- ¿Qué campos (columnas) están disponibles?
- ¿Qué tipo de datos predominan (estructurados, semi-estructurados)?


```r

```

---

### 🟩 **Ejercicio 2 – Análisis básico del comportamiento de los pasajeros**

Utilizando la información cargada anteriormente, responde:

- ¿Cuál es la tarifa promedio (`fare_amount`) de los viajes realizados en enero de 2023?
- ¿Cuál es la propina promedio (`tip_amount`)?
- ¿Qué porcentaje de viajes pagó propina superior a $5?


```r

```

