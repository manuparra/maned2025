# 📌 Instalación y Configuración de Dask para R

**Dask** es una  herramienta de computación paralela diseñada para trabajar con grandes volúmenes de datos. Aunque Dask está principalmente desarrollado en **Python**, es posible integrarlo con **R** mediante el paquete **`reticulate`**, que permite ejecutar código Python desde R.

---

## 🔹 **Paso 1: Instalar Dask usando `pip`**
Ejecuta el siguiente comando en tu terminal (o en el entorno de Anaconda si estás usando Jupyter Lab):

```bash
pip install "dask[complete]"
```

La opción `"dask[complete]"` incluye:
✅ `dask` → Core del motor de computación.  
✅ `distributed` → Permite ejecutar Dask en entornos escalables.  
✅ `dask-ml` → Herramientas para machine learning distribuido.  
✅ `dask-xgboost` → Soporte para modelos avanzados de Boosting.  

---

## 🔹 **Paso 2: Instalar el paquete `reticulate` en R**
`reticulate` es el puente que permite ejecutar código Python desde R.

```r
install.packages("reticulate")
```

---

## 🔹 **2. Configuración de Dask en R**
Para que R pueda usar Dask correctamente, debes indicarle la ruta de Python donde está instalado Dask.

### 🔹 **Paso 1: Configurar `reticulate` en R**
```r
library(reticulate)

# Verifica la ubicación de Python
py_config()

# Especifica el entorno de Python donde está instalado Dask
use_python("/usr/bin/python3")  # Linux/macOS
use_python("C:/Python311/python.exe")  # Windows (ajusta la ruta según tu instalación)
```

---

### 🔹 **Paso 2: Cargar Dask desde R**
Para importar y utilizar Dask directamente en R:

```r
# Importar Dask desde R
dask <- import("dask")

# Cargar el módulo 'dataframe' de Dask
dd <- import("dask.dataframe")

# Cargar un archivo CSV grande en un DataFrame de Dask
df <- dd$read_csv("datos_grandes.csv")

# Mostrar las primeras filas del DataFrame
df$head(10)
```

---

## 🔹 **3. Ejemplo Completo de Procesamiento con Dask en R**
Este ejemplo muestra cómo calcular estadísticas básicas sobre un gran conjunto de datos utilizando Dask.

```r
library(reticulate)

# Importar Dask
dask <- import("dask")
dd <- import("dask.dataframe")

# Leer un conjunto de datos de gran tamaño
datos <- dd$read_csv("ruta/al/archivo.csv")

# Resumir datos agrupados por categoría
resumen <- datos %>%
  filter(datos$precio > 100) %>%
  group_by(datos$categoria) %>%
  summarise(total_ventas = sum(datos$ventas))

# Recoger el resultado en R
resumen <- resumen$compute()
print(resumen)
```

---

## 🔹 **4. Configuración Avanzada: Dask Distribuido**
Dask se destaca por su capacidad de distribuir el trabajo en múltiples núcleos o incluso en un clúster. Para esto, puedes iniciar un cliente distribuido desde R.

### **Iniciar un cliente distribuido en Dask desde R**
```r
dask_distributed <- import("dask.distributed")
client <- dask_distributed$Client()

# Visualizar la interfaz web del clúster (http://localhost:8787)
print(client)
```

Puedes configurar parámetros adicionales para optimizar el rendimiento:

```r
client <- dask_distributed$Client(n_workers = 4, threads_per_worker = 2, memory_limit = "8GB")
```

---

## 🔹 **5. Rendimiento**
✅ **Dask** es ideal para manejar datos que exceden la memoria RAM.  
✅ Utiliza técnicas de **computación en paralelo** para acelerar el procesamiento.  
✅ Su integración con **R** mediante `reticulate` permite aprovechar la flexibilidad de ambos ecosistemas.  

---

## 🔹 **6. Solución de problemas**
- Si recibes el error `ModuleNotFoundError: No module named 'dask'`, verifica que estás usando el entorno de Python correcto en R con:

```r
py_config()
```

- Si Dask no reconoce el archivo CSV, prueba especificar el separador o la codificación:

```r
df <- dd$read_csv("datos_grandes.csv", sep = ";", encoding = "utf-8")
```
