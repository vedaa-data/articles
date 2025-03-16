# Scala formatting style guide (WIP)

This is an opinionated guide to how to best format Scala code.

"Formatting" the code could be defined as any changes to the source code that does not result in any changes to the compiled code.

When formatting the code, there are at least two goals:

* Consistency
* Readability

Any formatting style that is *consistent*, is better than no formatting style. 

However, different styles of consistent formatting could result in different degrees of readability. What is easily readable and not might also be a bit subjective, hence "opinionated".

## Imports

Prefer wildcard imports for very common imports, or when there are a large number from the same package:

```scala
//  prefer
import scala.util.*
import javafx.scene.control.*
```

```scala
/*  avoid
import scala.util.{ Try, Success, Failure }
import javafx.scene.control.{ Label, Button, Menu, ComboBox, TableView, ListView, ScrollPane }
*/
```

Conversely, use specific imports for uncommon or a small number of imports:

```scala
//  prefer
import scala.collection.Factory
import org.hammers.{ HammerWriter, HammerReader }
```

Group imports by package name, with more general packages towards the top, and more specific towards the bottom:

```scala
//  prefer
import scala.util.*
import scala.concurrent.*

import java.time.*
import java.time.format.*

import org.hammers.HammerFactory
import org.hammers.model.Hammer

import com.mycompany.mylib.*
```

```scala
/*  avoid
import scala.concurrent.*
import org.hammers.HammerFactory
import scala.util.*
import com.mycompany.mylib.*
import java.time.format.*
import org.hammers.model.Hammer
import java.time.*
*/
```

## Capitalization

* Classes, types etc.: `PascalCase`
* Variables: `camelCase`
* Constants: `PascalCase`
* Packages: `lowercase`

Avoid: `snake_case`, `UPPER_SNAKE_CASE`.

## Spacing

### Horizontal

Use two spaces for each indentation level:

```scala
//  correct
case class Person(name: String, age: Int):
  def render =
    s"The person's name is $name and their age is $age"
```

```scala
/*  wrong
case class Person(name: String, age: Int):
    def render =
          s"The person's name is $name and their age is $age"
*/
```

Use a space after type the type ascription colon and no space before it:

```scala
//  correct
def multiply(a: Int, b: Int): Int
```

```scala
/*  wrong
def multiply(a : Int, b:Int) :Int
*/
```

Use a space after commas:

```scala
//  correct
List(1, 2, 3, 4, 5)
```

```scala
/* wrong
List(1,2,3,4,5)
*/
```

Use no space after openening and before closing parentheses:

```scala
//  correct
List(1, 2, 3, 4, 5)
```

```scala
/*  wrong
List( 1, 2, 3, 4, 5  )
*/
```

Use no space before parameter lists:

```scala
//  correct
def foo(a: Int)
```

```scala
/*  wrong
def foo (a: Int)
*/
```

Use a space before and after operators:

```scala
//  correct
a + b
```

```scala
/*  wrong
a+b
*/
```

### Vertical

For short blocks, don't use any blank lines:

```scala
//  correct
def createHammer =
  val materials = getMaterials
  val process = getManufacturingProcess
  process(materials)
```

```scala
/*  wrong
def createHammer =

  val materials = getMaterials

  val process = getManufacturingProcess

  process(materials)
*/
```

## Line wrapping

For class/method definitions with a large number of parameters and multiple parameter lists, it can be a bit tricky to figure out where to put the punctuation (parentheses).

A good rule of thumb is that the punctuation should stick out as little as possible.

Here is an example:

```scala
//  prefer
def logged[A, D]
  (name: String, 
  logger: Logger)
  (f: Connector[D] ?=> A)
  (using connector: Connector[D]): A =
    f(using LoggingConnector(connector, logger))    
```


```scala
/*  avoid
def logged[A, D]
  (name: String,
  logger: Logger
)(
  f: Connector[D] ?=> A)(
  using connector: Connector[D]
): A =
  f(using LoggingConnector(connector, logger))    
*/
```

The most important to avoid is a combination of punctuation characters alone on a single line (e.g. `)(` above). The reason for this is that:

* It is "visual noise", or a distraction
* It can lead beginners to ask "what does the `)(` syntax mean in Scala?"


## Blocks

Prefer braceless style wherever possible.

```scala
//  prefer
object Workplace:
  val employees = List("Ann", "Bob", "Chris")
  if employees.size == 1 then
    val message = "One-man show"
    println(message)
  else employees.foreach: x =>
    println(x)
```

```scala
/*  avoid
object Workplace {
  val employees = List("Ann", "Bob", "Chris")
  if (employees.size == 1) {
    val message = "One-man show"
    println(message)
  }
  else employees.foreach { x =>
    println(x)
  }
}
*/
```

## Lambdas

For short lambdas, use underscore style:

```scala
//  prefer
val names = persons.map(_.name)
```

```scala
/*  avoid
val names = persons.map(x => x.name)
*/
```

For longer one-line lambdas, use parenthesis style:

```scala
//  prefer
val employeePersons = persons.filter(person => employees.exists(_.personId == person.id))
```

```scala
/*  avoid
val employeePersons = persons.filter { person => employees.exists(_.personId == person.id) }
*/
```

For multi-line lambdas, use colon style, with the parameter(s) on the same line as the method:

```scala
//  prefer
val formattedNames = persons.map: person =>
  val name = person.name
  s"The person's name is $name"
```

```scala
/*  avoid
val formattedNames = persons.map { person =>
  val name = person.name
  s"The person's name is $name"
}
*/
```

```scala
/*  avoid
val formattedNames = persons.map(person =>
  val name = person.name
  s"The person's name is $name"
)
*/
```

```scala
//  prefer
```


```scala
/*  avoid
*/
```
