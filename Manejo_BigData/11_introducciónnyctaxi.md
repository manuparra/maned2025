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

# 🔎 **`collect()` en Apache Spark con `sparklyr` en R**  

El método **`collect()`** es una función **clave** en el ecosistema de **Apache Spark**, especialmente en el contexto del análisis de datos masivos utilizando R y la librería **`sparklyr`**. Su uso es crucial para entender cómo se manejan los datos en entornos distribuidos.

---

## 🔹 **¿Qué es `collect()`?**  
En Apache Spark, el método **`collect()`** se utiliza para **extraer datos desde un clúster de Spark hacia el entorno local de R**. En otras palabras, **`collect()`** convierte los datos almacenados en Spark (en un entorno distribuido) en un **data frame** estándar de R, permitiendo su manipulación, análisis y visualización dentro de R.

---

## 🔹 **¿Por qué se necesita `collect()` en Spark?**

El diseño de **Spark** se basa en el principio de **computación distribuida**, lo que significa que los datos se encuentran repartidos en múltiples nodos del clúster.  

Por esta razón:  
✅ Cuando ejecutas una operación en Spark (como `filter()`, `group_by()`, etc.), el resultado se mantiene en el entorno de Spark y **no se transfiere automáticamente** a R.  
✅ La función **`collect()`** se usa para **transferir los resultados a la memoria local** en R.  

---

## 🔹 **¿Cómo funciona `collect()`?**

La función **`collect()`** realiza lo siguiente:

✅ Envía una solicitud a Spark para recopilar los resultados de todas las particiones del clúster.  
✅ Los datos se trasladan al entorno local de R como un **data frame estándar**.  
✅ Es ideal para obtener subconjuntos de datos pequeños o resultados finales que puedan analizarse directamente en R.  

> ⚠️ **¡Importante!**  
> Dado que `collect()` transfiere los datos completos a la memoria local, no es recomendable para conjuntos de datos muy grandes, ya que puede agotar la memoria del sistema.

---

## 🔹 **Sintaxis básica de `collect()`**
```r
library(sparklyr)
library(dplyr)

# Conectar a Spark
sc <- spark_connect(master = "local")

# Cargar un dataset en Spark
taxi_nyc <- spark_read_parquet(sc, name = "taxi_nyc", path = "hdfs://ruta/dataset.parquet")

# Filtrar los datos y luego recogerlos en R
datos_filtrados <- taxi_nyc %>%
  filter(fare_amount > 10) %>%
  select(pickup_datetime, fare_amount) %>%
  collect()

# Ver el resultado
head(datos_filtrados)
```

---

## 🔹 **Ejemplos Prácticos de `collect()`**

### 📋 **1. Obtener un subconjunto de datos**
El uso más común de `collect()` es extraer un pequeño subconjunto de datos para su inspección.

```r
# Obtener 100 registros aleatorios del dataset
sample_data <- taxi_nyc %>%
  sample_n(100) %>%
  collect()

# Visualización rápida
head(sample_data)
```

---

### 📋 **2. Agregar resultados resumidos**
En lugar de traer todos los registros, podemos calcular un resumen y luego usar `collect()` para transferir el resultado.

```r
# Tarifa promedio por tipo de pago
tarifa_promedio <- taxi_nyc %>%
  group_by(payment_type) %>%
  summarise(avg_fare = mean(fare_amount, na.rm = TRUE)) %>%
  collect()

# Visualización del resumen
print(tarifa_promedio)
```

---

### 📋 **3. Visualización directa de resultados**
Como Spark no genera gráficos directamente, una estrategia eficaz es:

1. Realizar la transformación en Spark.  
2. Recoger el resultado en R con `collect()`.  
3. Utilizar herramientas como `ggplot2` para graficar.  

---

### 📋 **4. Combinar `collect()` con `arrange()`**
Para obtener un listado ordenado desde Spark.

```r
# Top 10 viajes más costosos
viajes_costosos <- taxi_nyc %>%
  arrange(desc(fare_amount)) %>%
  select(pickup_datetime, fare_amount) %>%
  head(10) %>%
  collect()

print(viajes_costosos)
```

---

## 🔹 **Buenas Prácticas al usar `collect()`**

✅ **Filtra primero, luego usa `collect()`** → Evita traer datos innecesarios.  
✅ **Usa `head()` antes de `collect()`** para obtener solo una muestra pequeña.  
✅ **Asegúrate de no ejecutar `collect()` en datasets masivos** sin aplicar previamente filtros o agregaciones.  
✅ **Prefiere `compute()`** en lugar de `collect()` si deseas crear un nuevo dataset en Spark sin descargarlo.

---

## 🔹 **Alternativa a `collect()`: `compute()`**

La función **`compute()`** crea un **dataset temporal en Spark**, lo que resulta muy útil cuando se desea almacenar resultados parciales en el entorno distribuido sin traerlos a R.

