---
description: >-
  Cuando no necesitamos restricciones de herencia, pero queremos un modo de
  convertir el tipo a otro, pues podemos usar los view  bounds.
---

# View Bounds

Su notación es <%.

Veamos un ejemplo muy parecido a un post de [Scalera](https://scalerablog.wordpress.com/category/castellano):

```scala
def printListInDouble [T <% Double] (double: Set[T]): Unit =
    double.foreach(d => println(d +2.0))
```

Esto significa que nuestro método está parametrizado por un tipo T y que debe haber una función de conversión de T a Double en nuestro scope.&#x20;

Sin syntactic sugar sería algo así:

```scala
def printListInDouble[T](double: Set[T])(implicit conv:T => Double): Unit =
    double.foreach(d => println(conv(d) + 2.0))
```

Esto funcionará perfectamente para el tipo Int, por ejemplo, ya que tiene una función de conversión directa:

```scala
printListInDouble(Set(1,2)) 
```

&#x20;pero para el tipo Boolean por ejemplo deberemos definirla:&#x20;

```scala
implicit def toD(b: Boolean): Double = if (b) 1.0 else 0.0
printListInDouble(Set(true,false))
```
