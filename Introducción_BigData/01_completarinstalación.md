Aquí tienes un documento en **Markdown** con la instalación y descripción de las librerías necesarias para el curso **Big Data con R**.

---

# 📌 Instalación de Librerías para el Curso de Big Data con R

Este documento describe las librerías que vamos a utilizar en el curso y cómo instalarlas en **RStudio** o **Jupyter Lab con soporte para R**.

---

## 🔹 **1. Instalación de librerías en R**
Para instalar todas las librerías necesarias, ejecuta el siguiente código en una sesión de R:

```r
# Definir la lista de paquetes necesarios
paquetes <- c("data.table", "dplyr", "readr", "vroom", "arrow", "DBI", "dbplyr",
              "bigrquery", "sparklyr", "ggplot2", "plotly", "dbplot", "trelliscopejs",
              "shiny", "flexdashboard", "tidymodels", "caret", "mlr3", "h2o", "xgboost", "lightgbm")

# Instalar paquetes que no estén ya instalados
paquetes_instalados <- rownames(installed.packages())
paquetes_faltantes <- setdiff(paquetes, paquetes_instalados)

if (length(paquetes_faltantes) > 0) {
  install.packages(paquetes_faltantes, dependencies = TRUE)
} else {
  message("✅ Todos los paquetes ya están instalados.")
}

# Instalar Apache Spark en local
sparklyr::spark_install()

# Instalar H2O para Machine Learning distribuido
install.packages("h2o")
library(h2o)
h2o.init()
```

---

## 🔹 **2. Descripción de las librerías utilizadas**
### 📂 **Manejo y manipulación de datos**
- **`data.table`**: Permite manejar grandes volúmenes de datos de manera eficiente, optimizando velocidad y consumo de memoria.
- **`dplyr`**: Facilita la manipulación de datos con una sintaxis intuitiva basada en `tidyverse`.
- **`readr`**: Carga rápida de archivos CSV, TSV y otros formatos de texto.
- **`vroom`**: Alternativa rápida a `readr`, ideal para grandes archivos.
- **`arrow`**: Soporte para el formato de datos **Apache Arrow**, que permite procesamiento eficiente en memoria.

### 🗄️ **Bases de datos y Big Data**
- **`DBI`**: Interfaz general para conectarse a bases de datos SQL desde R.
- **`dbplyr`**: Permite manipular bases de datos SQL utilizando `dplyr` como si fueran data frames locales.
- **`bigrquery`**: Conexión y manejo de datos en Google BigQuery.
- **`sparklyr`**: Conecta R con **Apache Spark** para procesamiento distribuido.

### 📊 **Visualización de datos**
- **`ggplot2`**: Paquete fundamental para gráficos en R basado en la gramática de gráficos.
- **`plotly`**: Permite generar gráficos interactivos.
- **`dbplot`**: Optimizado para visualizar datos directamente desde bases de datos.
- **`trelliscopejs`**: Facilita la visualización de grandes cantidades de gráficos de manera interactiva.
- **`shiny`** y **`flexdashboard`**: Creación de dashboards interactivos en R.

### 🤖 **Machine Learning con Big Data**
- **`tidymodels`**: Conjunto de herramientas para modelado y machine learning en R.
- **`caret`**: Paquete clásico para la construcción y evaluación de modelos predictivos.
- **`mlr3`**: Alternativa moderna y escalable a `caret`, diseñada para Big Data.
- **`h2o`**: Plataforma de aprendizaje automático distribuido, con soporte para deep learning.
- **`xgboost`** y **`lightgbm`**: Implementaciones optimizadas de gradient boosting, muy utilizadas en ciencia de datos.

---

## 🔹 **3. Comprobación de la instalación**
Para verificar que todas las librerías se instalaron correctamente, ejecuta:

```r
sapply(paquetes, require, character.only = TRUE)
```
