---
title: "Kotlin Code Smell 9 - Subclassification for Code Reuse"
subtitle: "Crafting Flexible Code: The Power of Composition Over Inheritance"
slug: kotlin-code-smell-9-subclassification-for-code-reuse
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670058516294/akzymQD_J.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Code Smell 9 - Subclassification for Code Reuse"
seoDescription: "Learn why favoring composition over inheritance is crucial for code reuse. Refactor legacy systems for maintainability and reduced coupling."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** Always favor composition over inheritance.

## Problems

* Coupling
    
* Maintainability
    

## Solutions

* Composition
    

## Exceptions

* If the hierarchy follows the principle of "behaves like," then it is safe.
    

## Sample Code

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

%%[contact-me]

## Conclusion

In legacy systems, it is common to have deep hierarchies and method overriding. However, it is important to refactor them and subclass them for essential reasons rather than implementation reasons.

## More info

* [Liskov Substitution](https://en.wikipedia.org/wiki/Liskov_substitution_principle)
    
* [Inheritance (IS-A) vs. Composition (HAS-A) Relationship](https://www.w3resource.com/java-tutorial/inheritance-composition-relationship.php)
    

## Credits

* [Code Smell 11 - Subclassification for Code Reuse](https://maximilianocontieri.com/code-smell-11-subclassification-for-code-reuse) by @[Maximiliano Contieri](@mcsee)
