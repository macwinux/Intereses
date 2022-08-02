---
description: 'Vamos a ver como utilizar la libreria ZIO de scala: https://zio.dev/'
---

# ZIO Hello world

Zio es una librería de scala para programación concurrente. Se puede ver sus principales características en la pagina anterior.

```scala
import zio.*

object Main extends scala.App :
  val program =
  for 
    _ <- console.putStrLn("-" * 100)
    _ <- console.putStrLn("Hello world")
    _ <- console.putStrLn("-" * 100)
  yield ()

  //run it three times
  Runtime.default.unsafeRunSync(program)
```

Con zio.console podemos llamar al método putStrLn que nos permite escribir por pantalla. Serie el metodo println para zio.

Para poder correr el problema hay que llamar al Objeto Runtime con sus distintos métodos.

