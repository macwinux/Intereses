---
description: Como funciona un for comprehension con Mónadas, por ejemplo, la mónada Option
---

# For Comprehension

Como funciona un for comprenhension es simple. Es como si realizases flatMap uno tras otro con un map final. Por ejemplo:

```scala
val a = Some(1)
val b = Some(3)
val value = for {
val value1 <- a
val value2 <- b
} yield value1 + value2
println(value) //4
```

Es como si hacemos un flatMap + un map:

```scala
val value = a.flatMap{value1 =>
  b.map{ value2 =>
    value1 + value2
  }
}
println(value) //4 
```

Pero fijemonos en este caso:

```scala
val a = Some(1)
val b = Some(3)
val c = None


val value = for {
  value1 <- a.convertToZero()
  value2 <- b.convertToZero()
  value3 <- c.convertToZero()
} yield value1 + value2 + value3
//ERROR
```

Nos damos cuenta de que no compila porque el valor c es del tipo None.type y es ambiguo. Podemos resolverlo:

```scala
val a = Some(1)
val b = Some(3)
val c: Option[Int] = None
val value = for {
  value1 <- a
  value2 <- b
  value3 <- c
} yield value1 + value2 + value3
println(value) // None 
```

De esta manera el compilador sabe que es c es del tipo Option\[Int]. Pero vemos que la suma no la hace correctamente porque nos devuelve un None, y eso no es lo que queremos. Pero podemos hacer una triquiñuela tal que:

```scala
implicit class SumZero(val value: Option[Int]) {
  def convertToZero(): Option[Int] = value match {
    case Some(x) => Some(x)
    case None => Some(0)
  }
}
val a = Some(1)
val b = Some(3)
val c = None
val value = for {
  value1 <- a.convertToZero()
  value2 <- b.convertToZero()
  value3 <- c.convertToZero()
} yield value1 + value2 + value3
println(value) //Some(4)
```

En caso de que sea None, podemos transformar ese valor en un 0. Y además ya no necesitamos definir el valor c como un Option de Int.
