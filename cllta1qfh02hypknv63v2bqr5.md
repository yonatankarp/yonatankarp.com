---
title: "Kotlin Code Smell 13 - Companion Object Functions"
seoTitle: "Kotlin Code Smell 13: Companion Object Functions"
seoDescription: "Discover the issues caused by companion object methods in Kotlin, their impact on coupling and testability, and recommended solutions with code examples."
datePublished: Sun Aug 27 2023 09:57:51 GMT+0000 (Coordinated Universal Time)
cuid: cllta1qfh02hypknv63v2bqr5
slug: kotlin-code-smell-13-companion-object-functions
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670070461814/oAFXoWZxX.jpeg
tags: programming-blogs, testing, kotlin, clean-code, code-smell-1

---


> **TL;DR:** Companion object functions are globally available and cannot be replaced for testing.

## Problems

* Coupling
    
* Testability
    
* Protocol Overloading
    
* Cohesion
    

## Solutions

* Honor the Single Responsibility Principle by creating an instance for a class whenever possible.
    
* Delegate the method to the instance, if feasible.
    
* Create stateless objects instead of referring to them as "helpers."
    

## Examples

* Static class initializers
    
* Static class methods
    
* Static attributes
    

## Sample Code

### Wrong

```kotlin
class DateStringHelper {
    companion object {
        private val formatter = 
            DateTimeFormatter.ofPattern("yyyy-MM-dd")

        fun format(date: OffsetDateTime): String  =
            formatter.format(date)
    }
}

fun main() {
    print(DateStringHelper.format(OffsetDateTime.now()))
}
```

### Right

```kotlin
class DateToStringFormatter(private val date: OffsetDateTime) {

    fun englishFormat(): String {
        val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd")
        return formatter.format(date)
    }
}

fun main() {
    print(DateToStringFormatter(OffsetDateTime.now()).englishFormat())
}
```

## Conclusion

Using companion object methods pollutes the class protocol with "library methods," which breaks cohesion and generates coupling. It is advisable to extract them through refactorings.

We cannot manipulate companion classes and use them polymorphically, so we can't mock or test them effectively.

As a result, we end up with a globally accessible reference that is challenging to decouple.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## More info

* [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle)
    
* [Mocking in tests with mockk](https://mockk.io/)
    

## Credits

* [Code Smell 18 - Static Functions](https://maximilianocontieri.com/code-smell-18-static-functions) by @[Maxi Contieri](@mcsee)