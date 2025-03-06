
Variance is how subtyping between more complex types relates to subtyping between their components. 

[Wikipedia](https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science))

Generally, **read-only** data types **(sources)** can be **covariant**; **write-only** data types **(sinks)** can be **contravariant**.

The Variance of a C# generic interface is declared by placing the **out (covariant)** or **in (contravariant)** attribute on (zero or more of) its type parameters.

