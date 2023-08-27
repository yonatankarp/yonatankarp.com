---
title: "Kotlin Code Smell 7 - Long Chains Of Collaborations"
subtitle: "Decoupling Collaborations: Ensuring Code Stability through Reduced Chains and Better Encapsulation"
slug: kotlin-code-smell-7-long-chains-of-collaborations
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670056097460/SWqMSlqG9.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Avoid Code Smell: Long Chains of Collaborations"
seoDescription: "Learn how to prevent code coupling and encapsulation issues by eliminating long chains of collaborations in your Kotlin code."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR**: Long chains of collaborations generate coupling and ripple effect, where any change in the chain breaks the code.

## Problems

* Coupling
    
* Break encapsulation
    

## Solutions

* Create intermediate methods.
    
* Consider the [**Law of Demeter**](https://en.wikipedia.org/wiki/Law_of_Demeter).
    
* Create higher-level messages.
    

## Sample Code

### Wrong

```kotlin
class Dog(val feet: Array<Foot>) {
}

class Foot {
    fun move(): Unit = TODO()
}

fun main() {
    val feet = arrayOf(Foot(), Foot(), Foot(), Foot())
    val dog = Dog(feet)

    for(foot in dog.feet) {
        foot.move()
    }
}
```

### Right

```kotlin
// We're copying the reference of the array, so in theory, it can still
// be changed from outside, which is yet another code smell. A better
// approach would be to create a copy of the array inside the class
// instead of holding the reference.
class Dog(private val feet: Array<Foot>) {
    fun walk() {
        for(foot in feet) {
            foot.move()
        }
    }
}

class Foot {
    fun move(): Unit = TODO()
}

fun main() {
    val feet = arrayOf(Foot(), Foot(), Foot(), Foot())
    val dog = Dog(feet)
    dog.walk()
}
```

## Conclusion

Avoid successive message calls. Try to hide the intermediate collaborations and create new protocols. This way, not only will you protect your code from breaking in the future, but you will also maintain good encapsulation of your class.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## More Info

* [**Refactoring Guru**](https://refactoring.guru/es/smells/message-chains)
    

## Credits

* [Code Smell 08 - Long Chains Of Collaborations](https://maximilianocontieri.com/code-smell-08-long-chains-of-collaborations) by @[Maxi Contieri⭐⭐⭐](@mcsee)
