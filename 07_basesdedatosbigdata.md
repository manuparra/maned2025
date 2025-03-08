# 📌 Bases de Datos para Big Data: Uso desde R en Entornos Empresariales

En el contexto de **Big Data**, las bases de datos juegan un rol fundamental para almacenar, gestionar y consultar grandes volúmenes de datos. En este documento analizaremos los tipos más usados (**NoSQL**, **NewSQL**, y **bases de datos analíticas distribuidas**), ejemplos prácticos de acceso desde R y cómo esto conecta con las necesidades empresariales.

---

## 🔹 **1. Bases de Datos NoSQL**

Las bases de datos **NoSQL** almacenan grandes volúmenes de datos no estructurados o semi-estructurados, ideales para aplicaciones escalables y ágiles.

### 📂 **MongoDB (documental)**

- Almacena datos en formato JSON (documentos).
- Ideal para aplicaciones con datos flexibles y variables.

### 🔸 **Acceso desde R a MongoDB**
```r
library(mongolite)

# Conexión a MongoDB
mongo_conn <- mongo(collection = "clientes", db = "mi_empresa", url = "mongodb://localhost:27017")

# Consulta sencilla
clientes <- mongo_conn$find('{"edad": {"$gte":30}}')

head(clientes)
```

---

## 🔹 **2. Apache Cassandra (NoSQL)**

### 📂 **¿Qué es Cassandra?**
Base de datos NoSQL distribuida, especializada en alta disponibilidad y escalabilidad, ideal para grandes volúmenes de datos.

### 🔸 **Acceso desde R a Cassandra**
```r
library(RCassandra)

# Conexión a Cassandra
conn <- RC.connect(host = "127.0.0.1", port = 9042)
RC.use(conn, "ventas")

# Consulta de datos
resultados <- RC.execute(conn, "SELECT * FROM transacciones LIMIT 10;")
print(resultados)
```

---

## 🔹 **2. Bases de Datos SQL Distribuidas (NewSQL)**

Estas bases combinan la escalabilidad de NoSQL con la potencia del SQL tradicional. Ejemplos importantes son **Google BigQuery**, **Amazon Redshift** y **Snowflake**.

### 📂 **Google BigQuery**
Plataforma escalable para consultas SQL analíticas en datasets masivos.

### 🔸 **Acceso desde R a BigQuery**
```r
library(bigrquery)
library(dplyr)

# Conexión
project_id <- "mi-proyecto-bigdata"

sql <- "SELECT product_id, SUM(sales) as total_ventas
        FROM `mi_empresa.ventas.transacciones`
        GROUP BY product_id"

resultados <- bq_project_query(project_id = project_id, sql)
ventas_resumen <- bq_table_download(resultados)

head(ventas_resumen)
```

---

### 📂 **Amazon Redshift**
Base de datos analítica SQL que permite ejecutar consultas rápidas sobre grandes volúmenes.

### 🔸 **Acceso desde R a Redshift**
```r
library(DBI)
library(RPostgres)

con <- dbConnect(RPostgres::Postgres(),
                 host = "redshift-cluster.endpoint.amazonaws.com",
                 port = 5439,
                 dbname = "analytics",
                 user = "usuario",
                 password = "contraseña")

# Consulta SQL
ventas <- dbGetQuery(con, "SELECT * FROM ventas LIMIT 10")

head(ventas)
```

---

### 📂 **Snowflake**
Base de datos SQL en la nube que separa almacenamiento y cómputo.

### 🔸 **Acceso desde R a Snowflake**
```r
library(DBI)
library(odbc)

con <- dbConnect(odbc::odbc(),
                 Driver = "SnowflakeDSIIDriver",
                 Server = "empresa.snowflakecomputing.com",
                 Database = "analytics",
                 UID = "usuario",
                 PWD = "password")

datos <- dbGetQuery(con, "SELECT producto, sum(ventas) FROM ventas GROUP BY producto")
head(datos)
```

---

## 🔹 **2. Bases de Datos NoSQL Orientadas a Columnas (Cassandra, HBase)**
Estas bases permiten almacenar grandes cantidades de datos no estructurados para análisis distribuidos en tiempo real.

### 📂 **Apache HBase**
Sistema de almacenamiento columnar en tiempo real sobre HDFS.

### 🔸 **Acceso desde R a HBase usando Thrift**
```r
library(rhbase)

conn <- hb.init(host = "localhost", port = 9090)
tabla <- hb.get(conn, table = "clickstream", row = "usuario123")
print(tabla)
```

---

## 🔹 **3. Conexión con Datos Empresariales**
Las empresas generalmente almacenan sus datos transaccionales en bases de datos SQL tradicionales (Oracle, MySQL, PostgreSQL) y sus datos analíticos en almacenes especializados como Redshift, BigQuery o Snowflake.

### 📂 **Flujo habitual de integración empresarial con Big Data:**
- **Datos transaccionales** (ventas, clientes, inventarios) se almacenan en **bases relacionales**.
- **ETL/ELT** mueve esos datos a **almacenes analíticos** (como Redshift o Snowflake).
- Spark y herramientas R se conectan a esos almacenes para realizar análisis, modelado predictivo o dashboards.

---

## 🔹 **4. Ventajas y Desventajas de cada Base de Datos para Big Data**

| Base de datos      | Ventajas | Desventajas | Uso típico |
|--------------------|------------------|-----------------|---------------|
| **MongoDB** | Flexibilidad de estructura, rapidez para datos JSON | Complejidad para consultas analíticas |
| **Cassandra** | Alta escalabilidad, gran volumen | Difícil para consultas relacionales |
| **Google BigQuery** | Escalable, rápido, sin gestión de infra. | Costes por consulta pueden crecer |
| **Amazon Redshift** | SQL potente, rápido en datasets grandes | Costos altos si no se administra bien |
| **Snowflake** | Gran rendimiento analítico, escalabilidad dinámica | Costos elevados con alto volumen o muchas consultas |
| **Apache HBase** | Muy escalable para datos no estructurados | Complejidad alta, administración técnica difícil |

---

## 🔹 **5. Recomendaciones según contexto empresarial**
- **Para consultas SQL analíticas en la nube**: BigQuery, Redshift o Snowflake.
- **Datos altamente flexibles o no estructurados (logs, eventos)**: Cassandra, MongoDB o HBase.
- **Analítica en tiempo real sobre eventos**: HBase o Cassandra.
- **Integración y análisis distribuido avanzado (ETL)**: Apache Spark con BigQuery o Redshift.

---

## 🔹 **5. ¿Cómo conecta esto con la empresa?**
Las empresas generalmente tienen sistemas relacionales (PostgreSQL, Oracle, SQL Server) para almacenamiento transaccional, pero al crecer necesitan soluciones analíticas que permitan acceder a datos distribuidos rápidamente.

**Flujo típico empresarial:**

```
[Datos transaccionales (MySQL, PostgreSQL, Oracle)] 
                  ↓ (ETL)
[Almacén analítico (Redshift, BigQuery, Snowflake)]
                  ↓
        [Análisis avanzado con R/Spark/H2O]
                  ↓
   [Dashboards interactivos (Shiny, Power BI)]
```

Este flujo proporciona una vista completa y rápida de grandes volúmenes de información, permitiendo análisis escalables con Spark y R desde un entorno empresarial.
