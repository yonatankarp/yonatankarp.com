---
title: "Kotlin Code Smell 19 - Setters"
subtitle: "Code Missteps: When Objects Play Hard to Set"
slug: kotlin-code-smell-19-setters
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1671279616694/LhFnczXAn.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Avoid Mutability & Anemic Models: Solutions for Junior Programmers"
seoDescription: "Learn why mutability and anemic models are bad practices for junior programmers. Explore effective solutions to improve code quality and maintainability."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

The first exercise that junior programmers often do involves IDEs, tutorials, and senior developers who teach them an anti-pattern.

## Problems

* Mutability
    
* Information Hiding
    
* Anemic Models
    
* Fail Fast
    
* Integrity
    
* Duplicated Code
    
* Concurrent programming execution
    

## Solutions

* Avoid Setters
    
* Set essential attributes on object construction.
    

## Sample Code

### Wrong

```kotlin
// Anemic mutable class
data class PhoneCall(
    var origin: String? = null,
    var destination: String? = null,
    var duration: Long? = null
)

fun main() {
    val janePhoneCall = PhoneCall()
    janePhoneCall.origin = "555-5555"
    janePhoneCall.destination = "444-4444"
    janePhoneCall.duration = 60
}
```

Mutation brings lots of problems to your code.

```kotlin
fun main() {
    // Since we have a setter, we can create invalid combinations.
    // For example:
    // - We can't exchange the call destination during the call,
    // However, this is not enforced due to the setters' usage.
    val janePhoneCall = PhoneCall()
    janePhoneCall.origin = "555-5555"
    janePhoneCall.destination = "555-5555"
    janePhoneCall.duration = 60
}

// Origin and Destination cannot be the same
// To validate this, we're repeating the same code twice
class PhoneCall(
    origin: String? = null,
    destination: String? = null,
    duration: Long? = null
) {
    var origin: String? = origin
        set(value) {
            if (value == origin)
                throw IllegalArgumentException("Destination cannot be the same as origin")
            field = value
        }

    var destination: String? = destination
        set(value) {
            if (value == destination)
                throw IllegalArgumentException("Destination cannot be the same as origin")
            field = value
        }

    // duration is exposed in seconds as a ripple effect
    // this violates information hiding principle and prevents
    // us from changing its representation
    var duration: Long? = duration
        set(durationInSeconds) {
            field = durationInSeconds
        }
}

// Moreover, multiple threads (or coroutines) can change the same
// object. What is the right state of the object at the end?
```

### Right

```kotlin
class PhoneCall(
    val origin: String,
    val destination: String,
    val duration: Long
) {
    // Single control point.
    // We only create valid phone calls, and they remain valid
    // since they cannot mutate.
    init {
        if (origin == destination)
            throw IllegalArgumentException("Destination cannot be the same as origin")
    }

    // We're explicit about which measure of unit is used
    fun durationInSeconds() = duration
    fun durationInMilliSeconds() = duration * MILLISECONDS_IN_SECOND
    
    companion object {
        private const val MILLISECONDS_IN_SECOND = 1000
    }
}
```

## Examples

* DTOs
    

## Exceptions

* Setting attributes is safe for non-essential attributes. However, it has all the drawbacks and considerations already mentioned.
    

## Conclusion

Creating incomplete and anemic objects is a very bad practice that violates mutability, the fail fast principle, and real-world [bijections](https://maximilianocontieri.com/the-one-and-only-software-design-principle).

%%[contact-me]

## Credits

* [Code Smell 28](https://maximilianocontieri.com/the-one-and-only-software-design-principle) - Setters by @[Maximiliano Contieri](@mcsee)