**Ejemplo:**
```r
# Guardar un dataset temporal en Spark
datos_temporales <- taxi_nyc %>%
  filter(fare_amount > 50) %>%
  compute(name = "viajes_costosos")

# Visualizar directamente desde Spark sin descargar
datos_temporales %>%
  summarise(total_viajes = n()) %>%
  collect()
```

---

## 🔹 **¿Cuándo usar `collect()` y cuándo `compute()`?**

| Característica   | `collect()`                         | `compute()`                         |
|------------------|-------------------------------------|-------------------------------------|
| **Función principal** | Extrae los datos al entorno local de R  | Crea un dataset temporal dentro de Spark  |
| **Uso recomendado** | Para conjuntos de datos **pequeños** o resultados finales | Para conjuntos de datos **grandes** que se seguirán procesando en Spark |
| **Ventaja clave** | Permite análisis y visualización directa en R  | Mantiene los datos en el entorno distribuido de Spark |
| **Riesgo** | Puede agotar la memoria si se usa en conjuntos de datos grandes | No sobrecarga la memoria local |

---

> 🧠 **Regla de oro:** Filtra, agrega o resume en Spark antes de usar `collect()` para evitar problemas de memoria. 🚀

---

# 🚀 **Funciones Clave para la Gestión de Memoria y el Procesamiento Distribuido en Apache Spark con `sparklyr`**

Cuando trabajamos con grandes volúmenes de datos en **Apache Spark**, una gestión eficiente de la memoria es crucial para garantizar el rendimiento y evitar errores por falta de recursos. Además, comprender las funciones clave para el **procesamiento distribuido** en Spark ayuda a optimizar los flujos de trabajo y aprovechar al máximo su arquitectura escalable.

En esta guía, te mostraré las principales funciones que optimizan el uso de memoria, el manejo de datos en entornos distribuidos y la mejora del rendimiento en Spark usando **`sparklyr`** en R.

---

## 🔹 **1. `compute()` – Crear un Dataset Temporal en Spark**
La función **`compute()`** es una alternativa eficiente a `collect()` cuando se necesita almacenar un dataset temporal dentro de Spark sin mover los datos al entorno local de R.

### 🔎 **¿Por qué usar `compute()`?**
✅ Permite guardar el resultado de una operación en memoria dentro del clúster de Spark.  
✅ Evita la transferencia de datos masivos a R.  
✅ Útil cuando se desean reutilizar resultados en varias operaciones posteriores.  

### 🔎 **Ejemplo de uso**
```r
# Crear un dataset temporal en Spark
datos_temporales <- taxi_nyc %>%
  filter(fare_amount > 20) %>%
  compute(name = "datos_temporales")

# Consultar los registros directamente en Spark
datos_temporales %>%
  summarise(total_viajes = n()) %>%
  collect()
```

---

## 🔹 **2. `persist()` – Persistir Datos en Memoria o Disco**
La función **`persist()`** permite almacenar datos en memoria (RAM), en disco o en ambos. Es ideal para almacenar datasets intermedios que se reutilizarán varias veces.

### 🔎 **¿Por qué usar `persist()`?**
✅ Mejora el rendimiento evitando que Spark tenga que recalcular datos repetidamente.  
✅ Se puede elegir el nivel de almacenamiento:  

- **`"MEMORY_ONLY"`** → Almacena solo en memoria (rápido, pero puede agotar recursos).  
- **`"MEMORY_AND_DISK"`** → Combina memoria y disco (recomendado para datasets grandes).  
- **`"DISK_ONLY"`** → Almacena solo en disco (menos eficiente pero seguro).

### 🔎 **Ejemplo de uso**
```r
# Persistir los datos en memoria para optimizar su reutilización
taxi_nyc %>%
  filter(trip_distance > 5) %>%
  sdf_persist(storage.level = "MEMORY_ONLY")
```

---

## 🔹 **3. `cache()` – Almacenar Datos en Memoria**
La función **`cache()`** es similar a `persist()` pero más sencilla. Se recomienda para almacenar datos en memoria que se reutilizarán varias veces durante la sesión de Spark.

### 🔎 **¿Por qué usar `cache()`?**
✅ Almacena los datos en memoria RAM para una recuperación rápida.  
✅ Útil para conjuntos de datos que se consultarán repetidamente.  
✅ **No es configurable**, por lo que se almacena exclusivamente en memoria.  

> ⚠️ Si el dataset es muy grande, **`cache()`** puede agotar la memoria del clúster.  

### 🔎 **Ejemplo de uso**
```r
# Cachear un dataset en memoria
taxi_nyc %>%
  filter(fare_amount > 20) %>%
  cache()
```

---

## 🔹 **4. `unpersist()` – Liberar Memoria en Spark**
La función **`unpersist()`** se utiliza para **liberar los datos almacenados en memoria** o en disco mediante `persist()` o `cache()`.

