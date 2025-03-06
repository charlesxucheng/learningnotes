# Single-responsibility Principle
> A class should have one and only one reason to change, meaning that a class should have only one job.

The definition is loose. One reason can be big or small. Who can decide what the "single responsibility" should be? We can apply Domain-Driven Design and distil the requirements to define the responsibility (the reason) a class should have. i.e. Map the responsibilities to domain concepts.

# Open-closed Principle
>Objects or entities should be open for extension but closed for modification.

This means that a class should be extendable without modifying the class itself. This relates to SRP - it is not desirable to modify a class except when the change is related to the responsibility of the class. Reducing unnecessary changes means lower coupling and improves code maintainability.

There are times that a class should not be extensible. Examples:
1. Immutable Data Structures
2. Enforcing Invariants
3. Designing for composition over inheritance
4. Domain modelling for sealed hierarchies
5. Library or framework design
6. Security or performance considerations

In essence, **OCP should be violated when the cost of openness (e.g., complexity, fragility, or risk) outweighs the benefits of extensibility**.

# Liskov Substitution Principle
> Let q(x) be a property provable about objects of x of type T. Then q(y) should be provable for objects y of type S where S is a subtype of T.

This means that every subclass or derived class should be substitutable for their base or parent class. 

Examples of violations of Liskov Substitution Principle:
- Narrowing of the behaviour of overridden methods
- More strict preconditions or less strict post-conditions
- Throwing new exceptions that does not appear in the super class.
- Ignoring the super class's invariants

#### How does Liskov Substitution Principle work with [[Variance]] in programming languages?
- **Covariance** aligns with LSP by preserving subtyping relationships (e.g., `List<Dog>` can substitute `List<Animal>`).
    
- **Contravariance** aligns with LSP in function input types (e.g., a function accepting `Animal` can substitute a function accepting `Dog`).
    
- **Invariance** can conflict with LSP but is often necessary for type safety (e.g., preventing adding a `Cat` to a `List<Dog>`).
    
- **Variance annotations** (e.g., `in/out` in Kotlin or `+/-` in Scala) allow developers to design types that align with LSP while maintaining type safety.

# Interface Segregation Principle
> A client should never be forced to implement an interface that it doesn’t use, or clients shouldn’t be forced to depend on methods they do not use.

The ISP is also tied to DDD because DDD is about appropriate domain modelling so the right behaviours are implemented in the right places. 

If we take ISP to its most granular level, we have just functions.

# Dependency Inversion Principle
> Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

The DIP allows swapping of different implementation details. High level modules should depend on interfaces (abstractions) not implementations. Use ISP to define the appropriate interfaces.

DIP is related to [[Architecture Boundaries]].
