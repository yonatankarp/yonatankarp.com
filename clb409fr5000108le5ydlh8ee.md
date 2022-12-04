# Kotlin Code Smell 001 - Constants and Magic Numbers

> **TL;DR:** Avoid Magic numbers without explanation. We don't know their source and are afraid to change them.

# Problems

*   Coupling
    
*   Low testability
    
*   Low readability
    

# Solutions

*   Rename the constant with a semantic and name (meaningful and intention-revealing).
    
*   Replace constants with parameters, so you can mock them from the outside.
    
*   A constant definition is often a different object than the constant (ab)user.
    

# Examples

*   Algorithms Hyper Parameters
    

# Sample Code

## Wrong

```kotlin
fun energy(mass: Double) = (mass * 299792458).pow(2)
```

## Right

```kotlin
typealias MetersPerSeconds = Int

// We're using here the specific unit of measure to avoid yet another code smell
const val LIGHT_SPEED: MetersPerSeconds = 299792458

fun energy(mass: Double) = (mass * LIGHT_SPEED).pow(2)
```

# More Information

*   [Refactoring Guru](https://refactoring.guru/es/replace-magic-number-with-symbolic-constant)
    
*   [How to Decouple a Legacy System](https://maximilianocontieri.com/how-to-decouple-a-legacy-system)
    

# Credits

*   [Code Smell 02 - Constants and Magic Numbers](https://maximilianocontieri.com/code-smell-02-constants-and-magic-numbers) by @[Maxi Contieri](@mcsee)