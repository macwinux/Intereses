---
description: Apuntes sobre las transformaciones en Spark del libro High Performance Spark
---

# Effective Transformations

### Narrow Vs Wide Transformations

Wide transformations son aquellas transformaciones que requieren shuffle (distribución entre los nodos de datos) mientras que las narrow no lo necesitan, por eso son preferibles estas últimas.

&#x20;Las narrow son transformaciones en las que cada partición del RDD padre es usado a lo sumo por una partición del RDD hijo. Por el contratio, las wide son transformaciones en las que multiples RDD hijos pueden depender de cada partición en el padre.

#### Narrow dependency

```scala
val rdd2 = rdd1.map(x=>(x,1))
```

#### Wide dependency

```scala
val rdd3 = rdd12.groupByKey
```

Las dependencias narrow no requieren que los datos sean movidos entre particiones. Por lo tanto, no requieren comunicación con el driver. Es decir, en Spark, una serie de transformaciones narrow pueden ser computadas en el mismo stage en el plan de ejecución de Spark.

Por otro lado, el shuffle asociado a una dependencia wide maca un nuevo Stage en Spark.

El conste de un fallo en una dependencia wide es mucho mayor ya que requiero que más particiones sean recomputadas.

Si por ejemplo solo una partición el padre de un mappedRDD falla, solo su hijo debe ser recomputado y esa task puede ser distribuida a lo largo de los executors para hacer esta recomputación más rápida. Por lo tanto el coste será menor para una dependencia narrow que para una wide.

### Minimizar la creación de objetos

GC es el proceso de liberar la memoria alojada para un objeto una vez este ya no es necesario. Desde que Spark corre en la JVM, esto se hace de forma automática y puede convertirse en una parte muy importante de nuestro job de Spark.

Algunas transformaciones RDD nos permiten transformar los parámetros en las expresiones lambda en vez de crear nuevos objetos.

Una manera simple de reducir el costo de la creación de nuevos objetos puede ser usar el patrón de diseño this.type de scala.

Vamos a ver dos ejemplos que hacen los mismo, uno utilizando el patrón y otro creando nuevas instancias.

Sin el patrón:

```scala
class MetricsCalculator(
val totalWords : Int,
val longestWord: Int,
val happyMentions : Int,
val numberReportCards: Int) extends Serializable {
    def compOp(other:MetricsCalculator) : MetricsCalculator = {
        new MetricsCalculator(
           this.totalWords + other.totalWords,
           Math.max(this.longestWord, other.longestWord),
           this.happyMentions + other.happyMentions,
           this.numberReportCards + other.numberReportCards)
    }
}
```

Con el patrón:

```scala
class MetricsCalculator(
var totalWords : Int,
var longestWord: Int,
var happyMentions : Int,
var numberReportCards: Int) extends Serializable {
    def compOp(other:MetricsCalculator) : this.type = {
        totalWords += other.totalWords,
        longestWord = Math.max(this.longestWord, other.longestWord)
        happyMentions += other.happyMentions
        numberReportCards += other.numberReportCards
        this
    }
}
```

### Usar Estructuras de Datos Pequeñas o Primitivas

Una importante manera de optimizar los jobs de Spark tanto en tiempo como en espacio es usar tipos primitivos de datos en vez de clases customizadas.

Los arrays de Scala son las colecciones más eficientes en memoria en Scala.

En vez de los ejemplos anteriores hacer lo siguiente con arrays:

```scala
object MetricsCalculatorArrays extends Serializable {
    val totalWordIndex = 0
    val longestWordIndex = 1
    val happyMentionsIndex = 2
    val numberReportCardsIndex = 3
    
    def compOp(x: Array[Int], y: Array[Int]): Array[Int] = {
        x(totalWordIndex) += y(totalWordIndex)
        x(longestWordIndex) += Math.max(x(longestWordIndex), y(longestWordIndex))
        x(happyMentionsIndex) += y(happyManetionsIndex)
        x(numberReportCardsIndex) += y(numberReportCardsIndex)
        x
    }
```

### Tranformaciones Iterator-to-iterator con mapPartitions

Cuando una transformación directamente coge y devuelve un iterador sin forzar este a otro tipo de colección, la llamamos una transformación iterator-to-iterator.

La principal ventaja de este tipo de transformaciones en Spark es que nos permite distribuir los datos selectivamente. Por lo tanto, nos permite manipular particiones que son demasiado grandes para que quepan en memoria en un solo executor.
