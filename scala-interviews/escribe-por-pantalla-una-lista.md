---
description: Escribe por pantalla una lista de objetos de forma recursiva
---

# Escribe por pantalla una lista

```scala
object StringTailRecExercice extends App{

    def printList[T](list: List[T]): String = {
      //@tailrec
      def toStringTailrec(remaining: List[T], result: String): String = ???
      ???
    }

    val list: List[Int] = List(1,2,3)
    println(printList(list))
}
```

Para resolver este problema debemos crear un metodo recursivo dentro del propio metodo printList:

```scala
import scala.annotation.tailrec
object StringTailRecExercice extends App{

    def printList[T](list: List[T]): String = {
      @tailrec
      def toStringTailrec(remaining: List[T], result: String): String = {
        if (remaining.isEmpty) result
        else if (remaining.tail.isEmpty) s"$result${remaining.head}"
        else toStringTailrec(remaining.tail, s"$result${remaining.head}, ")
      }
      "[" + toStringTailrec(list, "") + "]"
    }

    val list: List[Int] = List(1,2,3)
    println(printList(list))
}
```
