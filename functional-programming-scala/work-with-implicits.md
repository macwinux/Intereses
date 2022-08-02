---
description: Ejercicio similar al del libro de Cats
---

# Trabajar con implicitos

Como en [Scala with Cats](https://underscore.io/books/scala-with-cats/), voy a trabajar con implicitos , pero de manera sencilla.

Quiero crear una librería Printable, pero con más concisa

Definimos el trait para Printable:

```scala
trait Printable[A] {
    def format(value:A): String
}
object Printable {
    def format[A](input: A)(implicit p: Printable[A]): String = p.format(input)
    def print[A](input: A)(implicit p: Printable[A]): Unit = println(format(input))
}
```

_Luego, para usar format y print, necesitamos el objeto implicito p. Vamos a crear algunos de ellos para String, Int o Boolean:_&#x20;

```scala
object PrintableInstances {
    implicit val stringPrintable = new Printable[String] {
        def format(input: String) = input
    }
    implicit val intPrintable = new Printable[Int] {
        def format(input: Int) = input.toString
    }
    implicit val booleanPrintable = new Printable[Boolean] {
        def format(input: Boolean) = input.toString
    }
}
```

_Ahora, tenemos tres val implicitos en un object, para usarlos, simplemente importamos el objeto, es decir, PrintableInstances:_

```scala
val hello = "hello"
Printable.print(hello)
/*<console>:14: error: could not find implicit value for parameter p: Printable[String]
       Printable.print(hello)
*/
import PrintableInstances._
Printable.print(hello)
//hello
```

Pero, podemos hacerlo incluso más legible:

```scala
hello.print
//hello
```

Para ello, necesitamos crear una clase implícita:

```scala
object PrintableSyntax {
    implicit class PrintableOps[A] (value: A){
        def format(implicit p: Printable[A]): String = p.format(value)
        def print(implicit p: Printable[A]): Unit = println(p.format(value))
    }
}
```

Y luego, simplemente importarla

```scala
import PrintableInstances._
import PrintableSyntax._
val bol = false
bol.print
//false
```

Pero eso no es todo. Podemos definir nuestros propios tipo de datos:

```scala
final case class User (name: String, age: Int, single: Boolean)
object User{
    implicit val userPrintable = new Printable[User] {
        def format(user: User): String = {
            import  PrintableInstances._
            val name = Printable.format(user.name)
            val age = Printable.format(user.age)
            s"The user ${name}, has ${age} years old."
        }
    }
}
```

y luego imprimir un usuario:

```scala
import User._
import PrintableSyntax._
val user = User("Daniel", 30, false)
user.print
//The user Daniel, has 30 years old.
```
