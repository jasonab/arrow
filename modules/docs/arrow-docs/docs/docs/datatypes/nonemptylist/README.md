---
layout: docs
title: NonEmptyList
permalink: /docs/datatypes/nonemptylist/
video: TC6IzE61OyE
---

## NonEmptyList

`NonEmptyList` is a data type used in __Λrrow__ to model ordered lists that guarantee to have at least one value.
`NonEmptyList` is available in the `arrow-data` module under the `import arrow.data.NonEmptyList`

```groovy
// gradle
compile 'io.arrow-kt:arrow-data:$arrowVersion'
```

```kotlin:ank
// namespace
import arrow.data.NonEmptyList
```

## of

A `NonEmptyList` guarantees the list always has at least 1 element.

```kotlin:ank:silent
NonEmptyList.of(1, 2, 3, 4, 5) // NonEmptyList<Int>
NonEmptyList.of(1, 2) // NonEmptyList<Int>
//NonEmptyList.of() // does not compile
```

## head

Unlike `List#[0]`, `NonEmptyList#head` it's a safe operation that guarantees no exception throwing.

```kotlin
NonEmptyList.of(1, 2, 3, 4, 5).head
```

## foldLeft

Whe we fold with turn a `NonEmptyList< A >` into `B` by providing a __seed__ value and a __function__ that carries the state on each iteration over the elements of the list.
The first argument is a function that addresses the __seed value__, this can be any object of any type which will then become the resulting typed value.
The second argument is a function that takes the current state and element in the iteration and returns the new state after transformations have been applied.

```kotlin:ank
fun sumNel(nel: NonEmptyList<Int>): Int =
  nel.foldLeft(0) { acc, n -> acc + n }

sumNel(NonEmptyList.of(1, 1, 1, 1))
```

## map

`map` allows us to transform `A` into `B` in `NonEmptyList< A >`

```kotlin:ank
NonEmptyList.of(1, 1, 1, 1).map { it + 1 }
```

## flatMap

`flatMap` allows us to compute over the contents of multiple `NonEmptyList< * >` values

```kotlin:ank
val nelOne: NonEmptyList<Int> = NonEmptyList.of(1)
val nelTwo: NonEmptyList<Int> = NonEmptyList.of(2)

nelOne.flatMap { one ->
  nelTwo.map { two ->
    one + two
  }
}
```

## Monad binding

Λrrow allows imperative style comprehensions to make computing over `NonEmptyList` values easy.

```kotlin
val nelOne: NonEmptyList<Int> = NonEmptyList.of(1)
val nelTwo: NonEmptyList<Int> = NonEmptyList.of(2)
val nelThree: NonEmptyList<Int> = NonEmptyList.of(3)

NonEmptyList.monad().binding {
    val one = nelOne.bind()
    val two = nelTwo.bind()
    val three = nelThree.bind()
    yields(one + two + three)
}.ev()
// NonEmptyList(6)
```

Monad binding in `NonEmptyList` and other collection related data type can be used as generators

```kotlin
NonEmptyList.monad().binding {
    val x = NonEmptyList.of(1, 2, 3).bind()
    val y = NonEmptyList.of(1, 2, 3).bind()
    yields(x + y)
}.ev()
// NonEmptyList(all=[2, 3, 4, 3, 4, 5, 4, 5, 6])
```

## Applicative Builder

Λrrow contains methods that allow you to preserve type information when computing over different `NonEmptyList` typed values.

```kotlin:ank
import arrow.data.*
import arrow.syntax.applicative.*
import java.util.*

data class Person(val id: UUID, val name: String, val year: Int)

// Note each NonEmptyList is of a different type
val nelId: NonEmptyList<UUID> = NonEmptyList.of(UUID.randomUUID(), UUID.randomUUID())
val nelName: NonEmptyList<String> = NonEmptyList.of("William Alvin Howard", "Haskell Curry")
val nelYear: NonEmptyList<Int> = NonEmptyList.of(1926, 1900)

NonEmptyList.applicative().map(nelId, nelName, nelYear, { (id, name, year) ->
  Person(id, name, year)
})
```

### Summary

- `NonEmptyList` is __used to model lists that guarantee at least one element__
- We can easily construct values of `NonEmptyList` with `NonEmptyList.of`
- `foldLeft`, `map`, `flatMap` and others are used to compute over the internal contents of a `NonEmptyList` value.
- `NonEmptyList.monad().binding { ... } Comprehensions` can be __used to imperatively compute__ over multiple `NonEmptyList` values in sequence.
- `NonEmptyList.applicative().map { ... }` can be used to compute over multiple `NonEmptyList` values preserving type information and __abstracting over arity__ with `map`
