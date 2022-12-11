# Kotlin Code Smell 008 - Too Many Arguments

> ***TL;DR:*** *Don't pass more than three arguments to your functions.*

# Problems

*   Low maintainability
    
*   Low Reuse
    
*   Coupling
    
*   Readability
    

# Solutions

*   Find cohesive relations among arguments
    
*   Create a "context".
    
*   Consider using a [**Method Object**](https://wiki.c2.com/?MethodObject) Pattern.
    
*   Avoid "basic" Types: strings, arrays, integers, etc, and think in terms of objects.
    

# Exceptions

*   Operations in the real-world needing no cohesive collaborators.
    

# Sample Code

### Wrong

```kotlin
class Printer {
    fun print(
        documentToPrint: String,
        paperSize: String,
        orientation: String,
        grayScales: Boolean,
        pageFrom: Int,
        pageTo: Int,
        copies: Int,
        marginLeft: Float,
        marginRight: Float,
        marginTop: Float,
        marginBottom: Float
    ): Unit = TODO()
}
```

### Right

```kotlin
class PaperSize {
    //...
}

class Document {
    //...
}

class PrintMargins {
    //...
}

class PrintRange {
    //...
}

class ColorConfiguration {
    //...
}

class PrintOrientation {
    //...
}

class PrintSetup(
    paperSize: PaperSize,
    orientation: PrintOrientation,
    color: ColorConfiguration,
    range: PrintRange,
    copiesCount: Int,
    margins: PrintMargins
)

class Printer {
    fun print(
        documentToPrint: Document,
        setup: PrintSetup
    ): Unit = TODO()
}
```

# Conclusion

Relate arguments and group them. Always favor real-world mappings. Find in the real world how to group the arguments into cohesive objects.

If a function gets too many arguments, some of them might be related to class construction. This is a design smell too.

# Credits

*   [Code Smell 10 - Too Many Arguments](https://maximilianocontieri.com/code-smell-10-too-many-arguments) by @[Maxi Contieri](@mcsee)