---
description: Diferentes utilidades del PT
---

# Pattern Matching

Podemos usar el patter matching para diferentes situaciones donde nos puede ser realmente útil.

* Sirve para métodos recursivos:

```scala
def sumList(num: List[Int]): Int = numbers match {
    case Nil => 0
    case h :: t => h + sumList(t)
}
```

* Cuando solo te interesa un elemento de la lista en una determinada posicion:

```scala
list match {
    case List (_, _, 3, _*) => "Solo me importa el tercer elemento"
}
```

* Si solo nos interesa el último valor:

```scala
list match {
    case List(1,_*) :+ 2 => "Me interesa el dos final"
}
```

* Podemos machear por tipo de dato, se utiliza mucho en las excepciones:

```scala
exception match {
    case _: IOException => "Es un fallo de IO"
    case _: RunTimeException => "Algo se ha roto"
}
```

* Name Binding

```scala
case class Perro (sonido: String, nombre: String)
val p1 = Perro("guau", "Toby")
val p2 = p1 match {
    case p @ Perro(sonido,nombre) => s"El perro $nombre hace $sonido"
        p.copy(nombre = "Dobby")
}
```
