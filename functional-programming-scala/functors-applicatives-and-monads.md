---
description: Pequeña introducción
---

# Functores, Aplicativos y Monadas

## Functores <a href="#functors-applicativesandmonads-functors" id="functors-applicativesandmonads-functors"></a>

Cuando un Valor esta envuelto en un contexto, no puedes aplicar una función normal a dicho valor. Necesitamos para eso un **map.** La función map sabe como aplicar funciones a estos valores que están envueltos en un contexto.

Tenemos un Type Constructor C\[\_] y dos tipos de datos A y B. Queremos aplicar funciones del tipo C\[A] => C\[B], por lo tanto necesitamos transformaciones adecuadas:

```
(A => B) => (C[A] => C[B])
```

Definimos nuestro método **map** como:

```
def map[A,B] (A => B): (F[A] => F[B])
```

## Aplicativos <a href="#functors-applicativesandmonads-applicatives" id="functors-applicativesandmonads-applicatives"></a>

Cuando ambos, un valor y una función están envueltas en un contexto, no podemos aplicar la función como si fuese una función "simple". Necesitamos el método **apply.** Este método sabe como aplicar la función envuelta en el contexto a un valor igualmente envuelto en un contexto.

Tenemos un Type Constructor C\[\_]  y dos tipos A y B, queremos aplicar funciones del tipo C\[A] => C\[B], por lo tanto necesitamos transformaciones adecuadas:&#x20;

```
(C[A => B]) => (C[A] => C[B])
```

Definimos el método **apply** como:

```
def apply[A,B](F[A=>B]):(F[A]=>F[B])
```

## Monadas <a href="#functors-applicativesandmonads-monads" id="functors-applicativesandmonads-monads"></a>

Las monadas aplican una función que returna un valor envuelto de un valor envuelto.

Tenemos un Type Constructor C\[\_]  y dos tipos A y B, queremos aplicar funciones del tipo C\[A] => C\[B], por lo tanto necesitamos transformaciones adecuadas:&#x20;

```
(A => C[B]) => (C[A] => C[B])
```

Definimos el método **flatMap** como:

```
def flatMap[A,B](A => F[B]): (F[A] => F[B])
```

### Ejemplos: <a href="#functors-applicativesandmonads-example" id="functors-applicativesandmonads-example"></a>

\
**Functor**

```scala
class MyBox[T](val value: T) 
//defined class MyBox //Functor 
def map[A,B](myFunction: A=>B): MyBox[A] => MyBox[B] = (a: MyBox[A]) => new MyBox(myFunction(a.value))// map: [A, B](rawfunc: A => B)MyBox[A] => MyBox[B] 
val boxedHello: MyBox[String] = new MyBox("Hello")
// boxedHello: MyBox[String] = MyBox@463561c5 
def lengthFunction(a: String): Int = a.length
// lengthFunction: (a: String)Int 
val transformedLengthMap = map(lengthFunction)
// transformedLengthMap: MyBox[String] => MyBox[Int] = $$Lambda$1116/417621837@f245bdd 
val result: MyBox[Int] = transformedLengthMap(boxedHello)
//result: MyBox[Int] = MyBox@274fdea6 result.value
//res1: Int = 5
```

\
**Monada**

```scala
class MyBox[T](val value: T) 
//defined class MyBox //Monad 
def flatMap[A,B](myFunction: A => MyBox[B]): MyBox[A] => MyBox[B] = (a: MyBox[A]) => myFunction(a.value)
// flatMap: [A, B](func: A => MyBox[B])MyBox[A] => MyBox[B] 
val boxedHello: MyBox[String] = new MyBox("Hello")
// boxedHello: MyBox[String] = MyBox@463561c5 
def lengthFunction(a: String): MyBox[Int] = new MyBox(a.length)
// lengthFunction: (a: String)MyBox[Int] 
val transformedLengthFlatMap = flatMap(lengthFunction)
// transformedLengthFlatMap: MyBox[String] => MyBox[Int] = $$Lambda$1268/1217517351@550574cb 
val result: MyBox[Int] = transformedLengthFlatMap(boxedHello)
//result: MyBox[Int] = MyBox@2ad6aeb8 result.value
//res1: Int = 5
```

**Aplicativo**

```scala
class MyBox[T](val value: T) 
//defined class MyBox 
//Applicative 
val boxedHello: MyBox[String] = new MyBox("Hello")
// boxedHello: MyBox[String] = MyBox@463561c5 
def lengthFunction(a: String): Int = a.length
// lengthFunction: (a: String)Int 
def boxedLengthFunction: MyBox[String => Int] = new MyBox(lengthFunction _)
// boxedLengthFunction: MyBox[String => Int] = MyBox@53b83897 
def apply[A,B] (b: MyBox[A=>B]): MyBox[A] => MyBox[B] = (a:MyBox[A]) => new MyBox(b.value(a.value))
// apply: [A, B](b: MyBox[A => B])MyBox[A] => MyBox[B] 
val transformedLengthApply = apply(boxedLengthFunction)
// transformedLengthFlatMap: MyBox[String] => MyBox[Int] = $$Lambda$1268/1217517351@550574cb 
val result: MyBox[Int] = transformedLengthFlatMap(boxedHello)
//result: MyBox[Int] = MyBox@1a632663 result.value
//res1: Int = 5
```
