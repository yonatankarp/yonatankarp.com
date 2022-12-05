# Kotlin Code Smell 005 - Too Clever For Your own Good

> ***TL;DR:*** *Don't pretend you are too smart. Clean code asks for readability and simplicity.*

# Problems

*   Readability
    
*   Maintainability
    
*   Code-quality
    
*   Premature optimization
    

# Solutions

*   Refactor the code
    
*   Use [**better names**](https://maximilianocontieri.com/what-exactly-is-a-name-part-i-the-quest)
    

# Examples

*   Optimized loops
    

# Exceptions

*   Optimized code for low-level operations.
    
*   1-liner code functions
    

# Sample Code

### **Wrong**

```kotlin
fun primeFactors(n: Int): ArrayList<Int> {
    val f = ArrayList<Int>(); var d = 2; var nn = n
    while (nn >= 2) {
        if (nn % d == 0) {
            f.add(d)
            nn /= d
        } else {
            d++
        }
    }

    return f
}
```

### Right

```kotlin
fun primeFactors(numberToFactor: Int): ArrayList<Int> {
    val factors = ArrayList<Int>()
    var divisor = 2
    var remainder = numberToFactor

    while(remainder >= 2) {
        if(remainder % divisor == 0) {
            factors.add(divisor)
            remainder /= divisor
        }
        else{
            divisor++
        }
    }
    return factors
}
```

# Conclusion

Developers who are too clever for their own good write cryptic code to brag. Smart developers write clean code. Clear beats clever!

# **Credits**

*   [Code Smell 06 - Too Clever Programmer](https://maximilianocontieri.com/code-smell-06-too-clever-programmer) by @[Maxi Contieri](@mcsee)