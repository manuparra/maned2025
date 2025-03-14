# 🔵 **Tema 6.5.7 – Seguridad, Gobernanza y Privacidad de Datos en Big Data usando R**

En entornos de **Big Data**, la **seguridad**, la **gobernanza** y la **privacidad** son aspectos críticos para garantizar que los datos estén protegidos, correctamente administrados y que se cumplan las normativas legales. Aunque herramientas como **Apache Atlas** o **AWS Glue** son populares en este ámbito, es posible implementar medidas efectivas directamente utilizando **R** en combinación con **Apache Spark**.

En esta sección aprenderás a:

✅ Entender los conceptos clave en seguridad, gobernanza y privacidad de datos  
✅ Implementar estrategias de control de acceso y permisos en entornos Big Data usando R  
✅ Auditar y controlar el linaje de datos para asegurar la trazabilidad  
✅ Gestionar y proteger datos sensibles mediante técnicas como **enmascaramiento**, **anonimización** y **tokenización**  
✅ Cumplir normativas como **GDPR**, **HIPAA** o **ISO 27001** utilizando técnicas reproducibles en R  
✅ Realizar ejemplos prácticos con el dataset real de **NYC Taxi Trips**  
✅ Resolver problemas reales de seguridad con 2 ejercicios prácticos  

---

# 🔹 **1. Conceptos clave en Seguridad, Gobernanza y Privacidad de Datos**

## 📌 **1.1. Seguridad de los datos**
La **seguridad** de datos se refiere a las prácticas y herramientas implementadas para proteger la información contra accesos no autorizados, alteración, pérdida o destrucción.

### 🔎 **Principales aspectos de seguridad en Big Data**
- **Autenticación:** Verifica la identidad de los usuarios antes de permitir el acceso.  
- **Autorización:** Controla los permisos que tienen los usuarios sobre los datos.  
- **Cifrado:** Protege los datos durante el almacenamiento o la transmisión.  
- **Control de acceso basado en roles (RBAC):** Permite asignar permisos según los roles de los usuarios.  

---

## 📌 **1.2. Gobernanza de los datos**
La **gobernanza de datos** se centra en la gestión del ciclo de vida de los datos, incluyendo:

- **Control del linaje de datos (Data Lineage):** Rastrear el origen, las transformaciones y el destino de los datos.  
- **Gestión de metadatos:** Documentar información clave sobre los datos (fuente, tipo, etc.).  
- **Mecanismos de auditoría:** Registrar quién accede a los datos y qué operaciones realiza.  

---

## 📌 **1.3. Privacidad de los datos**
La **privacidad** de los datos implica la protección de la información sensible de individuos, en cumplimiento de normativas como:

✅ **GDPR (Reglamento General de Protección de Datos)**  
✅ **HIPAA (Ley de Portabilidad y Responsabilidad del Seguro Médico)**  
✅ **ISO 27001 (Gestión de Seguridad de la Información)**  

### 🔎 **Estrategias clave para proteger datos sensibles**
- **Enmascaramiento de datos:** Sustitución de valores reales por datos ficticios.  
- **Anonimización:** Proceso de eliminar o modificar información que identifique personas.  
- **Tokenización:** Sustitución de datos sensibles por valores generados que carecen de valor directo.

---

# 🔹 **2. Implementación de Seguridad, Gobernanza y Privacidad con R**

## 📌 **2.1. Control de Acceso en R/Spark**
En R, podemos controlar el acceso a los datos utilizando la gestión de usuarios y permisos directamente dentro del entorno Spark.

### 🔎 **Paso 1: Definir roles de usuario**
Creamos un conjunto de usuarios ficticios para simular un entorno empresarial.

```r
usuarios <- data.frame(
  usuario = c("analista", "data_scientist", "admin"),
  rol = c("lectura", "lectura_escritura", "administrador")
)
```

---

### 🔎 **Paso 2: Asignar permisos según roles**
Implementamos un mecanismo básico de control de permisos para filtrar el acceso a ciertas columnas:

