---
title: "Kotlin Code Smells 19 - Setters"
seoTitle: "Avoid Mutability & Anemic Models: Solutions for Junior Programmers"
seoDescription: "Learn why mutability and anemic models are bad practices for junior programmers. Explore effective solutions to improve code quality and maintainability."
datePublished: Tue Jul 18 2023 08:30:09 GMT+0000 (Coordinated Universal Time)
cuid: clk81avhh000309mrcfyih9ya
slug: kotlin-code-smells-19-setters
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1671279616694/LhFnczXAn.jpeg
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

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

---

Stay updated with my latest thoughts and ideas by registering for my [newsletter](https://yonatankarp.com/newsletter). Connect with me on [LinkedIn](https://www.linkedin.com/in/yonatankarp/) or [Twitter](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 28](https://maximilianocontieri.com/the-one-and-only-software-design-principle) - Setters by @[Maxi Contieri⭐⭐⭐](@mcsee)