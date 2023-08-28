---
title: "Kotlin Code Smell 13 - Companion Object Functions"
subtitle: "Yet another global access coupled with laziness that cannot be mocked."
slug: kotlin-code-smell-13-companion-object-functions
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, testing, kotlin, clean-code, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670070461814/oAFXoWZxX.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Code Smell 13: Companion Object Functions"
seoDescription: "Discover the issues caused by companion object methods in Kotlin, their impact on coupling and testability, and recommended solutions with code examples."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
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

%%[contact-me]

## Conclusion

Using companion object methods pollutes the class protocol with "library methods," which breaks cohesion and generates coupling. It is advisable to extract them through refactorings.

We cannot manipulate companion classes and use them polymorphically, so we can't mock or test them effectively.

As a result, we end up with a globally accessible reference that is challenging to decouple.

## More info

* [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle)
    
* [Mocking in tests with mockk](https://mockk.io/)
    

## Credits

* [Code Smell 18 - Static Functions](https://maximilianocontieri.com/code-smell-18-static-functions) by @[Maximiliano Contieri](@mcsee)
