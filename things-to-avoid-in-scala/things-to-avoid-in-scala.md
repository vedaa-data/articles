# Things to avoid in Scala

Scala is a lovely programming language with a rich type system that makes the compiler able to prove the correctness of our programs in many ways. Yet there are ways in which one could shoot oneself in the foot.

This article will list constructs in Scala that as a rule should be avoided and explain why. "Avoided" in this context means: don't use them unless you have very good reasons to.

## null

A legacy from Java, `null` is dangerous because if you call a method on it, your program will crash with a `NullPointerException`.

Typically `null` would be use to indicate an empty or a missing value. For instance, if we have a method:

```scala
def getName: String
```

and we don't know the name, we might return a `null`. Then we could:

```scala
val name = getName
println(name.toUpperCase)
```

which would throw an exception if `getName` returns `null`.

Instead, we should change the return type of the method to `Option[String]`:

```scala
def getName: Option[String]
```

In addition to being safer, it is also semantically more correct: it explicitly says that the method may or may not return a name.

Now we can't call any `String` methods on the result, instead we have to map on it:

```scala
val name = getName
println(name.map(_.toUpperCase).getOrElse(""))
```

In the above example, we control the type signature ourselves. However often we do not do that, and especially when working with Java libraries, a method can return `null` or expect `null` as an argument for an empty value, and there is nothing we can do about it. The rule of thumb for handling this is:

* When calling a library method that may return `null`, immediately wrap in into an `Option`.
* When passing `null` to a method, write it explicitly as the argument instead of assigning it to a `val` earlier in the code block.

_When would it still be acceptable to use `null` in your own code?_

`Option` adds some overhead. If you have a tight loop where performance is critical, `null` could be an optimization trick.

## `throw Exception`

Speaking of exceptions, throwing exception is dangerous in general. The reason for this is that they are not tracked by the compiler, so that if some method far down the call chain is throwing an exception, there is no way to know that you may have to catch it just by looking at any type signature. In other words, the code you are writing at one place in your program, which does not throw any exceptions by its own, may still have to catch exceptions thrown by any methods it calls, to be certain that the program will not crash due to any uncaught exceptions.

```scala
def getInitials(name: String): String =
  if name.isEmpty then throw Exception("Cannot compute initials of empty name")
  else name.split(" ").flatMap(_.headOption).mkString
```

```scala
val name = getName
val initials = name.map(_.getInitials) 
```

The code immediately above looks fine on its own - you would have to looks into the implementation of `getInitials` (or look up the documentation, if any) to see that it may throw an exception.

Instead, we should change the return type of `getInitials` to `Try[String]`:

```scala
def getInitials(name: String): Try[String] =
  if name.isEmpty then Failure(Exception("Cannot compute initials of empty name"))
  else Success(name.split(" ").flatMap(_.headOption).mkString)
```

`Try` is very similar to `Option` except that it can hold an `Exception` instead of an empty value.

The logic for dealing with Java libraries that may throw exceptions is very much that same as for `null`: if a method may throw an exception, immediately wrap it in a `Try`. (Or handle it manually with `try`-`catch` if you need more precise control.)

_When would it still be acceptable to throw exceptions in your own code?_

* As long as you make sure to catch it yourself within the local scope and it does not escape your public method.
* For _truly exceptional_ scenarios which can not be recovered from.

## `.get` on `Option`

`.get` on `Option` is unsafe because it will throw an exception if the `Option` is empty. And, as we have seen above, throwing exceptions, either explicitly or by calling methods that can throw, should be avoided.

Instead, use methods like `.foreach`, `.map`, `.flatMap`, and others.

_When would it still be acceptable to use `.get`?_

When you are absolutely certain that the `Option` will have a value, or you are within a scope that will catch any exception.

## `.get` on `Try`

`.get` on `Option` is unsafe because if the `Try` is a `Failure`, then it will simply throw the exception that the `Failure` contains. 

## `.apply` and `.head` on `List`

This is in the same category as `.get` on `Option` and `Try`. Consider:

```scala
val list = List("a", "b", "c")
val item = list(5)
```

This will attempt to get the 5th element from a list with only 3 elements, which will throw an exception.

(Note that `list(5)` syntax is short for `list.apply(5)`).

Taking the `.head` of a list turns out to be empty will also throw an exception.

## `asInstanceOf`

`asInstanceOf` is probably the most dangerous construct there is, as it completely bypasses the type system:

```scala
"hello".asInstanceOf[Int]
```

This will try to cast a `String` to an `Int` at runtime, which will of course not succeed, and as such the program will crash.

It should never be used in application code.

_When would it still be acceptable to use a `asInstanceOf`?_

In low-level library code, in scenarios where the compiler is not able to track the types, and instead operates on top-level types like `Any`.

## `var`

`var`, ie. a mutable variable, is not dangerous in the same way as the above constructs (which can make the program crash at runtime).

However, mutability can make a program hard to read, understand, and maintain. This is especially so if the variable is globally available.

Instead, use a `val`.

_When would it still be acceptable to use a `var`?_

In a short local scope only, either for performance reasons or if it makes that particular piece of code easier to read.

## Mutable collections

Although not really dangerous, mutable collections are in the same category as `var`: they should be avoided because mutability adds complexity. Instead, favor immutable collections if possible.