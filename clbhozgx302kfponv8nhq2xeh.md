# Kotlin Code Smell 007 - Long Chains Of Collaborations

> **TL;DR**: *Making long chains generate coupling and ripple effect. Any chain change breaks the code.*

# Problems

*   Coupling
    
*   Break encapsulation
    

# Solutions

*   Create intermediate methods.
    
*   Think about the [**Law of Demeter**](https://en.wikipedia.org/wiki/Law_of_Demeter).
    
*   Create higher-level messages.
    

# Sample Code

### Wrong

```kotlin
class Dog(val feet: Array<Foot>) {
}

class Foot {
    fun move(): Unit = TODO()
}

fun main() {
    val feet = arrayOf(Foot(), Foot(), Foot(), Foot())
    val dog = Dog(feet)

    for(foot in dog.feet) {
        foot.move()
    }
}
```

### **Right**

```kotlin
// We're copying the reference of the array, so in theory it can still
// be changed from outside, which is yet another code smell. A better
// approach would be to create a copy of the array inside the class
// instead of holding the reference.
class Dog(private val feet: Array<Foot>) {
    fun walk() {
        for(foot in feet) {
            foot.move()
        }
    }
}

class Foot {
    fun move(): Unit = TODO()
}

fun main() {
    val feet = arrayOf(Foot(), Foot(), Foot(), Foot())
    val dog = Dog(feet)
    dog.walk()
}
```

# Conclusion

Avoid successive message calls. Try to hide the intermediate collaborations and create new protocols. That way, not only that you protect your code from breaking in the future, but also maintain good encapsulation of your class.

# More Info

*   [**Refactoring Guru**](https://refactoring.guru/es/smells/message-chains)
    

# Credits

*   [Code Smell 08 - Long Chains Of Collaborations](https://maximilianocontieri.com/code-smell-08-long-chains-of-collaborations) by @[Maxi Contieri](@mcsee)