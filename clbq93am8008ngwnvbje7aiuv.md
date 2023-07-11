---
title: "Kotlin Code Smell 11 - God Objects"
seoTitle: "Avoid God Objects: Use the Single Responsibility Principle"
seoDescription: "Learn how to avoid God Objects in object-oriented programming by following the Single Responsibility Principle. Improve code cohesion and maintainability."
datePublished: Fri Dec 16 2022 08:30:42 GMT+0000 (Coordinated Universal Time)
cuid: clbq93am8008ngwnvbje7aiuv
slug: kotlin-code-smell-11-god-objects
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670063425483/sM3lSX7i5.jpeg
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

---

> **TL;DR:** Avoid taking on excessive responsibilities. Use the single responsibility principle.

## Problems

* Lack of cohesion
    
* Tight coupling
    

## Solutions

* Split responsibilities.
    
* Follow the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle).
    
* Follow [**The Boy Scout Rule.**](https://en.wikipedia.org/wiki/Single-responsibility_principle)
    

## Examples

* Libraries
    

## Exceptions

* [**Facades**](https://en.wikipedia.org/wiki/Facade_pattern)
    

## Sample Code

### Wrong

```kotlin
class Soldier {
    fun run() {}
    fun fight() {}
    fun driveGeneral() {}
    fun clean() {}
    fun fire() {}
    fun bePromoted() {}
    fun serialize() {}
    fun display() {}
    fun persistOnDatabase() {}
    fun toXML() {}
    fun jsonDecode() {}
    //...     
}
```

### Right

```kotlin
class Soldier {
    fun run() {}
    fun fight() {}
    fun clean() {}
}
```

## Conclusion

In Object-Oriented Programming, we distribute responsibilities among multiple objects.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## More info

* [God object - Wikipedia](https://en.wikipedia.org/wiki/God_object)
    
* [The S.O.L.I.D principles - Wikipedia](https://en.wikipedia.org/wiki/SOLID)
    
* [Refactoring Guru](https://refactoring.guru/es/smells/large-class)
    
* [Coupling](https://maximilianocontieri.com/coupling-the-one-and-only-software-design-problem)
    

## Credits

* [Code Smell 14 - God Objects](https://maximilianocontieri.com/code-smell-14-god-objects) by @[Maxi Contieri](@mcsee)