# 📌 Instalación y Configuración de Apache Spark para R

---

## 🔹 **1. Instalación de Spark en R**
Para instalar Spark en R, abre **RStudio o Jupyter Lab** y ejecuta:

```r
# Instalar la librería sparklyr
install.packages("sparklyr")

# Instalar Apache Spark en local (descarga automática)
sparklyr::spark_install()
```

### **📌 Notas sobre la instalación**
- `sparklyr` permite la conexión entre **R y Apache Spark**.
- `spark_install()` descarga e instala Spark automáticamente en el sistema.

Si prefieres instalar **una versión específica** de Spark, usa:

```r
sparklyr::spark_install(version = "3.2.0")
```

---

## 🔹 **2. Verificación de la instalación**
Para asegurarte de que Spark está correctamente instalado, ejecuta:

```r
library(sparklyr)
spark_available_versions()
```

Para comprobar que Spark se inicia correctamente, conecta R con Spark:

```r
sc <- spark_connect(master = "local")
sc
```

Si la conexión es exitosa, verás información sobre el servidor Spark.

---

## 🔹 **3. Configuración avanzada de Spark**
Si necesitas asignar más recursos a Spark, puedes personalizar la inicialización:

```r
sc <- spark_connect(master = "local", 
                    config = list(spark.memory.fraction = 0.8, 
                                  spark.executor.memory = "4G"))
```

O si estás trabajando con **Spark en un clúster**, cambia el `master`:

```r
sc <- spark_connect(master = "yarn")  # Para entornos Hadoop/YARN
sc <- spark_connect(master = "spark://ip-servidor:7077")  # Para Spark independiente
```

---

## 🔹 **4. Uso básico de Spark con R**
Una vez conectado, puedes cargar y manipular datos directamente en Spark:

```r
# Cargar datos en Spark desde un CSV
datos <- spark_read_csv(sc, name = "datos_spark", path = "ruta/dataset.csv", infer_schema = TRUE)

# Ver las primeras filas
head(datos)

# Transformaciones en Spark usando dplyr
library(dplyr)

datos %>%
  group_by(categoria) %>%
  summarise(promedio = mean(valor)) %>%
  collect()  # 'collect()' trae los resultados a R
```

---

## 🔹 **5. Apagar la sesión de Spark y desinsta**
Cuando termines de usar Spark, cierra la conexión con:

```r
spark_disconnect(sc)
```
Con esto se liberarán todos los recursos de memoria, disco, etc. asociados a la session de spark.

Para desinstalar spark completamente usa lo siguiente:

```r
spark_uninstall(version = "3.2.0")
```
