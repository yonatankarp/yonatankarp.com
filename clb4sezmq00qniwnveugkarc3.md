# Kotlin Code Smell 002 - Functions Are Too Long

> ***TL;DR:*** *Refactor and extract functions longer than 5-10 lines of code.*

# Problems

*   Low Cohesion
    
*   High coupling
    
*   Difficult to read
    
*   Low Reuse
    

# Solutions

*   [Refactor](https://maximilianocontieri.com/refactoring-010-extract-method-object)**.**
    
*   Create small objects dealing with some tasks, and unit-test them.
    
*   Compose methods.
    

# Examples

*   Libraries
    

# Sample Code

### **Wrong**

```kotlin
class ChessBoard() {
    init {
        placeOnBoard(whiteTower)
        placeOnBoard(whiteKnight)
        // All other write pieces
        
        // Empty space to pause definition
        placeOnBoard(blackTower)
        placeOnBoard(blackKnight)
        // All other black pieces
    }

    fun placeOnBoard(piece: Piece) = TODO()
}
```

### Right

```kotlin
class ChessBoard() {
    init {
        placeWhitePieces()
        placeBlackPieces()
    }
    
    private fun placeWhitePieces() = TODO()
    private fun placeBlackPieces() = TODO()
}
```

# Conclusion

Extract the long method into smaller pieces. Break complex algorithms into parts. This will also allow you to unit-test these parts more easily. Moreover, it improves readability and allows us to focus on the correct level of abstraction in your method.

# More info

*   [Refactoring Guru](https://refactoring.guru/es/smells/long-method)
    

# Credits

*   [Code Smell 03 - Functions Are Too Long](https://maximilianocontieri.com/code-smell-03-functions-are-too-long) by @[Maxi Contieri](@mcsee)