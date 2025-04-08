Reference: https://typelevel.org/cats/datatypes/kleisli.html

Kleisli is a great tool to compose functions.

At its core, `Kleisli[F[_], A, B]` is just a wrapper around the function `A => F[B]`. Depending on the properties of the `F[_]`, we can do different things with `Kleisli`s. For instance, if `F[_]` has a `FlatMap[F]` instance (we can call `flatMap` on `F[A]` values), we can compose two `Kleisli`s much like we can two functions.

Below are some more methods on `Kleisli` that can be used as long as the constraint on `F[_]` is satisfied:

| Method   | Constraint on `F[_]` |
| -------- | -------------------- |
| andThen  | FlatMap              |
| compose  | FlatMap              |
| flatMap  | FlatMap              |
| lower    | Monad                |
| map      | Functor              |
| traverse | Applicative          |

`Kleisli` in Cats also has a `lift` operation that takes a type parameter `G[_]` such that `G` has an `Applicative` instance and lifts a `Kleisli` value such that its output type is `G[F[B]]`. This allows us to then lift a `Reader[A, B]` into a `Kleisli[G, A, B]`. Note that lifting a `Reader[A, B]` into some `G[_]` is equivalent to having a `Kleisli[G, A, B]` since `Reader[A, B]` is just a type alias for `Kleisli[Id, A, B]`, and `type Id[A] = A` so `G[Id[A]]` is equivalent to `G[A]`.

## Benefits of using Kleisli over native methods in Scala type constructors (`F[_]`)
- **First-class function composition** - Kleisli gives you a composable object that represents your `A => F[B]` function. This lets you treat function composition as a value that can be passed around, stored, and manipulated.
- **Cleaner syntax for longer composition chains** - When you compose multiple functions that return monadic values, the Kleisli API can be more readable than nested flatMaps:
    
```scala
// Without Kleisli 
def processUser(id: UserId): IO[User] = ??? 
def validateUser(user: User): IO[ValidatedUser] = ??? 
def saveUser(user: ValidatedUser): IO[SavedUser] = ??? 

// Composition is right-to-left and gets nested 
val process: UserId => IO[SavedUser] = id =>    
	processUser(id).flatMap(user =>    
		validateUser(user).flatMap(validUser =>      
		saveUser(validUser))) 

// With Kleisli 
val processUserK = Kleisli(processUser) 
val validateUserK = Kleisli(validateUser) 
val saveUserK = Kleisli(saveUser) 

// Composition is left-to-right and flat 
val processK: Kleisli[IO, UserId, SavedUser] =    processUserK andThen validateUserK andThen saveUserK`
```

- **Reader monad pattern** - Kleisli is especially useful for the [[Reader]] pattern, which allows you to thread a common environment or context through a computation:
```scala
type DbReader[A] = Kleisli[IO, DbConnection, A]

val getUsers: DbReader[List[User]] = Kleisli { conn => 
  conn.query("SELECT * FROM users")
}

val getActiveUsers: DbReader[List[User]] = 
  getUsers.map(_.filter(_.isActive))
Kleisli Category: https://en.wikipedia.org/wiki/Kleisli_category```
```

Since Kleislis are just values, they can be used for dynamic composition based on run time values such as results of previous function execution.

## Monad Transformers

Many data types have a monad transformer equivalent that allows us to compose the `Monad` instance of the data type with any other `Monad` instance. For instance, `OptionT[F[_], A]` allows us to compose the monadic properties of `Option` with any other `F[_]`, such as a `List`. This allows us to work with nested contexts/effects in a nice way (for example, in for-comprehensions).

`Kleisli` can be viewed as **the monad transformer for functions**. Recall that at its essence, `Kleisli[F, A, B]` is just a function `A => F[B]`, with niceties to make working with the value we actually care about, the `B`, easy. `Kleisli` allows us to take the effects of functions and have them play nice with the effects of any other `F[_]`.

This may raise the question, what exactly is the "effect" of a function?

Well, if we take a look at any function, we can see it takes some input and produces some output with it, without having touched the input (assuming the function is pure, i.e. [referentially transparent](https://en.wikipedia.org/wiki/Referential_transparency_%28computer_science%29)). That is, we take a read-only value, and produce some value with it. For this reason, the type class instances for functions often refer to the function as a [[Reader]]. For instance, it is common to hear about the `Reader` monad. In the same spirit, Cats defines a `Reader` type alias along the lines of:

```scala
// We want A => B, but Kleisli provides A => F[B]. To make the types/shapes match,
// we need an F[_] such that providing it a type A is equivalent to A
// This can be thought of as the type-level equivalent of the identity function
type Id[A] = A

// Reader[A, B] is an alias of Kleisli[Id, A, B]
type Reader[A, B] = Kleisli[Id, A, B] 
object Reader {
  // Lifts a plain function A => B into a Kleisli, giving us access
  // to all the useful methods and type class instances
  def apply[A, B](f: A => B): Reader[A, B] = Kleisli[Id, A, B](f)
}

type ReaderT[F[_], A, B] = Kleisli[F, A, B]
val ReaderT = Kleisli
```

