# Kotlin Smell 013 - Companion Object Functions

> ***TL;DR:*** The companion object functions are globally available, and cannot be replaced for testing.

# Problems

*   Coupling
    
*   Testability
    
*   Protocol Overloading
    
*   Cohesion
    

# Solutions

*   A class [**Single Responsibility Principle**](https://en.wikipedia.org/wiki/Single-responsibility_principle) is to create an instance. Honor it when possible.
    
*   Delegate the method to the instance if possible.
    
*   Create stateless objects. Don't call them **helpers**.
    

# Examples

*   Static class initializers
    
*   Static class methods
    
*   Static attributes
    

# Sample Code

### Wrong

```kotlin
class DateStringHelper {
    companion object {
        private val formatter = 
            DateTimeFormatter.ofPattern("yyyy-MM-dd")

        fun format(date: OffsetDateTime): String  =
            formatter.format(date)
    }
}

fun main() {
    print(DateStringHelper.format(OffsetDateTime.now()))
}
```

### Right

```kotlin
class DateToStringFormatter(private val date: OffsetDateTime) {

    fun englishFormat(): String {
        val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd")
        return formatter.format(date)
    }
}

fun main() {
    print(DateToStringFormatter(OffsetDateTime.now()).englishFormat())
}
```

# Conclusion

Using the companion object method pollutes the class protocol with "library methods", which breaks cohesion and generates coupling. We should extract them with refactorings.

We cannot manipulate the companion classes and use them polymorphically, so we can't mock them or plug them into our tests.

Therefore, we have a global reference too difficult to decouple.

# More info

*   [**Single Responsibility Principle**](https://en.wikipedia.org/wiki/Single-responsibility_principle)
    
*   [Mocking in tests with mockk](https://mockk.io/)
    

# Credits

*   [Code Smell 18 - Static Functions](https://maximilianocontieri.com/code-smell-18-static-functions) by @[Maxi Contieri](@mcsee)