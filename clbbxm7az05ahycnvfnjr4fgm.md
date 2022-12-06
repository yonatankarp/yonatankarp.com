# Kotlin Code Smell 006 - Boolean Variables

*Using boolean variables as flags exposes accidental implementation and pollute the code with Ifs.*

> ***TL;DR:*** *Don't use boolean variables, they force you to write Ifs. Create polymorphic states instead.*

# Problems

*   Extensibility
    
*   Comparison in some languages
    

# Solutions

*   If Boolean maps to a real-world entity are safe. Otherwise, model as a State to favor Extensibility. This also follows the [**Open/Closed Principle**](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle).
    

# Examples

*   Flags
    

# Exceptions

*   Real-world true/false rules
    

# Sample Code

### wrong

```kotlin
fun processBatch(
    useLogin: Boolean,
    deleteEntries: Boolean,
    beforeToday: Boolean
) {
    ...
}
```

### Right

```kotlin
fun processBatch(
    useLogin: LoginStrategy,
    deleteEntries: DeletePolicy,
    beforeToday: OffsetDateTime
) {
    ...
}
```

# Conclusion

Take extra care when declaring something as a *boolean*. Flags are difficult to maintain and extend. Learn more about the domain. Try migrating to the [**state design pattern**](https://en.wikipedia.org/wiki/State_pattern). Use polymorphism instead of ifs or when (pattern matching).

# More Info

*   [FlagArgument](https://martinfowler.com/bliki/FlagArgument.html) by Martin Fowler
    

# Credits

*   [Code Smell 07 - Boolean Variables](https://maximilianocontieri.com/code-smell-07-boolean-variables) by @[Maxi Contieri](@mcsee)