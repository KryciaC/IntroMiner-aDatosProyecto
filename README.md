# Minería de datos - Krycia Castillo 

## 

# Proyecto Parte 1
## Análisis de datos - Servicios hospitalarios consulta interna

## Datos generales
En este proyecto se están utilizando los datos disponibles en la página del INE (Instituto Nacional de Estadística de Guatemala), se muestra los datos sobre los servicios internos de los distintos hospitales, sanatorios y casas de salud del sector privado. Los datos disponibles corresponden a los años del 2009 a 2022. Link: `https://www.ine.gob.gt/estadisticas-hospitalarias/`

## Información del código 

Para usar este código se puede correr un chunk tras otro ya que estan colocados en orden. 

Para iniciar se debe instalar y llamar a las librerías necesarias para realizar este proyecto, conforme se avanza en el proyecto se agregarán las liberías correspondientes. En caso no tenerlas se deben instalar. Se utilizarán las siguientes librerias:

**Librerias** 

install.packages("haven")

library(haven)

library(arules)

library(fim4r)

library(dplyr)

library(ggplot2)

**Carga de datos**

#Los datos lestán disponibles en un archivo por año. Los archivos se encuentran en formato SPSS (.sav), se procede a leer los archivos utilizando read_sav para lo cual se debe instalar previamente la librería "haven". En este caso el notebook de R se ha guardado en la misma carpeta donde están los archivos por lo cual no se especifica la ubicación del archivo, unicamente el nombre, en caso que el archivo esté en otra carpeta se debe especificar la ubicación correspondiente:

Ej. 

```
data2009 <- read_sav("Datos 2009.sav")
```

**Revisión de datos**

Se puede utilizar str (NOMBRE_DEL_DATASET) para revisar la estructura como se muestra acontinuación:

```
str(data2009)
```

En este caso se ha creado un dataset por cada año y se ha revisado cada uno, se identificaron diferentes diferencias y se modificaron utilizando rename, attr( ), mutate. Tomar en cuenta que al cargar los archivos .sav se genera un dataset que incluye las etiquetas de las clasificaciones incluidas en el data set, si se modifica el código de alguna categoría se debe modificar la etiqueta correspondiente también. 

Se puede revisar la información disponible en los datasets utilizando:
```
summary(dataset)

table(dataset$variable a revisar)
```

Posterior a la revisión se identifico algunas variables que que contenían valores sin categoría por lo cual se procedió a eliminarlas. 

Luego de realizar la limpieza correspondiente de los datos se unió los todos años en un único dataset - usando la librería dplyr y bind_rows:

```
data <- bind_rows(data2009, data2010, data2011, data2012, data2013, data2014, data2015, data2016, data2017, data2018, data2019, data2020, data2021, data2022)
```

En este caso posterior a la union de los datasets se realizó un última modificación a los datos Debido a que la edad se muestra en 2 columnas, EDAD donde se muestra el valor numérico de la edad y PERIODOEDA donde se indica si son días, meses o años. Para facilidad en el análisis posterior se procederá agregar una nueva columna donde todas las edades se muestren en años. 


**Aplicación del algoritmo Apriori**

para este algoritmo necesitamos la librería Arules, se debe considerar que para aplicar este algoritmo todas las varialbes deben tener más de un valor, en caso que haya alguna que solo tenga un valor se debe eliminar para hacer el análisis:

Ej.

```
reglasap <- apriori(data, parameter = list(support=0.2, confidence=0.5 )) - Aplicación del algoritmo, se debe seleccionar el soporte y la confianza

reglasfAp <- as(reglasap, "data.frame") - Creación de un dataframe para revisión de las reglas generadas
```
Se puede filtrar el dataset de acuerdo con lo que se vaya identificando en las reglas para realizar más análisis. 

**Aplicación del algoritmo FPGrowht**
Para aplicar el algoritmo FPGrowth para analizar todo data set, para realizar este análisis necesitamos la librería fim4r (tomar en cuenta que para instalar esta librería se debe instalar previamente RTools de la versión correspondiente de R)

EJ. 
```
reglasfp <- fim4r(dataNA, method = "fpgrowth", target="rules", supp = .2, conf = .5) - Aplicación del algoritmo, se debe seleccionar el soporte y la confianza

reglasffp <- as(reglasfp, "data.frame") - Creación de un dataframe para revisión de las reglas generadas
```

** Clusters**

Para encontrar clusteres vamos a utilizar Kmeans, debe considerar que para aplicar este algoritmo no debemos tener valores NA o Character, deben modificarse antes de aplicarlo. 
Ej. 
```
dataC1 <- na.omit(dataNAsincond)
dataC1 <- dataC1 %>%
  select( -CAUFIN)

cluster <- kmeans(dataC1, centers=4) - Se debe seleccionar cuántos centros se analizarán en los clusters
```

**Grafica de clusteres**

Se procede a graficar los clusteres, para realizarlo es necesario contar con la libreria ggplot, se selecciona las variables que se considerarán para la gráfica con respecto al eje x y el eje y como se muestra a continuación:

Ej. 

```
ggplot(dataC1, aes(x =AÑO, y = DIASESTANCIA, color = as.factor(cluster$cluster)))+
  geom_point()+
  geom_point(data = as.data.frame(cluster$centers), aes(x =AÑO, y = DIASESTANCIA), color = "black", size=4, shape=17)+
  labs(title = "Servicios Internos - Año VS Días de estancia")+
  theme_minimal()
```