```r
# Función para controlar el acceso según el rol
control_acceso <- function(datos, rol) {
  if (rol == "lectura") {
    return(select(datos, pickup_datetime, fare_amount))
  } else if (rol == "lectura_escritura") {
    return(select(datos, pickup_datetime, fare_amount, tip_amount))
  } else if (rol == "administrador") {
    return(datos)  # Acceso total
  } else {
    stop("Acceso denegado.")
  }
}
```

### 🔎 **Paso 3: Aplicar permisos a un dataset**
```r
# Ejemplo de uso
taxi_nyc <- spark_read_parquet(sc, name = "taxi_nyc", path = "hdfs://ruta/nyc_taxi.parquet")

# Permitir solo acceso básico (rol de analista)
taxi_analista <- control_acceso(taxi_nyc, "lectura")
taxi_analista %>% head()
```

---

## 📌 **2.2. Gestión del Linaje de Datos en R**
El linaje de datos permite rastrear el ciclo de vida de los datos, desde la ingesta hasta su transformación y visualización.

### 🔎 **Estrategia manual de linaje de datos en R**
Podemos documentar cada transformación utilizando registros automáticos.

**Ejemplo:** Documentar una transformación de datos

```r
library(dplyr)

# Pipeline de transformación con trazabilidad
linaje <- list()

# Transformación 1: Filtrar datos
taxi_clean <- taxi_nyc %>%
  filter(fare_amount > 0)
linaje[[1]] <- "Filtrado de registros con tarifas negativas"

# Transformación 2: Crear columna de ingresos totales
taxi_clean <- taxi_clean %>%
  mutate(total_ingresos = fare_amount + tip_amount)
linaje[[2]] <- "Creación de columna total_ingresos"

# Registro del linaje
print(linaje)
```

---

## 📌 **2.3. Enmascaramiento y Anonimización de Datos en R**
El enmascaramiento y anonimización protegen la identidad de los individuos.

### 🔎 **Enmascaramiento de datos sensibles**
```r
library(stringr)

# Ejemplo de datos ficticios
clientes <- data.frame(
  cliente_id = c("001", "002", "003"),
  nombre = c("Juan Pérez", "María López", "Carlos Ruiz"),
  telefono = c("654321987", "678987654", "612345678")
)

# Enmascarar el teléfono (sólo mostrar los últimos 4 dígitos)
clientes <- clientes %>%
  mutate(telefono_enmascarado = str_replace(telefono, "^[0-9]{6}", "******"))

print(clientes)
```

---

### 🔎 **Anonimización de datos**
```r
# Anonimización reemplazando nombres por IDs
clientes <- clientes %>%
  mutate(nombre = paste("Cliente", cliente_id))

print(clientes)
```

---

## 📌 **2.4. Auditoría de Accesos**
Una auditoría registra quién accedió a los datos y qué acciones realizó.

### 🔎 **Registro de acciones en R**
```r
registro_auditoria <- data.frame(
  usuario = character(),
  accion = character(),
  fecha = Sys.time()
)

# Función para registrar una acción
registrar_accion <- function(usuario, accion) {
  nueva_entrada <- data.frame(
    usuario = usuario,
    accion = accion,
    fecha = Sys.time()
  )
  assign("registro_auditoria", rbind(registro_auditoria, nueva_entrada), envir = .GlobalEnv)
}

# Ejemplo de acciones
registrar_accion("analista", "Consultó registros de enero de 2023")
registrar_accion("admin", "Modificó datos en la tabla principal")

# Mostrar el registro de auditoría
print(registro_auditoria)
```

---

# 🔹 **3. Ejercicios Prácticos**

### 🟩 **Ejercicio 1 – Enmascaramiento de Datos Sensibles**
**Objetivo:**  
- Cargar el dataset de taxis en formato **Parquet**.  
- Enmascarar la columna `PULocationID` sustituyendo sus valores por un identificador anónimo.  

**Código inicial:**
```r

```

---

### 🟩 **Ejercicio 2 – Control de Acceso**
**Objetivo:**  
- Cargar el dataset de taxis en formato **Parquet**.  
- Implementar un sistema de permisos que permita mostrar únicamente la columna `fare_amount` para usuarios con rol de "analista".  

**Código inicial:**
```r

```
