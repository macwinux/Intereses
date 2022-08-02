---
description: Generar un mailbox que tiene un sistema de prioridades
---

# Crear un PriorityMailbox

Podemos generar un mailbox propio que nos permite procesar los mensajes según la prioridad que queramos. Por ejemplo vamos a crear una sería de clases y en función del tipo que recibamos pues tendrán una prioridad.

Para ello primero generamos los tipos de datos que vamos a generar:

```scala
sealed abstract class Priority(message: String)  {
  override def toString: String = message
}
case class Priority0(message: String) extends Priority(message)
case class Priority1(message: String) extends Priority(message)
case class Priority2(message: String) extends Priority(message)
```

Usamos una abstract class para poder sobrescribir el toString y no tener que usar varios case en el actor luego.

Ahora generamos el Mailbox que priorizará los mensajes en la cola:

```scala
class MailboxPrioridad(settings: ActorSystem.Settings, config: Config) extends  UnboundedPriorityMailbox (
  PriorityGenerator {
    case Priority0(_) => 0
    case Priority1(_) => 1
    case Priority2(_) => 2
    case _ => 3
  })
```

Tenemos que añadir esta configuración al application.conf:

```
prioridad-dispatcher {
 mailbox-type = "com.macwinux.MailboxPrioridad"

}
```

Finalmente, podemos crear el actor:

```scala
class MyActor extends Actor {
  def receive: PartialFunction[Any, Unit] = {
    case msg => println(msg)
  }
}
```

Por último creamos la App:

```scala
object CustomPriorityMailBox extends App{
  val actorSystem = ActorSystem("EjemploMailbox")
  val myPriorityActor = actorSystem.actorOf(Props[MyActor].withDispatcher("prioridad-dispatcher"))
  val prio0 = Priority0("Este mensaje es de prioridad 0")
  val prio1 = Priority1("Este mensaje es de prioridad 1")
  val prio2 = Priority2("Este mensaje es de prioridad 2")
  val prio3 = "Este mensaje no tiene prioridad"
  myPriorityActor ! prio3
  myPriorityActor ! prio1
  myPriorityActor ! prio2
  myPriorityActor ! prio1
  myPriorityActor ! prio0
}
```

Y la salida es la siguiente:

```
Este mensaje es de prioridad 0
Este mensaje es de prioridad 1
Este mensaje es de prioridad 1
Este mensaje es de prioridad 2
Este mensaje no tiene prioridad
```
