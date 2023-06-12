El paquete “readxl” es una librería de R que se utiliza para leer archivos de Microsoft Excel. Este paquete es útil para trabajar con datos que están almacenados en formato de hoja de cálculo. 

[✅ Documentación “readxl”](https://r-coder.com/leer-excel-r/)

~~~R
install.packages("readxl")  # Instalar la librería
~~~

La función “c()” en R se utiliza para combinar valores en un vector. La letra “c” significa “combinar”. 

[✅ Documentación Funciones](https://reptantia.com/blogs/r/funciones-en-r)


La función “barplot()” en R se utiliza para crear gráficos de barras. Esta función es parte de la biblioteca base de R y no requiere que se cargue ninguna biblioteca adicional

[✅ Documentación barplot()](https://r-coder.com/grafico-barras-r/)


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
~~~    

El índice de cintura/talla es un indicador de riesgo cardiovascular de las personas, según la literatura se
puede interpretar que aquellas personas que tienen IC/T por encima de 0.5 tienen mayor riesgo.

~~~R
datos$ic_t <- datos$cintura / datos$talla
datos$Riesgo <- ifelse(datos$ic_t > 0.5, "Alto Riesgo", "Bajo Riesgo")
~~~

---

1️⃣ - Se tiene la creencia que más de ¼ de las mujeres adultas jóvenes, con estado nutricional  normal, tienen riesgo cardiovascular. Interprete los resultados.

~~~R
#Filtrar los datos para obtener únicamente las mujeres adultas jóvenes con estado nutricional normal:
mujeres_normal <- datos$sexo == "Mujeres" & datos$edad_anos >= 18 & datos$edad_anos <= 30 & datos$Categoria_IMC == "Normalidad"
# Calcular la proporción estimada y el valor de referencia
proporcion_estimada <- sum(mujeres_normal & datos$Riesgo == "Alto Riesgo") / sum(mujeres_normal)
valor_referencia <- 0.25
# Crear un data frame con los valores
datos_proporcion <- data.frame(Variable = c("Proporción Estimada", "Valor de Referencia"),
                               Proporcion = c(proporcion_estimada, valor_referencia))

# Graficar las proporciones
ggplot(datos_proporcion, aes(x = Variable, y = Proporcion, fill = Variable)) +
  geom_bar(stat = "identity", width = 0.5) +
  geom_hline(yintercept = valor_referencia, color = "red", linetype = "dashed") +
  geom_text(aes(label = round(Proporcion, 3)), vjust = -0.5) +
  scale_fill_manual(values = c("Proporción Estimada" = "steelblue", "Valor de Referencia" = "gray")) +
  xlab("") +
  ylab("Proporción") +
  ggtitle("Comparación de Proporciones") +
  theme_minimal()
~~~
<p align="center">
<img src="https://res.cloudinary.com/dilrruxyx/image/upload/v1686600966/primero_ehgcat.jpg" alt="mypic" width="65%">
</p>

## Análisis 
Dado que el valor estimado (0.183) es menor que el valor de referencia (0.25), no hay evidencia suficiente para afirmar que más de 1/4 de las mujeres adultas jóvenes con estado nutricional normal tienen riesgo cardiovascular en tu muestra. 

---

2️⃣ - Se sabe que en los últimos años ha aumentado la adiposidad abdominal en la juventud, se piensa
que los hombres en sobrepeso presentan mayor prevalencia de adiposidad que las mujeres en el
mismo estado. Interprete los resultados.

~~~R
library(ggplot2)

# Filtrar los datos para obtener únicamente a los participantes en sobrepeso
sobrepeso <- datos$Categoria_IMC == "Sobrepeso"

# Calcular el ICO 
datos$ICO <- datos$cintura/100 / (0.109 * sqrt(datos$peso / datos$Talla_m))

# Calcular las proporciones de hombres y mujeres en sobrepeso con ICO por encima de 1.2
prop_hombres <- sum(sobrepeso & datos$sexo == "Hombres" & datos$ICO > 1.2) / sum(sobrepeso & datos$sexo == "Hombres")
prop_mujeres <- sum(sobrepeso & datos$sexo == "Mujeres" & datos$ICO > 1.2) / sum(sobrepeso & datos$sexo == "Mujeres")

# Crear un data frame con los valores
datos_proporcion <- data.frame(Sexo = c("Hombres", "Mujeres"),
                               Proporcion = c(prop_hombres, prop_mujeres))

# Graficar las proporciones
ggplot(datos_proporcion, aes(x = Sexo, y = Proporcion, fill = Sexo)) +
  geom_bar(stat = "identity", width = 0.5) +
  geom_text(aes(label = round(Proporcion, 3)), vjust = -0.5) +
  xlab("") +
  ylab("Proporción") +
  ggtitle("Prevalencia de Adiposidad Abdominal en Sobrepeso") +
  theme_minimal()

~~~
<p align="center">
<img src="https://res.cloudinary.com/dilrruxyx/image/upload/v1686600417/2_jps9sl.jpg" alt="mypic" width="65%">
</p>

## Análisis 
Comparando las proporciones estimadas, podemos observar que la proporción de hombres en sobrepeso con ICO por encima de 1.2 (0.738) es significativamente mayor que la proporción de mujeres en sobrepeso con el mismo indicador (0.513). Esto podría indicar una mayor prevalencia de adiposidad abdominal en hombres en sobrepeso en comparación con mujeres en el mismo estado.


## Prueba de hipótesis
~~~R
# Contar el número de hombres y mujeres en sobrepeso con ICO por encima de 1.2
hombres_conicidad <- sum(sobrepeso & datos$sexo == "Hombres" & datos$ICO > 1.2)
mujeres_conicidad <- sum(sobrepeso & datos$sexo == "Mujeres" & datos$ICO > 1.2)

# Calcular las proporciones de hombres y mujeres en sobrepeso con ICO por encima de 1.2
propor_hombres <- hombres_conicidad / sum(sobrepeso & datos$sexo == "Hombres")
propor_mujeres <- mujeres_conicidad / sum(sobrepeso & datos$sexo == "Mujeres")

# Realizar la prueba de hipótesis
prop.test(c(hombres_conicidad, mujeres_conicidad), c(sum(sobrepeso & datos$sexo == "Hombres"), sum(sobrepeso & datos$sexo == "Mujeres")), alternative = "greater")
~~~
## Salida
~~~CMD
X-squared = 387.25, df = 1, p-value < 2.2e-16
alternative hypothesis: greater
95 percent confidence interval:
 0.2060104 1.0000000
sample estimates:
   prop 1    prop 2 
0.7377487 0.5134321 
~~~

El intervalo de confianza del 95% para la diferencia de proporciones se encuentra entre 0.2060104 y 1.0000000, lo que implica que la proporción de hombres en sobrepeso con ICO por encima de 1.2 es estadísticamente mayor que la proporción de mujeres en sobrepeso.

En resumen, los resultados respaldan la afirmación de que hay una diferencia estadísticamente significativa en la prevalencia de adiposidad abdominal entre hombres y mujeres en sobrepeso. Los hombres en sobrepeso presentan una proporción más alta de ICO por encima de 1.2 en comparación con las mujeres en la misma condición.


---
3️⃣ - ¿Las mujeres sin pertenencia étnica en normo peso del centro del país, con doble riesgo
cardiovascular en su etapa final de adultez, presentan un peso superior a los 55kg? Interprete los
resultados.
~~~R
library(ggplot2)

# Filtrar los datos para obtener únicamente a las mujeres sin pertenencia étnica en normo peso del centro del país con riesgo cardiovascular en su etapa final de adultez
filtro <- datos$sexo == "Mujeres" & datos$etnia == "Ninguno" & datos$Categoria_IMC == "Normalidad" &datos$Riesgo == "Alto Riesgo" & datos$region == "5.CENTRAL" & datos$edad_anos>= 50

# Crear un data frame con los valores de peso y una variable que indique si es mayor a 55 kg
df <- data.frame(Peso = datos$peso[filtro], Mayor_55kg = datos$peso[filtro] > 55)

# Calcular la proporción de mujeres con peso mayor a 55 kg
proporcion <- sum(df$Mayor_55kg) / nrow(df)


# Crear la gráfica de pastel
ggplot(df, aes(x = "", fill = Mayor_55kg)) +
  geom_bar(width = 1) +
  labs(title = "Proporción de mujeres con riesgo cardiovascular") +
  scale_fill_manual(values = c("steelblue", "gray"), labels = c("Peso > 55 kg", "Peso <= 55 kg")) +
  theme_minimal() +
  coord_polar("y", start = 0)
~~~
<p align="center">
<img src="https://res.cloudinary.com/dilrruxyx/image/upload/v1686601093/3jpg_xuxiqi.jpg" alt="mypic" width="65%">
</p>
---

4️⃣ - Se tiene la hipótesis que los hombres menores de 40 años en normo peso de la población NARP,
sin riesgo cardiovascular, pero con riesgo de adiposidad, presentan cinturas mayores que los
indígenas. ¿Es Cierta la hipótesis? Interprete los resultados.

~~~R
# Filtrar los datos para obtener los hombres menores de 40 años en normo peso de la población NARP
filtro_hombres_NARP <- datos$sexo == "Hombres" & datos$edad_anos <= 40 & datos$Categoria_IMC == "Normalidad" & datos$etnia == "NARP" & datos$Riesgo == "Bajo Riesgo" & datos$ICO >1.2


# Filtrar los datos para obtener los hombres indígenas
filtro_hombres_indigenas <- datos$sexo == "Hombres" & datos$etnia == "Indígena"

# Obtener las medidas de cintura para cada grupo
cintura_hombres_NARP <- datos$cintura[filtro_hombres_NARP]
cintura_hombres_indigenas <- datos$cintura[filtro_hombres_indigenas]

# Realizar la prueba de comparación (prueba t de Student)
resultado <- t.test(cintura_hombres_NARP, cintura_hombres_indigenas, alternative = "greater")

# Imprimir los resultados
print(resultado)

~~~
## Salida
~~~CMD
data:  cintura_hombres_NARP and cintura_hombres_indigenas
t = -2.0805, df = 19.677, p-value = 0.9746
alternative hypothesis: true difference in means is greater than 0
95 percent confidence interval:
 -4.333481       Inf
sample estimates:
mean of x mean of y 
 83.02500  85.39345 
~~~

Los resultados de la prueba de hipótesis indican que el valor p es 0,9746. No se encontró evidencia suficiente para rechazar la hipótesis nula con un nivel de significancia típico de 0.05, ya que el valor p es mayor que el nivel de significancia.

Esto significa que no hay suficiente evidencia estadística para concluir que los hombres menores de 40 años en el promedio de peso de la población NARP, sin riesgo cardiovascular pero con riesgo de adiposidad, tienen cinturas mayores que los hombres indígenas.

Además, se encontró una estimación de la diferencia de medias de cintura de -2.36845, lo que indica que, en promedio, los hombres indígenas tienen cinturas ligeramente mayores que los hombres menores de 40 años en el peso normal de la población NARP. Sin embargo, la estimación tiene un intervalo de confianza que llega hasta el infinito superior, lo que indica una gran incertidumbre en la estimación.



5️⃣ - ¿Las mujeres adultas jóvenes en normalidad y obesidad, con riesgo cardiovascular y sin
pertenencia étnica, presentan diferencias en el índice de conicidad según el estado nutricional?
Interprete los resultados.


~~~R
# Filtrar los datos para obtener las mujeres adultas jóvenes en normalidad y obesidad, con riesgo cardiovascular y sin pertenencia étnica
filtro <- datos$sexo == "Mujeres" & datos$edad_anos > 25 & datos$edad_anos <= 40 & datos$Categoria_IMC %in% c("Normalidad", "Obesidad") & datos$Riesgo == "Alto Riesgo" & datos$etnia == "Ninguno"

# Crear un dataframe con los datos filtrados
datos_filtrados <- data.frame(
  Estado_Nutricional = datos$Categoria_IMC[filtro],
  Indice_Conicidad = datos$ICO[filtro])

# Crear el gráfico de cajas y bigotes
ggplot(datos_filtrados, aes(x = Estado_Nutricional, y = Indice_Conicidad)) +
  geom_boxplot() +
  labs(x = "Estado Nutricional", y = "Índice de Conicidad") +
  ggtitle("Comparación del Índice de Conicidad\nentre Mujeres Adultas Jóvenes en Normalidad y Obesidad") +
  theme_minimal()
~~~
<p align="center">
<img src="https://res.cloudinary.com/dilrruxyx/image/upload/v1686601459/5_kuuozw.jpg" alt="mypic" width="65%">
</p>
