---
title: "Kotlin Code Smell 11 - God Objects"
subtitle: ""
slug: kotlin-code-smell-11-god-objects
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670063425483/sM3lSX7i5.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Avoid God Objects: Use the Single Responsibility Principle"
seoDescription: "Learn how to avoid God Objects in object-oriented programming by following the Single Responsibility Principle. Improve code cohesion and maintainability."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
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

* [Code Smell 14 - God Objects](https://maximilianocontieri.com/code-smell-14-god-objects) by @[Maxi Contieri⭐⭐⭐](@mcsee)
