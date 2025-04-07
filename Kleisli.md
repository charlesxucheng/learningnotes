Kleisli is a great tool to compose functions.

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

- **Reader monad pattern** - Kleisli is especially useful for the [[Reader Monad Pattern]], which allows you to thread a common environment or context through a computation:
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