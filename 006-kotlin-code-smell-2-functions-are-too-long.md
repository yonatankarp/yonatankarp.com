---
title: "Kotlin Code Smell 2 - Functions Are Too Long"
subtitle: ""
slug: kotlin-code-smell-2-functions-are-too-long
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: functions, kotlin, clean-code, kotlin-beginner, programming-tips
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/PRwiDHKJWRo/upload/v1669832611031/6RAbcDEZ6.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Refactor Long Functions in Kotlin Code"
seoDescription: "Learn how to refactor and extract functions longer than 5-10 lines of code to improve code quality and maintainability in Kotlin."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** Refactor and extract functions longer than 5-10 lines of code.

## Problems

* Low cohesion
    
* High coupling
    
* Difficult to read
    
* Low reuse
    

## Solutions

* [Refactor](https://maximilianocontieri.com/refactoring-010-extract-method-object).
    
* Create small objects to handle specific tasks and unit test them.
    
* Compose methods.
    

## Examples

* Libraries
    

## Sample Code

### **Wrong**

```kotlin
class ChessBoard() {
    init {
        placeOnBoard(whiteTower)
        placeOnBoard(whiteKnight)
        // All other white pieces
        
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

## Conclusion

Extract long methods into smaller pieces. Break down complex algorithms into parts. This approach allows for easier unit testing and improves readability while ensuring the correct level of abstraction in your method.

---

Stay updated with my latest thoughts and ideas by registering for my [newsletter](https://yonatankarp.com/newsletter). Connect with me on [LinkedIn](https://www.linkedin.com/in/yonatankarp/) or [Twitter](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## More info

* [Refactoring Guru](https://refactoring.guru/es/smells/long-method)
    

## Credits

* [Code Smell 03 - Functions Are Too Long](https://maximilianocontieri.com/code-smell-03-functions-are-too-long) by @[Maxi Contieri⭐⭐⭐](@mcsee)
