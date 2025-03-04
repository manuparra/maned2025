# 📌 Instalación y Configuración de H2O para R

En este documento aprenderás a instalar **H2O**, una plataforma de Machine Learning distribuido que permite trabajar con grandes volúmenes de datos en R.

---

## 🔹 **1. Instalación de H2O en R**
Para instalar H2O en R, abre RStudio o Jupyter Lab y ejecuta el siguiente código:

```r
# Instalar paquetes necesarios
install.packages(c("h2o", "curl", "RCurl"))

# Cargar la librería
library(h2o)

# Iniciar H2O
h2o.init()
```

### **📌 Notas **
- `h2o` es la librería principal que permite la conexión con el motor de H2O.
- `curl` y `RCurl` son dependencias necesarias para la comunicación con el servidor de H2O.

---

## 🔹 **2. Verificación de la instalación**
Después de instalar H2O, asegúrate de que todo funciona correctamente ejecutando:

```r
h2o.init()
```

Si la instalación es correcta, verás información sobre el clúster H2O, como la cantidad de memoria disponible, número de nodos y configuración del servidor.

Para verificar la versión instalada, usa:

```r
h2o.clusterInfo()
```

---

## 🔹 **3. Configuración avanzada de H2O**
Si necesitas ajustar la memoria asignada a H2O (por defecto usa 25% de la RAM disponible), puedes definir la cantidad de memoria antes de iniciarlo:

```r
h2o.shutdown(prompt = FALSE)  # Apagar cualquier sesión activa
h2o.init(max_mem_size = "8G") # Iniciar con 8GB de RAM
```

Si deseas especificar más parámetros, puedes usar:

```r
h2o.init(nthreads = -1, max_mem_size = "16G")  # Usar todos los núcleos disponibles y 16GB de RAM
```

---

## 🔹 **4. Uso básico de H2O en R**
Una vez instalado, podemos cargar datos en formato H2O:

```r
# Cargar un dataset de ejemplo
data <- h2o.importFile("https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv")

# Mostrar resumen de los datos
summary(data)

# Convertir una columna a factor (categórica)
data$species <- as.factor(data$species)

# Dividir datos en entrenamiento y prueba (80%-20%)
splits <- h2o.splitFrame(data, ratios = 0.8, seed = 42)
train <- splits[[1]]
test <- splits[[2]]

# Crear un modelo de regresión logística
modelo <- h2o.glm(x = c("sepal_length", "sepal_width", "petal_length", "petal_width"),
                   y = "species",
                   training_frame = train,
                   family = "multinomial")

# Mostrar resultados
h2o.performance(modelo, newdata = test)
```

---

## 🔹 **5. Apagado de H2O**
Cuando termines de usar H2O, es recomendable apagar la sesión para liberar memoria:

```r
h2o.shutdown(prompt = FALSE)
```
