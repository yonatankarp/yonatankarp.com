# Kotlin Code Smell 009 - Subclassification for Code Reuse

> ***TL;DR:*** *Always Favor composition over inheritance.*

# Problems

*   Coupling
    
*   Maintainability
    

# Solutions

*   Composition
    

# Exceptions

*   If the hierarchy follows the principle `behaves like` then it is safe.
    

# Sample Code

### Wrong

```kotlin
open class Rectangle(
    protected val length: Int,
    protected val width: Int
) {
    open fun area() = length * width
}

class Square(size: Int) : Rectangle(size, size) {
    override fun area() = length * length
}

class Box(size: Int) : Rectangle(size, size)
```

### Right

```kotlin
interface Shape {
    fun area(): Int
}

class Rectangle(
    private val length: Int,
    private val width: Int
) : Shape {
    override fun area() = length * width
}

class Square(private val size: Int) : Shape {
    override fun area() = size * size
}

class Box(size: Int) {
    private val shape: Square

    init {
        shape = Square(size)
    }

    fun area() = shape.area()
}
```

# Conclusion

In legacy systems is very common to have *Deep Hierarchies* and *method overriding*, we need to refactor them and subclass them by `essential` reasons and not `implementation` ones.

# More info

*   [**Liskov Substitution**](https://en.wikipedia.org/wiki/Liskov_substitution_principle)
    
*   [Inheritance (IS-A) vs. Composition (HAS-A) Relationship](https://www.w3resource.com/java-tutorial/inheritance-composition-relationship.php)
    

# Credits

*   [Code Smell 11 - Subclassification for Code Reuse](https://maximilianocontieri.com/code-smell-11-subclassification-for-code-reuse) by @[Maxi Contieri](@mcsee)