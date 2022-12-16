# Kotlin Code Smell 011 - God Objects

> ***TL;DR:*** *Don't take too many responsibilities. use the single responsibility principle*

# Problems

*   Cohesion
    
*   Coupling
    

# Solutions

*   Split responsibilities.
    
*   Follow the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle).
    
*   Follow [**The Boy Scout Rule.**](https://en.wikipedia.org/wiki/Single-responsibility_principle)
    

# Examples

*   Libraries
    

# Exceptions

*   [**Facades**](https://en.wikipedia.org/wiki/Facade_pattern)
    

# Sample Code

### Wrong

```kotlin
class Soldier {
    fun run() {}
    fun fight() {}
    fun driveGeneral() {}
    fun clean() {}
    fun fire() {}
    fun bePromoted() {}
    fun serialize() {}
    fun display() {}
    fun persistOnDatabase() {}
    fun toXML() {}
    fun jsonDecode() {}
    //...     
}
```

### Right

```kotlin
class Soldier {
    fun run() {}
    fun fight() {}
    fun clean() {}
}
```

# Conclusion

In Object-Oriented Programming, we will distribute responsibilities among many objects.

# More info

*   [God object - Wikipedia](https://en.wikipedia.org/wiki/God_object)
    
*   [The S.O.L.I.D principles - Wikipedia](https://en.wikipedia.org/wiki/SOLID)
    
*   [Refactoring Guru](https://refactoring.guru/es/smells/large-class)
    
*   [Coupling](https://maximilianocontieri.com/coupling-the-one-and-only-software-design-problem)
    

# Credits

*   [Code Smell 14 - God Objects](https://maximilianocontieri.com/code-smell-14-god-objects) by @[Maxi Contieri](@mcsee)