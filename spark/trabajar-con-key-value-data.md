# Trabajar con Key/Value Data

Las operaciones de pares clave valor pueden causar:

* Errores out-of-memory en el driver.
* Errores out-of-memory en los executors.
* Fallos de shuffles.
* Tareas rezagadas o particiones que son especialmente  lentas de procesar.

&#x20;Estos errores suelen ser, la mayoría de las veces causado por shuffles asociados con los transformaciones wide en las clases PairRDDFunctions y OrderedRDDFunctions.

### Como usar PairRDDFunctions y OrderedRDDFunctions

La clase RDD hace uso de los implicitos de scala, y PairRDDFunctions estará disponible para cualquier RDD del tipo clave valor (K,V). Estos valores pueden ser de cualquier tipo, pero para la clase OrderedRDDFunctions (sortByKey, repartitionAndSortWithinPartitions, filterByRange) k debe tener algún orden implícito.

Para utilizar este último tipo de clase para una case class, por ejemplo, necesitas definir su orden:

```scala
implicit def orderByLocationAndName [A <: PandaKey]: Ordering[A] =  {
    Ordering.by(pandaKey => (pandaKey.city, pandaKey.zip, pandaKey.name))
}
implicit val ordering: Ordering[(K,S)] = Ordering.Tuple2
```

#### Evitar usar GroupByKey

Tanto reduceByKey, treeAggregate, aggregateByKey y foldByKey combinan los registros con la misma clave antes de hacer el shuffled. Esto reduce bastante la etapa de shuffled read de Spark.
