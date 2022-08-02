---
description: >-
  La base de la programación funcional son estas dos clases, de las que heredan
  las otras
---

# Monoids y Semigroups

Estas clases nos permiten añadir y combinar valores.

### Definición de Monoid

Para un tipo A, un monoid es:

* una operación de combinación del tipo: (A, A) => A
* un elemento vacio del tipo A.

```scala
trait Monoid[A] {
    def combine(x: A, y: A): A
    def empty: A
}
```

También un monoid necesita obedecer  una serie de leyes, asociativa y la ley de la identidad:

```scala
def leyAsociativa[A](x: A, y:A, z:A) (implicit m: Monoid[A]): Boolean = {
    m.combine(x, m.combine(y,z)) == m.combine(m.combine(x,y), z)
}
def leyIdentidad[A](x:A) (implicit m: Monoid[A]): Boolean = {
    (m.combine(x, m.empty) == x) && (m.combine(m.empty, x) == x)
}
```



### Definición de Semigroup

Un semigroup es simplemente la parte de combinación del Monoid. Mientras que muchos semigroups son también Monoids, no todos los tipos de datos contienen el elemento vacio:

```scala
trait Semigroup[A] {
    def combine(x: A, y: A): A
}

trait Monoid[A] extends Semigroup[A] {
    def empty: A
}
```
