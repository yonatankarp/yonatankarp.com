---
title: "Kotlin Code Smell 9 - Subclassification for Code Reuse"
seoTitle: "Kotlin Code Smell 9 - Subclassification for Code Reuse"
seoDescription: "Learn why favoring composition over inheritance is crucial for code reuse. Refactor legacy systems for maintainability and reduced coupling."
datePublished: Tue Dec 13 2022 08:30:44 GMT+0000 (Coordinated Universal Time)
cuid: clblyrsi003h7rwnv8fwr6063
slug: kotlin-code-smell-9-subclassification-for-code-reuse
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670058516294/akzymQD_J.jpeg
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

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

## Conclusion

In legacy systems, it is common to have deep hierarchies and method overriding. However, it is important to refactor them and subclass them for essential reasons rather than implementation reasons.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## More info

* [Liskov Substitution](https://en.wikipedia.org/wiki/Liskov_substitution_principle)
    
* [Inheritance (IS-A) vs. Composition (HAS-A) Relationship](https://www.w3resource.com/java-tutorial/inheritance-composition-relationship.php)
    

## Credits

* [Code Smell 11 - Subclassification for Code Reuse](https://maximilianocontieri.com/code-smell-11-subclassification-for-code-reuse) by @[Maxi Contieri](@mcsee)