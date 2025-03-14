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

### 🔹 **Ejemplo básico de carga de datos en R/Spark:**

Veamos cómo conectar R con Apache Spark y cargar inicialmente el dataset de taxis de NYC (enero 2023):

```r
# Cargar las librerías necesarias
library(sparklyr)
library(dplyr)

# Iniciar conexión local con Spark
sc <- spark_connect(master = "local")

# Leer datos del dataset público (en formato Parquet, muy común en Big Data)
taxis_nyc <- spark_read_parquet(
  sc,
  name = "taxis_nyc",
  path = "https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-01.parquet"
)

# Ver estructura básica de los datos
taxis_nyc %>% glimpse()

# Consultar algunos registros
taxis_nyc %>% head(5)
```

Este sencillo ejemplo ya nos muestra el manejo inicial de grandes volúmenes de datos usando herramientas especializadas para Big Data (Spark).

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

