# Kotlin Code Smell 012 - Ripple Effect

> **TL;DR:** *If small changes have a big impact, you need to decouple your system.*

# Problems

*   Coupling
    

# Solutions

*   Decouple.
    
*   Cover with tests.
    
*   Refactor and isolate what is changing.
    
*   Depend on interfaces.
    

# Examples

*   Legacy Systems
    

# Sample Code

### Wrong

```kotlin
class Time(
    private val hour: Int,
    private val minute: Int,
    private val seconds: Int
) {
    fun now() {
        // call operating system  
    } 
}

// Adding a TimeZone will have a big Ripple Effect
// Changing now() to consider timezone will also bring the effect
```

### Right

```kotlin
// Removed now() since is invalid without context
data class Time(
    private val hour: Int,
    private val minute: Int,
    private val seconds: Int,
    private val timezone: String
)

class RelativeClock(private val timezone: String) {
    fun now() {
        val localSystemTime = this.localSystemTime()
        val localSystemTimezone = this.localSystemTimezone()
        
        // Make some math translating timezones...
        
        return Time(..., timezone)
    }
    
    // ...
}
```

# Conclusion

There are multiple strategies to deal with legacy and coupled systems. We should deal with this problem before it explodes in our hands.

# More info

*   [**How to Decouple a Legacy System**](https://maximilianocontieri.com/how-to-decouple-a-legacy-system)
    

# Credits

*   [Code Smell 16 - Ripple Effect](https://maximilianocontieri.com/code-smell-16-ripple-effect) by @[Maxi Contieri](@mcsee)