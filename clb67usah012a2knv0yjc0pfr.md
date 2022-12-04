# Kotlin Code Smell 003 - String Abusers

> ***TL;DR:*** *Use real abstractions and real objects instead of string accidental manipulation.*

# **Problems**

*   Complexity
    
*   Readability
    
*   Maintainability
    
*   Lack of Abstractions
    

# **Solutions**

*   Work with objects instead of strings.
    
*   Replace strings with data structures dealing with object relations.
    
*   Find [**Bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) problems between real objects and the strings.
    

# **Examples**

*   Serializers
    
*   Parsers
    

# **Sample Code**

### **Wrong**

```kotlin
val schoolDescription = "College of Springfield"

// location = "Springfield"
val location = """[^ ]*\$""".toRegex()
    .find(schoolDescription)?.value

// school = "College"
val school = """^[\w]+""".toRegex()
    .find(schoolDescription)?.value
```

### Right

```kotlin
class School(
    private val name: String,
    private val location: Location
) {
    fun description() = "$name of ${location.name}"
}

class Location(
    val name: String
)
```

# **Conclusion**

Don't abuse strings. Favor real objects. Find absent protocol to distinguish them from strings.

# **Credits**

*   [Code Smell 04 - String Abusers](https://maximilianocontieri.com/code-smell-04-string-abusers) by @[Maxi Contieri](@mcsee)