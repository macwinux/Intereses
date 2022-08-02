---
description: Como usar la monada Write the Cats con Spark para recopilar los logs
---

# Write Monad para recopilar logs en Spark

Como ejemplo tomaremos el caso en el que queremos validar el schema de un DataFrame en Spark. Para no validar solo el esquema, pondremos el caso de que necesitamos que al menos uno de los campos sea obligatorio. Se verá más sencillo después, pero para empezar vamos a definir una case class que será nuestra referencia para validar las columnas del esquema de spark.

```scala
case class SchemaColumn(
                         name: String,
                         mandatory: Boolean = true
                       )
```

Para ver un ejemplo, definamos un pequeño DataFrame y una lista de columnas a validar:

```scala
val someDF = Seq(
  (8, "bat"),
  (64, "mouse"),
  (-27, "horse")
).toDF("number", "word")
val schemaColumns = List(
  SchemaColumn("column_that_fail",false),
  SchemaColumn("word", false)
  )
```

Ahora veamos como definir una función que dada un DataFrame y una lista de columnas a validar nos devuelva una monada Writer con el estado:

```scala
def validation(df: DataFrame, schema: List[SchemaColumn]): Writer[List[String], Boolean] = {
    val checkColumns = df.schema.names.map(_.toUpperCase).foldLeft(Writer(List.empty[String],true)){ (wr, name) =>
      schema.map(_.name.toUpperCase).contains(name) match {
        case true => wr
        case false => wr.tell(List(s"Column: $name not in the registration")).map(_ => false)
      }
    }

    val checkMandatory = (schema.exists(_.mandatory.equals(true)) match {
      case true => Writer(List.empty[String],true)
      case false => Writer(List("No mandatory Columns in the registration."),false)
    })

    (for {
      columns <- checkColumns
      mandatory  <- checkMandatory
    } yield if(columns && mandatory) true else false)
  }
```

Ahora veamos el método en acción:

```scala
val valid = validation(someDF, schemaColumns)
```

El ouput de la validación es:

```bash
valid: Writer[List[String], Boolean] = WriterT(
  (
    List(
      "Column: NUMBER not in the registration",
      "No mandatory Columns in the registration"
    ),
    false
  )
)
```

