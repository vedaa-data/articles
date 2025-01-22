# Scala formatting style guide (WIP)

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

Group imports by package name, with more general packages towards the top, and more specific towards the bottom:

```scala
//  correct
import scala.util.*
import scala.concurrent.*

import java.time.*
import java.time.format.*

import org.hammers.HammerFactory
import org.hammers.model.Hammer

import com.mycompany.mylib.*
```

```scala
/*  wrong
import scala.concurrent.*
import org.hammers.HammerFactory
import scala.util.*
import com.mycompany.mylib.*
import java.time.format.*
import org.hammers.model.Hammer
import java.time.*
*/
```

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

