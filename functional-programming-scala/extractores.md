---
description: Vamos a ver como extraer objetos de una clase
---

# Extractores

Primero vamos a crear un simple **trait**:

```scala
trait Fruta {
    def forma: String,
    def color : String
}
```

Ahora vamos a crear diferentes clases que sean frutas:

```scala
class Manzana ( val forma: String,
    val color: String) extends Fruta
class Pera ( val forma: String,
    val color: String) extends Fruta
```

Ahora, si intentamos acceder con un pattern matching sobre una instancia de una de estas clases fallará:

```scala
val manzana = new Manzana("redonda","rojo")
manzana match {
    case Manzana(forma,color) => println(s"La manzana es $color y $forma")
    case Pera(forma,color) => println(s"La pera es $color y $forma")
}
//error: not found: value Manzana
//error: not found: value Pera    
```

Para poder usar este pattern matching, debemos crear extractores:

```scala
object Manzana {
    def unapply(manzana: Manzana): Option[(String,String)] =
      Some((manzana.forma,manzana.color)) 
}
object Pera {
    def unapply(pera: Pera): Option[(String,String)] =
      Some((pera.forma,pera.color)) 
}
```

Y de esta manera ya si podríamos hacer el pattern matching anterior.
