---
description: Hay situaciones en las que querremos routear mensajes a uno o más actores.
---

# Routing Messages

Algunas de las situaciones pueden ser las siguientes:

* Enviar mensajes al actor que menos ocupado esté.
* Enviar mensajes en un orden round-robin a los actores.
* Enviar un mensaje a todos los actores del grupo.
* Redistribuir el trabajo a lo largo de los actores de forma automática.

#### SmallestMailboxPool

Se entrega al actor del pool con menor número de mensajes en su mailbox.

#### BalancingPool

Intenta redistribuir el trabajo a lo largo de los actores para obtener una mejora en el rendimiento.

#### RoundRobinPool

En este tipo de pool todos los actores comparten el mismo mailbox.&#x20;

**BroadCastPool**

Es usando cuando queremos enviar el mismo mensaje a todos los actores del pool.

#### ScatterGatherFirstCompletedPool

Envía el mismo mensaje a todos los actores, espera por el que primero complete el trabajo y envía una respuesta a este. Descarta las respuestas de los otros.

#### TailChoppingPool

Primero envía el mensaje a uno, elegido de forma aleatoria, y luego, después de un poco delay, lo envías a un segundo, y así sucesivamente.&#x20;

#### ConsistentHashingPool

Usa un hashing consistente para enviar mensajes a los actores.

