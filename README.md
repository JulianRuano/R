El paquete “readxl” es una librería de R que se utiliza para leer archivos de Microsoft Excel. Este paquete es útil para trabajar con datos que están almacenados en formato de hoja de cálculo. 

[Documentación “readxl”](https://r-coder.com/leer-excel-r/)

~~~R
install.packages("readxl")  # Instalar la librería
~~~

La función “c()” en R se utiliza para combinar valores en un vector. La letra “c” significa “combinar”. 

[Funciones](https://reptantia.com/blogs/r/funciones-en-r)


La función “barplot()” en R se utiliza para crear gráficos de barras. Esta función es parte de la biblioteca base de R y no requiere que se cargue ninguna biblioteca adicional

[barplot()](https://r-coder.com/grafico-barras-r/)

~~~R
# Cargar la librería
library(readxl)  
# Cambiar la dirrección donde se encuentra el documento
datos <- read_excel("¡CAMBIAR! dirección absoluta.xlsx", sheet = "Sheet 1") 
datos$Talla_m <- datos$talla / 100  # Convertir talla a metros
datos$IMC <- datos$peso / (datos$Talla_m^2)  # Calcular el IMC

datos$Categoria_IMC <- ifelse(datos$IMC < 18.5, "Delgadez",
                              ifelse(datos$IMC < 25, "Normalidad",
                                     ifelse(datos$IMC < 30, "Sobrepeso", "Obesidad")))

categorias <- c("Delgadez", "Normalidad", "Sobrepeso", "Obesidad")
frecuencia <- table(datos$Categoria_IMC)

barplot(frecuencia, names.arg = categorias,
        xlab = "Categoría IMC", ylab = "Frecuencia",
        main = "Distribución de IMC")
~~~

## Salida
![Texto alternativo](https://res.cloudinary.com/dilrruxyx/image/upload/v1686442863/IMC_dxaihk.jpg)



