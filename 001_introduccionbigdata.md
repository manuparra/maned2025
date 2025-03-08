# 📌 **Introducción y Conceptos Clave de Big Data**

En este documento proporcionamos una introducción integral a los conceptos clave del **Big Data**, orientado a alumnos de máster que necesitan adquirir un conocimiento sólido sobre esta tecnología.

---

## 🔹 **1. Definición de Big Data**

**Big Data** es el término que define la gestión y análisis de grandes volúmenes de datos que superan la capacidad de procesamiento tradicional, caracterizado habitualmente mediante las **5 V**:

- **Volumen:** Grandes cantidades de datos (terabytes, petabytes o exabytes).
- **Velocidad:** Generación rápida de datos (streaming, tiempo real).
- **Variedad:** Datos estructurados, semi-estructurados y no estructurados.
- **Veracidad:** Fiabilidad y calidad variable de los datos obtenidos.
- **Valor:** Capacidad para extraer valor real y útil del análisis de los datos.

**Ejemplo práctico**:  
Una plataforma como Netflix genera millones de registros por minuto (Velocidad), que incluyen vídeos (no estructurados), metadatos (estructurados) y comentarios de usuarios (semi-estructurados). El análisis en tiempo real genera recomendaciones personalizadas (valor) para millones de usuarios.

---

## 🔹 **2. Herramientas Esenciales en Big Data**

| Categoría | Herramientas |
|-----------|--------------|
| **Almacenamiento** | HDFS, Amazon S3, Google Cloud Storage, Azure Data Lake Storage |
| **Procesamiento distribuido** | Apache Spark, Apache Hadoop (MapReduce), Apache Flink, Dask |
| **Bases de datos** | Cassandra, MongoDB, HBase, Amazon Redshift, BigQuery, Snowflake |
| **Análisis y Machine Learning** | Spark MLlib, H2O.ai, Dask-ML |
| **Visualización** | Tableau, Power BI, ggplot2, Shiny, Plotly |

---

## 🔹 **3. Plataformas y Arquitecturas Comunes**

La arquitectura típica de Big Data incluye:

**a. Data Ingestion (Ingesta de datos)**  
- Fuentes de datos múltiples (logs, bases de datos empresariales, APIs).
- Herramientas: Kafka, Apache Flume, Sqoop.

**b. Data Storage (Almacenamiento)**  
- HDFS, sistemas basados en objetos (S3, GCS), bases NoSQL.

**c. Procesamiento**  
- Apache Spark, Hadoop MapReduce, Dask, Apache Flink.

**d. Análisis y Machine Learning**  
- Spark MLlib, H2O, TensorFlow, XGBoost, scikit-learn distribuido.

**e. Visualización**  
- Dashboards interactivos (Shiny, Power BI), herramientas avanzadas (Tableau).

**Esquema simplificado de arquitectura**:
```
Fuentes → Ingesta → Almacenamiento Distribuido → Procesamiento → Análisis → Visualización
```

---

## 🔹 **4. Tipos de Datos Usados en Big Data**

- **Estructurados**: Bases de datos SQL, CSV.
- **Semi-estructurados**: JSON, XML, logs.
- **No estructurados**: Imágenes, audio, vídeo, texto libre.

---

## 🔹 **4.1 Tipos de Ficheros Más Comunes**

- **CSV**: Sencillo pero ineficiente para grandes volúmenes.
- **Parquet**: Columnar, comprimido, rápido para consultas analíticas.
- **ORC**: Similar a Parquet, optimizado para consultas en Hadoop.
- **Avro**: Ideal para transmisión y serialización con esquemas flexibles.

---

## 🔹 **5. Modelos de Procesamiento de Big Data**

- **Procesamiento batch (por lotes)**: MapReduce, Spark batch.
- **Procesamiento en streaming**: Spark Streaming, Apache Flink.
- **Procesamiento híbrido**: Spark Structured Streaming.

---

## 🔹 **6. Bases de Datos para Big Data**

- **NoSQL**: Cassandra, MongoDB, HBase.
- **SQL Distribuido**: BigQuery, Redshift, Snowflake.

---

## 🔹 **7. Algoritmos y Machine Learning en Big Data**

- **Algoritmos distribuidos comunes**:  
  - Regresión logística, árboles de decisión, random forest, clustering (k-means).
- **Frameworks usados**: H2O, Spark MLlib, XGBoost distribuido, Dask-ML.

## 🔹 **8. Ventajas y Retos del Big Data**

**Ventajas:**
✅ Escalabilidad, rendimiento en datos masivos  
✅ Análisis en tiempo real  
✅ Optimización en toma de decisiones empresariales

**Desventajas**
❌ Complejidad técnica elevada  
❌ Costos potencialmente altos (infraestructura o cloud)  
❌ Requiere personal capacitado y especializado  

---

## 🔹 **8. Ejemplos Reales de Big Data Empresarial**

- **Netflix**: Usa Spark y Hadoop para recomendar contenido personalizado basado en análisis de millones de usuarios simultáneos.
- **Amazon**: Usa Big Data para personalización de compras, logística optimizada y recomendaciones.
- **BBVA, Santander (sector financiero)**: Utilizan Big Data para detección de fraudes y análisis predictivo financiero.
- ... Muchos otros a comentar

---

## 🔹 **9. Big Data en la Empresa: Integración y Aplicación**

Las empresas aprovechan Big Data para:

- **Marketing**: Segmentación precisa de clientes.
- **Operaciones**: Optimización logística.
- **RRHH**: Retención de talento mediante análisis predictivos.
- **Finanzas**: Gestión de riesgos, fraudes.
- **Mucha otras áreas**