### 🔎 **¿Por qué usar `unpersist()`?**
✅ Es importante liberar los datos cuando ya no se necesiten para evitar problemas de memoria.  
✅ Permite liberar la memoria manualmente en lugar de depender del recolector de basura automático.

### 🔎 **Ejemplo de uso**
```r
# Eliminar un dataset almacenado en memoria para liberar recursos
taxi_nyc %>%
  unpersist()
```

---

## 🔹 **5. `repartition()` – Optimizar el Particionado de Datos**
La función **`repartition()`** se utiliza para **redistribuir** los datos en un número específico de particiones dentro del clúster de Spark.

### 🔎 **¿Por qué usar `repartition()`?**
✅ Permite mejorar la **paralelización** de las tareas en Spark.  
✅ Es especialmente útil cuando se detectan **particiones desbalanceadas**.  
✅ Recomendado para preparar los datos antes de realizar grandes agregaciones o joins.  

> ⚠️ **`repartition()`** reorganiza los datos completamente, lo que puede consumir tiempo si se utiliza en exceso.

### 🔎 **Ejemplo de uso**
```r
# Redistribuir el dataset en 200 particiones
taxi_nyc <- taxi_nyc %>%
  repartition(200)
```

---

## 🔹 **6. `coalesce()` – Reducir el Número de Particiones**
La función **`coalesce()`** se utiliza para reducir el número de particiones en Spark sin reorganizar completamente los datos. Es más eficiente que `repartition()` para este fin.

### 🔎 **¿Por qué usar `coalesce()`?**
✅ Menos costosa que `repartition()` porque no redistribuye los datos de forma completa.  
✅ Ideal cuando se desea reducir el número de particiones después de una operación de filtrado o agregación.

### 🔎 **Ejemplo de uso**
```r
# Reducir el número de particiones de 200 a 50
taxi_nyc <- taxi_nyc %>%
  coalesce(50)
```

---

## 🔹 **7. `group_by()` + `summarise()` – Agregación Eficiente**
El uso combinado de **`group_by()`** y **`summarise()`** permite realizar agregaciones directamente en Spark de forma eficiente.

### 🔎 **¿Por qué usar esta combinación?**
✅ Permite realizar cálculos directamente en el entorno distribuido.  
✅ Evita mover grandes volúmenes de datos al entorno local.  

### 🔎 **Ejemplo de uso**
```r
# Calcular la tarifa promedio por tipo de pago en Spark
tarifa_promedio <- taxi_nyc %>%
  group_by(payment_type) %>%
  summarise(tarifa_media = mean(fare_amount, na.rm = TRUE)) %>%
  collect()
```

---

## 🔹 **8. `distinct()` – Eliminar Duplicados en Spark**
La función **`distinct()`** permite identificar y eliminar registros duplicados directamente en el entorno distribuido de Spark.

### 🔎 **¿Por qué usar `distinct()`?**
✅ Optimiza el rendimiento eliminando duplicados directamente en el clúster.  
✅ Es más eficiente que `unique()` cuando se trabaja con grandes volúmenes de datos.  

### 🔎 **Ejemplo de uso**
```r
# Eliminar duplicados en el dataset
datos_limpios <- taxi_nyc %>%
  distinct(pickup_datetime, dropoff_datetime, trip_distance)
```

---

## 🔹 **9. `explain()` – Análisis del Plan de Ejecución**
La función **`explain()`** permite analizar cómo Spark está ejecutando una consulta.

### 🔎 **¿Por qué usar `explain()`?**
✅ Permite identificar posibles problemas de rendimiento.  
✅ Muestra el **plan de ejecución** que Spark está utilizando.  
✅ Útil para optimizar el flujo de trabajo en Spark.  

### 🔎 **Ejemplo de uso**
```r
# Visualizar el plan de ejecución para identificar problemas de rendimiento
taxi_nyc %>%
  filter(fare_amount > 50) %>%
  explain()
```

---

## 🔹 **10. `sdf_sample()` – Muestreo Eficiente en Spark**
La función **`sdf_sample()`** permite extraer muestras representativas de grandes volúmenes de datos directamente en Spark.

### 🔎 **¿Por qué usar `sdf_sample()`?**
✅ Permite explorar datos sin cargar todo el conjunto en memoria.  
✅ Ideal para realizar análisis exploratorio en conjuntos masivos.  

### 🔎 **Ejemplo de uso**
```r
# Extraer una muestra del 10% del dataset
muestra <- taxi_nyc %>%
  sdf_sample(fraction = 0.1) %>%
  collect()

head(muestra)
```


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

## 🔹 **Ejercicios iniciales sugeridos**

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

**Ejercicio 3 – Análisis de Propinas Elevadas**
**Objetivo:**  
- Filtra los viajes con propinas (`tip_amount`) superiores a **10 USD**.  
- Devuelve las 10 propinas más altas junto con la fecha y hora del viaje.  

**Código**
```r
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

