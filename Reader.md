Reader Monad can be used to read from the "Environment", i.e. doing dependency injection.

Reader.local() allows a local transformation function to be executed on the input (environment) before it is passed to the reader. It can be used for things like configuration overrides, test mock setup, and environment narrowing/widening/composition.


