# Databricks Notes

En este README estan las notas generales de como usar Databricks y pySpark.

## 1. Spark Context

Es el corazon de cualquier aplicacion de Spark, establece la cocexión al ambiente de ejecución de Spark, atravez de este objeto podemos crear RDD's,
acceso a los servicios de spark y mucho mas, spark context permite al programa driver acceder al clouster atravez del Resourse Manager, el 
programa driver corre las operaciones en los nodos, el Spark context usa *Py4j* para lanzar una JVM la cual crea un Java Spark Context

### SparkContextParameters

- Master
- appName
- sparkHome
- pyFiles
- Enviroment
- batchSize
- Serializer
- conf
- Gateway
- ISC
- Profiler_cls

Los mas comunmente usados son *Master* y *appName*

### BasicLifeCycle

[1. Create RDD`s](#1-Create-RDD`s)
  - Crea RDD`s de alguna fuente externa de datos ó collección en el 
  programa driver 
[2. LazyTransformation](#2-Lazy-Transformation)
  - Convierte los RDD´s base en nuevos RDD`s usando transformaciones
[3. Cache RDD`s](#3-Cache-RDD´s)
  - Guarda algunos RDD´s para un futuro reuso
[4. Perform Actions](#4-Perform-Actions)
  - Prepara algunas acciones para ejecutarn en computo en paralelo y producir resultados

## 2. RDD´s
  - Resilient: Tolerante a fallas y es capaz de reconstruir datos en un fallo
  - Distributed: Los datos estan distribuidos en varios nodos del clouster
  - Dataset: Coleccion particionada de datos con valores primitivos

    Los RDD´s son inmutables, lo que quiere decir que no puede cambiar su estado despues de ser creado
    pero es posible aplicar ciertas transformaciones que daran como resultado otros RDD´s 
    Es posible aplicar multiples operaciones en los RDD´s  los cuales son categorizados de la siguiente manera

### Transformations

Son operaciones que se aplican a un RDD para crear un nuevo RDD

- map
- flatmap
- filter
- distinct
- reduceByKey
- mapPartition
- sortBy

### Actions
- collect
- collectAsMap
- reduce
- countByKey/countByValue
- take
- first

## 3. Broadcast & Accumulator

- son variables compartidas en todos los nodos

- Broadcast: Estas variables son usadas para guardar una copia de los datos en todos los nodos
- Accumulator: Estas variables son usadas para agregar información atravez de operacines asociativas y conmutativas

## 4. Spark Configuration
Provee las configuraciones y parametros que son necesarios para ejecutar la aplicacion de spark en el sistema local o en cualquier clouster
```java
class SparkConf{
  loadDefaults = TRUE,
  _jmv = None,
  _jconf = None,
}
```

- Atributes of SparkConf class

 set(key, value) ------------------------------------ Sets Config property

 setMaster(value) ----------------------------------- Sets the master URL
 
setAppName(key, value) ------------------------------ Sets an aplication`s name
 
get(key, defaultValue=None) ------------------------- Gets the configuration value of a key

setSparkHome(value) --------------------------------- Sets the Spark instalation path on worker nodes

## 5. Spark Files

sparkFiles class contiene metodos para obtener la ruta de los archivos agregados a spark

 get(filename) ------------------------------------ It specifies the path of the file that is added through sc.addFile()

 getrootdirectory() -------------------------------- It specifies the path to the root directory of the file that is added through sc.addFile()

## 6. Data Frames

Dataframes: Colección distribuida de renglones que es similar a una Tabla de una base de datos relacional ó a una hoja de excel

[File location and type]
file_location = "/FileStore/tables/WA_Fn_UseC__Telco_Customer_Churn.csv"
file_type = "csv"

[CSV options, Infer Schema infiere el tipo de dato directamente del dataset, first_row_is_header indica que la primera fila coorresponde a el nombre de cada columna y delimiter indica cual es el delimitador de los datos]
infer_schema = "true"
first_row_is_header = "true"
delimiter = ","

[The applied options are for CSV files. For other file types, these will be ignored.]
df = spark.read.format(file_type) \
  .option("inferSchema", infer_schema) \
  .option("header", first_row_is_header) \
  .option("sep", delimiter) \
  .load(file_location)

nycFlights_df = spark.read.csv("file://home/edureka/Downloads/nycflights.csv", inferSchema = True, header=True)

Muestra en forma de texto plano los primeros 20 registros del dataframe
nycFlights_df.show()

Imprime el Esquema del dataframe
nycFlights_df.printSchema()

muestra el numero de registros en el Dataframe
nycFlights_df.count()

Realiza un SELECT del dataframe como si fuese una tabla de una base de datos
nycFlights_df.select("flight","origin","dest").show()

Muestra información de una columna en particular, si es que esta columna esta comprendida por datos numericos entonces se muestran el valor maximo, el valor minimo, el numero de renglones, la media y la desviacón estandar
nycFlights_df.describe("distance").show()


Muestra los registros que cumplan con la condicion en la cual la distancia es igual a 17
nycFlights_df.filter(nycFlights_df.distance == "17").show()

Muestra los registros que cumplan con la condicion en la cual el origen es "EWR"
nycFlights_df.filter(nycFlights_df.origin == "EWR").show()

Usando la clausula WHERE la cual tiene la misma funcion que "Filter"
nycFlights_df.where(nycFlights_df.day == 2).show()

Multiples parametros
nycFlights_df.where((nycFlights_df.day == 7) & (nycFlights_df.origin == "JFK") & (nycFlights_df.arr_delay < "0")).show()

Crea una tabla temporal para realizar consultas SQL en dicha tabla temporal
nycFlights_df.registerTempTable("NYC_Flights")

sqlContext.sql("SELECT * from NYC_Flights").show()

[Muestra la onformaciòn en una tabla]
display(df)

