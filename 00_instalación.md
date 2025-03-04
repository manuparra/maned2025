
# **Instalación de R, RStudio y JupyterLab con soporte para R**  

## **1. Instalación de R**  
R es un lenguaje de programación y un entorno para el análisis estadístico y la ciencia de datos. Debes instalarlo antes de RStudio y JupyterLab.  

### **Windows**  
1. Abre tu navegador web y visita la página oficial de R:  
   [https://cran.r-project.org](https://cran.r-project.org)  
2. Haz clic en **Download R for Windows** y luego en **base**.  
3. Descarga el instalador más reciente (archivo `.exe`).  
4. Ejecuta el instalador y sigue los pasos:  
   - Acepta los términos de licencia.  
   - Mantén la ruta de instalación por defecto (normalmente `C:\Program Files\R\`).  
   - En la opción de seleccionar componentes, deja todas las opciones marcadas.  
   - Cuando pregunte si quieres personalizar las opciones, selecciona "No".  
5. Finaliza la instalación y cierra el instalador.  

### **Mac**  
1. Accede a [https://cran.r-project.org](https://cran.r-project.org) y haz clic en **Download R for macOS**.  
2. Descarga el paquete `.pkg` más reciente disponible.  
3. Abre el archivo `.pkg` y sigue las instrucciones del instalador:  
   - Acepta los términos de licencia.  
   - Completa la instalación manteniendo las opciones por defecto.  
4. Finaliza la instalación y cierra el instalador.  

Para verificar la instalación, abre la terminal (Mac) o el símbolo del sistema (Windows) y ejecuta:  

```sh
R --version
```
Si R está instalado correctamente, verás la versión instalada en la pantalla.  

---

## **2. Instalación de RStudio**  
RStudio es un entorno de desarrollo integrado (IDE) para R.  

### **Windows y Mac**  
1. Ve al sitio web oficial de RStudio:  
   [https://posit.co/download/rstudio-desktop/](https://posit.co/download/rstudio-desktop/)  
2. Descarga la versión gratuita de RStudio para tu sistema operativo.  
3. Abre el instalador (`.exe` en Windows o `.dmg` en Mac) y sigue los pasos:  
   - Acepta los términos de uso.  
   - Mantén las configuraciones por defecto.  
   - Completa la instalación.  
4. Abre RStudio para verificar que funciona correctamente.  

---

## **3. Instalación de JupyterLab con Soporte para R**  
JupyterLab es una interfaz de cuadernos interactivos que permite ejecutar R en combinación con otros lenguajes como Python.  

### **Windows y Mac**  
#### **Instalar Python y pip**  
Si no tienes Python instalado, descárgalo desde [https://www.python.org/downloads/](https://www.python.org/downloads/) e instálalo asegurándote de marcar la casilla **"Add Python to PATH"** en Windows.  

Para verificar que Python y `pip` están instalados, ejecuta en la terminal o en el símbolo del sistema:  
```sh
python --version
pip --version
```
Si ves la versión de Python y `pip`, puedes continuar.  

#### **Instalar JupyterLab**  
Ahora instala JupyterLab con el siguiente comando:  
```sh
pip install jupyterlab
```
Verifica la instalación ejecutando:  
```sh
jupyter lab --version
```

#### **Instalar el Kernel de R**  
Para que JupyterLab pueda ejecutar código en R, es necesario instalar el paquete `IRKernel`.  

1. Abre R (desde RStudio o la terminal) y ejecuta:  
   ```r
   install.packages("IRKernel")
   IRKernel::installspec(user = TRUE)
   ```
2. Luego, cierra R y prueba si JupyterLab reconoce el kernel de R ejecutando:  
   ```sh
   jupyter kernelspec list
   ```
   Si ves una entrada para "ir", significa que R está correctamente configurado.  

#### **Ejecutar JupyterLab con R**  
Para iniciar JupyterLab, abre una terminal y ejecuta:  
```sh
jupyter lab
```
Se abrirá una ventana en el navegador. Para usar R, selecciona **Nuevo cuaderno** y elige **R** como kernel.  

Con esto, ya tienes un entorno completo con R, RStudio y JupyterLab listo para trabajar. 🚀
