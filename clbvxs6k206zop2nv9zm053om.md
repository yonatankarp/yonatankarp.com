# Kotlin Code Smells 014 - Anonymous Functions Abusers

> ***TL;DR:*** Don't abuse closures and functions. Encapsulate them into objects.

# **Problems**

*   Maintainability
    

*   Testability
    
*   Code Reuse
    
*   Implementation Hiding
    
*   Debugging
    

# Solutions

*   Wrap functions/closures
    
*   Reify algorithms in a [method object](https://maximilianocontieri.com/refactoring-010-extract-method-object) / Strategy pattern
    

# Sample Code

### Wrong

```kotlin
fun sortFunction(
    list: MutableList<Int>,
    fn: (Int, Int) -> Boolean
) {
    for (i in list.indices) {
        for (j in 0 until list.size - i - 1) {
            if (fn(list[j], list[j + 1])) {
                val temp = list[j]
                list[j] = list[j + 1]
                list[j + 1] = temp
            }
        }
    }
}

fun main() {
    val scores = mutableListOf(9, 5, 2, 7, 23, 1, 3)
    sortFunction(scores) { a, b -> a > b }
}
```

### Right

```kotlin
class ElementComparator {
    fun greaterThen(firstElement: Int, secondElement: Int) =
        firstElement > secondElement

    // This is just an example. With more complex objects this
    // comparison might not so be trivial...
}

class BubbleSortStrategy(
    private val elements: MutableList<Int>,
    private val comparator: ElementComparator
) {
    // We have a strategy, we can't unit test it, change for a
    // polymorphic, Swap and benchmark algorithms, etc.
    fun sort() {
        for (i in elements.indices) {
            for (j in 0 until elements.size - i - 1) {
                if (comparator.greaterThen(elements[j], elements[j + 1])) {
                    swap(j)
                }
            }
        }
    }

    private fun swap(index: Int) {
        val temp = elements[index]
        elements[index] = elements[index + 1]
        elements[index + 1] = temp
    }
}

fun main() {
    val scores = mutableListOf(9, 5, 2, 7, 23, 1, 3)
    BubbleSortStrategy(scores, ElementComparator()).sort()
}
```

# Conclusion

Humans read code, Software works ok with anonymous functions, but maintainability is compromised when multiple closures are invoked. Moreover, extracting functionality to an object allows us to reuse this code.

# Credits

*   [Code Smell 21 - Anonymous Functions](https://maximilianocontieri.com/code-smell-21-anonymous-functions-abusers) Abusers by @[Maxi Contieri](@mcsee)